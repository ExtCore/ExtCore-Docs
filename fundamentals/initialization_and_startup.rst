Initialization and Startup
==========================

As any other ASP.NET Core web application, ExtCore-based web application initialization
and startup process is implemented by ``Startup`` class and consists of 3 steps:

* constructor call;
* ``ConfigureServices`` method call;
* ``Configure`` method call.

Don’t forget to inherit your main web application’s ``Startup`` class from
``ExtCore.WebApplication.Startup``.

There is the overloaded constructor that gets the second parameter of the ``IAssemblyProvider`` type.
It can be used to change the way ExtCore discovers and loads the assemblies (or their location).

Constructor Call
----------------

ExtCore-based web application initializes few protected variables in the constructor. Derived
web application have to initialize ``configurationRoot`` protected variable in its constructor
in order to provide configuration parameters to ExtCore (these configuration parameters are only
used during the initialization and startup):

.. code-block:: c#
    :emphasize-lines: 4,10

    public Startup(IServiceProvider serviceProvider)
      : base(serviceProvider)
    {
      this.serviceProvider.GetService<ILoggerFactory>().AddConsole();

      IConfigurationBuilder configurationBuilder = new ConfigurationBuilder()
        .SetBasePath(this.serviceProvider.GetService<IHostingEnvironment>().ContentRootPath)
        .AddJsonFile("config.json", optional: true, reloadOnChange: true);

      this.configurationRoot = configurationBuilder.Build();
    }

Also, as you can see, we have added console logger here.

``ConfigureServices`` Method Call
---------------------------------

This method is used by ExtCore to discover all the extensions and their assemblies and to cache them
inside the ``ExtensionManager`` class. After that, ExtCore executes prioritized (defined in a specific order)
actions (code fragments) from all the extensions, so each extension may execute some code within this method
using the ``ConfigureServicesActionsByPriorities`` of the ``IExtension`` interface.

You can skip this method and let ExtCore use the default one (defined inside the base ``ExtCore.WebApplication.Startup``
class), or you can override it and add some custom logic, but don’t forget to call the base method
in this case:

.. code-block:: c#

    public override void ConfigureServices(IServiceCollection services)
    {
      base.ConfigureServices(services);

      // Your additional code
    }

For example, ExtCore.Data extension
`uses this method <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Data/DataExtension.cs#L25>`_
to find and register an implementation of the ``IStorage`` interface.

``Configure`` Method Call
-------------------------

ExtCore doesn’t execute any special logic in this method. It only executes prioritized actions from all the extensions
here, in a similar way as it is done in the ``ConfigureServices`` method.

This method can be skipped too in order to use the implementation of the base class, or you can override it and add
some custom logic, but don’t forget to call the base method in this case:

.. code-block:: c#

    override void Configure(IApplicationBuilder applicationBuilder)
    {
      if (this.serviceProvider.GetService<IHostingEnvironment>().IsDevelopment())
      {
        applicationBuilder.UseDeveloperExceptionPage();
        applicationBuilder.UseDatabaseErrorPage();
        applicationBuilder.UseBrowserLink();
      }

      else
      {

      }

      // Your additional code

      base.Configure(applicationBuilder);
    }

For example, ExtCore.Mvc extension
`uses this method <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc/MvcExtension.cs#L45>`_
to add static files and MVC middleware into the web application pipeline.