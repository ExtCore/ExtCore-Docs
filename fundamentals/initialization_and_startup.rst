Initialization and Startup
==========================

ExtCore initialization and startup process consists of running two extension methods:
``AddExtCore`` and ``UseExtCore``. These methods must be called inside the ``ConfigureServices`` and ``Configure`` methods
of the web application’s ``Startup`` class:

.. code-block:: c#
    :emphasize-lines: 3,15

    public void ConfigureServices(IServiceCollection services)
    {
      services.AddExtCore("absolute path to your extensions");
    }

    public void Configure(IApplicationBuilder applicationBuilder, IHostingEnvironment hostingEnvironment)
    {
      if (hostingEnvironment.IsDevelopment())
      {
        applicationBuilder.UseDeveloperExceptionPage();
        applicationBuilder.UseDatabaseErrorPage();
        applicationBuilder.UseBrowserLink();
      }

      applicationBuilder.UseExtCore();
    }

``AddExtCore`` Method
---------------------

This method discovers and loads the assemblies and caches them into the
`ExtensionManager <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/ExtensionManager.cs#L15>`_ class.
Then it `executes code from all the extensions <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/Extensions/ServiceCollectionExtensions.cs#L64>`_
inside the ``ConfigureServices`` method using the implementations of the
`IConfigureServicesAction <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/Actions/IConfigureServicesAction.cs#L13>`_
interface.

``UseExtCore`` Method
---------------------

This method `executes code from all the extensions <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/Extensions/ApplicationBuilderExtensions.cs#L32>`_
inside the ``Configure`` method using the implementations of the
`IConfigureAction <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/Actions/IConfigureAction.cs#L13>`_
interface.