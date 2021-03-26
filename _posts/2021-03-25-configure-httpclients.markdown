---
layout: post
title:  "Alternative way to configure HttpClients"
date:   2021-03-25 18:11:23 +0100
categories: [asp.net-core, c#]
author: dnf
image: assets/images/http_client_options_main.png
featured: true
comments: true
---

Sometimes in complicated situations a standard way for configuring http clients in ASP.NET Core is not enough but there is alternative way.

# Introduction

When we have simple scenario we usually configure a http client this way

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("ClientName", configure =>
    {
        configure.BaseAddress = new Uri("http://testaddress.com");
    }).AddHttpMessageHandler<CustomHttpHandler>();
}
```

but when our app grows or we would like to prepare more generic way to configure our http clients this method lack flexibility. Fortunately there is a way to do it better and for some reasons this method is not widely known.

# HttpClientFactoryOptions

Asp.Net Core is using [Options pattern](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-5.0) in many places and this gives great opportunity to inject our custom code. One of this places is interface called **IConfigureNamedOptions**

```c#
//
// Summary:
//     Represents something that configures the TOptions type.
//
// Type parameters:
//   TOptions:
public interface IConfigureNamedOptions<in TOptions> : IConfigureOptions<TOptions> where TOptions : class
{
    //
    // Summary:
    //     Invoked to configure a TOptions instance.
    //
    // Parameters:
    //   name:
    //     The name of the options instance being configured.
    //
    //   options:
    //     The options instance to configure.
    void Configure(string name, TOptions options);
}
```

It is used to instrument ASP.NET Core to use our custom class when class of type **TOptions** is used inside **IOption<>** (or **IOptionsMonitor\<\>**). We use those TOption given type in **Configure** method and prepare it before it will be used. 
**IConfigureNamedOptions** can be used for our own **Options** but the interesting part is when we could configure types used by Asp.Net core internally. One of those type is called **HttpClientFactoryOptions** and it is used to configure each HttpClient created by **IHttpClientFactory**

> I assume that you already know that in .NET Core 2.1+ we should use only HttpClients created by this factory and never do it manually - you can read more about this from [Steve Gordon](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore)

Every time the **IHttpClientFactory** is creating new http client (named or typed) this option is used. Keep in mind that this is done **only once** for each name of the client. Below we can see full example of configuration **GenericHttpClientConfigurator** that is implementing **IConfigureNamedOptions\<HttpClientFactoryOptions\>**. It depend on other options **IOptionsMonitor\<HttpClientOptions\> httpOptions** that are read from application configuration file (appsettings.json) - this gives us opportunity to configure our clients from application settings. We have used **IOptionsMonitor\<\>** instead **IOptions\<\>** because we would like to have many client configurations not one. Another dependency is **ICertificateProvider** that abstracts away logic for reading certificates.

```c#
internal class GenericHttpClientConfigurator : IConfigureNamedOptions<HttpClientFactoryOptions>
{
    private readonly IOptionsMonitor<HttpClientOptions> _httpOptions;
    private readonly ICertificateProvider _certificateProvider;

    public GenericHttpClientConfigurator(IOptionsMonitor<HttpClientOptions> httpOptions, ICertificateProvider certificateProvider)
    {
        _httpOptions = httpOptions;
        _certificateProvider = certificateProvider;
    }

    public void Configure(string name, HttpClientFactoryOptions options)
    {
        // Get named options for HttpClientOptions configured in configuration file
        var httpOption = _httpOptions.Get(name);

        // Configure base HTTP settings
        options.HttpClientActions.Add(action =>
        {
            action.BaseAddress = httpOption.BaseAddress;
            action.Timeout = httpOption.TimeOut;
        }
        );

        // Configure handlers
        options.HttpMessageHandlerBuilderActions.Add(messageHandlerBuilder =>
        {
            // Custom HTTP handler
            messageHandlerBuilder.AdditionalHandlers.Add(messageHandlerBuilder.Services.GetService<CustomHandler>());
            // Polly handler
            messageHandlerBuilder.AdditionalHandlers.Add(GetRetryPolicy());
            // Primary handler
            messageHandlerBuilder.PrimaryHandler = GetPrimaryHanlder(name);
        });
    }

    /// <summary>
    /// Get retry policy using Polly
    /// </summary>
    private PolicyHttpMessageHandler GetRetryPolicy() => new PolicyHttpMessageHandler(HttpPolicyExtensions.HandleTransientHttpError()
                                                                        .CircuitBreakerAsync(3, TimeSpan.FromMilliseconds(500)));

    /// <summary>
    /// Get primary handler with ClientCertificate
    /// </summary>
    private HttpClientHandler GetPrimaryHanlder(string name)
    {
        var clientHandler = new HttpClientHandler();
        clientHandler.ClientCertificates.Add(_certificateProvider.GetCertificate(name));
        return clientHandler;
    }

    public void Configure(HttpClientFactoryOptions options)
    {
        
    }
}

/// <summary>
/// Delegating handler that adds special headers
/// </summary>
internal class Delegating handler : DelegatingHandler
{
    protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        request.Headers.Add("VeryImportantHeader", "value");

        return base.SendAsync(request, cancellationToken);
    }
}

/// <summary>
/// Configuration from appsettings.json
/// </summary>
public class HttpClientOptions
{
    public Uri BaseAddress { get; set; }
    public TimeSpan TimeOut { get; set; }
}

/// <summary>
/// Certificate provider
/// </summary>
interface ICertificateProvider
{
    X509Certificate2 GetCertificate(string name);
}
```

In our example we extract options for our http client by the name using **IOptionsMonitor\<HttpClientOptions\>.Get(string name)** that maps to our app config. 

We can do many things while configuring:
- Configure basic http client option in **HttpClientActions** like BaseAddress or TimeOut
- Configure all delegating handlers
- Configure primary message handler


**Delegating handler** is used like decorator pattern and allow us to execute code before and after our request. Each of delegating handlers is executed after each other like onion and order depend of the order of registration but inside each handler we don't know about the rest. We could do many interesting thins with Delegating handlers. For example our **Delegating handler** ia adding header to each request. We could also make LoggingHandler that have our **SenAsync** method in try/catch statement. We can also use handlers from third party libraries like [Polly](https://github.com/App-vNext/Polly). In our example we used **CircuitBreaker** policy to secure our service from blow up when service we are calling is not responding.

> Be aware that delegating handlers are tricky when it comes to they [lifetime](https://andrewlock.net/understanding-scopes-with-ihttpclientfactory-message-handlers/) - handlers pipeline for each name is created for 2 minutes and is separate from request scope. So with those 2 minutes handlers created for the same client name share instance. You have to be very careful when you are using state services or scope lifetime inside delegating handlers. 

**Primary message handler** is other option that allow us to configure client behavior - in our case we configure our client to use **ClientCertificate**. 

After we prepare our configuration class we have to register it in DI

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureOptions<GenericHttpClientConfigurator>();
}
```

We can also have configuration like this in our **appsettning.json**

```json
{
  "HttpClients": {
    "SampleClient": {
      "BaseAddress": "http://localhost:5666",
      "TimeOut":  "00:00:30"
    },
    "SampleClient2": {
      "BaseAddress": "http://localhost:6666",
      "TimeOut": "00:00:45"
    }
  }
}
```

I'm not including code for mapping this config to **IOptionsMonitor\<HttpClientOptions\>** as an exercise for you :)


We can now use httpclients centrally configured by our code.


# Summary

Options pattern give us great opportunity for configuring our applications. I encourage you to explore this topic and configure your app properly with clean code :)