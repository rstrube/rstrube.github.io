---
title: 'Minimal .NET Core Console Application with IoC'
tags: ['development','dotnet','code-example']
---
```csharp
using System;
using System.IO;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.EnvironmentVariables;
using Microsoft.Extensions.DependencyInjection;

namespace MyDomain
{
    public abstract class MyConsoleApp
    {
        public static IConfigurationRoot CreateConfiguration(string basePath)
        {
            basePath = !string.IsNullOrEmpty(basePath) ? basePath : Directory.GetCurrentDirectory();

            return new ConfigurationBuilder()
                .SetBasePath(basePath)
                .AddJsonFile("appsettings.json", optional : true, reloadOnChange : true)
                .AddEnvironmentVariables()
                .Build();
        }

        public static IServiceCollection ConfigureServices(IConfigurationRoot config)
        {
            var serviceCollection = new ServiceCollection()
                .AddOptions();

                /* General Configuration
                .Configure<MyOptions>(config);

                // Section Configuration
                .Configure<MySectionOptions>(config.GetSection("MySubSection"));

                // Transient Service
                .AddTransient<IMyService,MyService>();

                // Transient Service with parametered cstor
                .AddTransient<IMyService>(s => new MyService(myParam));

                // Singleton Service
                .AddSingleton<IMySingletonService, MySingletonService>();
                */

            /* If you need access to the configuration to affect the configured services

            // General Configuration
            var myOptions = new MyOptions();
            config.Bind(myOptions);

            // Section Configuration
            var mySubOptions = new MySubOptions();
            config.GetSection("MySubSection").Bind(mySubOptions);

            */

            return serviceCollection;
        }

        public static (IConfigurationRoot, IServiceCollection) ConfigureConsoleApplication(string basePath)
        {
            var config = CreateConfiguration(basePath);
            var serviceCollection = ConfigureServices(config);
            return (config, serviceCollection);
        }

        static void Main(string[] args)
        {
            if(args == null || args.Length == 0)
                return;

            var commandArg = args[0].ToLowerInvariant();
            var pathArg = args.Length <= 1 ? "." : args[1];

            if(!Directory.Exists(pathArg))
                return;

            pathArg = Path.GetFullPath(pathArg);

            var appConfiguration = ConfigureConsoleApplication(pathArg);

            var config = appConfiguration.Item1;
            var serviceProvider = appConfiguration.Item2.BuildServiceProvider();

          	//var myService = serviceProvider.GetService<IMyService>();

            switch(commandArg)
            {
                case "help":
                	//run help command
                    return;

                case "anothercommand":
                	//run another command
                    return;
                    
                default:
                    return;
            }
        }
    }
}
```