Tutorial: Create Simple ExtCore-Based Web Application
========================================================

We are going to create simple modular and extendable ExtCore-based web application. First of all,
if you are new to ASP.NET Core please visit `this page <https://www.microsoft.com/net/core>`_. You
will find there everything you need to start developing ASP.NET Core web applications.

Create Main Web Application
---------------------------

Now let’s start Visual Studio and create new ASP.NET Core project:

.. image:: /images/tutorial_simple/1.png

.. image:: /images/tutorial_simple/2.png

Empty project is created.

Open project.json file and add dependencies to ExtCore.WebApplication version 1.0.0-beta1 and
Microsoft.Extensions.Configuration.Json version 1.0.0. After that ``dependencies`` section of your
project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2,5

    "dependencies": {
      "ExtCore.WebApplication": "1.0.0-beta1",
      "Microsoft.AspNetCore.Server.IISIntegration": "1.0.0",
      "Microsoft.AspNetCore.Server.Kestrel": "1.0.0",
      "Microsoft.Extensions.Configuration.Json": "1.0.0",
      "Microsoft.Extensions.Logging.Console": "1.0.0",
      "Microsoft.NETCore.App": {
        "version": "1.0.0",
        "type": "platform"
      }
    }

Create config.json file in the project root. We will use this file to provide configuration
parameters to ExtCore (such as path of the extensions folder). Now it should contain only one
parameter ``Extensions:Path`` and look like this:

.. code-block:: js
    :emphasize-lines: 2,4

    {
      "Extensions": {
        // Please keep in mind that you have to change '\' to '/' on Linux-based systems
        "Path": "\\Extensions"
      }
    }

Open Startup.cs file and modify your ``Startup`` class as follows:

* inherit it from ``ExtCore.WebApplication.Startup`` class;
* add constructor with ``IServiceProvider`` parameter and pass this parameter to the constructor of the parent class;
* initialize ``configurationRoot`` field of the parent class in the constructor;
* override ``ConfigureServices`` and ``Configure`` methods and add calls of parent class ones.

Now your Startup class should look like this:

.. code-block:: c#

    public class Startup : ExtCore.WebApplication.Startup
    {
      public Startup(IServiceProvider serviceProvider)
        : base (serviceProvider)
      {
        this.serviceProvider.GetService<ILoggerFactory>().AddConsole();

        IConfigurationBuilder configurationBuilder = new ConfigurationBuilder()
          .SetBasePath(this.serviceProvider.GetService<IHostingEnvironment>().ContentRootPath)
          .AddJsonFile("config.json", optional: true, reloadOnChange: true);

        this.configurationRoot = configurationBuilder.Build();
      }

      public override void ConfigureServices(IServiceCollection services)
      {
        base.ConfigureServices(services);
      }

      public override void Configure(IApplicationBuilder applicationBuilder)
      {
        base.Configure(applicationBuilder);
        applicationBuilder.Run(async (context) =>
        {
          await context.Response.WriteAsync("Hello World!");
        });
      }
    }

That’s all, you now have ExtCore-based web application. Now we need to create some extension project to
show how ExtCore types discovering works.

Create Extension
----------------

Create new .NET Core class library project:

.. image:: /images/tutorial_simple/3.png

Open project.json file and add dependencies to ExtCore.Infrastructure version 1.0.0-beta1. After that
``dependencies`` section of your project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2

    "dependencies": {
      "ExtCore.Infrastructure": "1.0.0-beta1",
      "NETStandard.Library": "1.6.0"
    }

Create ``Extension`` class and inherit it from ``ExtCore.Infrastructure.ExtensionBase``. Override
``Name`` property in this way:

.. code-block:: c#

    public override string Name
    {
      get
      {
        return "Some name";
      }
    }

It is enough for now.

Put it Together
---------------

We have two options to make our extension available in main web application:

* add direct dependency on ExtCoreExtension in project.json file of ExtCoreWebApplication;
* put compiled ExtCoreExtension.dll file to extensions folder of the ExtCoreWebApplication that is configured in config.json file.

While the first option is too obvious let’s try the second one. Copy the ExtCoreExtension.dll file
to the extensions folder of the ExtCoreWebApplication and modify ``Configure`` method of ``Startup`` class
in next way:

.. code-block:: c#
    :emphasize-lines: 6

    public override void Configure(IApplicationBuilder applicationBuilder)
    {
      base.Configure(applicationBuilder);
      applicationBuilder.Run(async (context) =>
      {
        await context.Response.WriteAsync(ExtensionManager.Extensions.First().Name);
      });
    }

If we run our web application we will have following result:

.. image:: /images/tutorial_simple/4.png

It may not look very impressive, but it’s only the beginning! In the next tutorials we will see
how extensions may execute their own code in ``ConfigureServices`` and ``Configure`` methods, how
to use MVC and how to work with storage.