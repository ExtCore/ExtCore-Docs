Getting Started
===============

How to Start Using the ExtCore Framework
----------------------------------------

1. Add dependency on the `ExtCore.WebApplication <https://www.nuget.org/packages/ExtCore.WebApplication/>`_
NuGet package to your main web application project.

2. Optionally (if your web application uses MVC), add dependency on the `ExtCore.Mvc <https://www.nuget.org/packages/ExtCore.Mvc/>`_
NuGet package as well.

3. Call the ``services.AddExtCore(extensionsPath)`` extension method inside the ``ConfigureServices`` method of your ``Startup`` class:

.. code-block:: c#
    :emphasize-lines: 3
	
    public void ConfigureServices(IServiceCollection services)
    {
      services.AddExtCore(this.extensionsPath);
    }

4. Call the ``applicationBuilder.UseExtCore()`` extension method inside the ``Configure`` method of your ``Startup`` class:

.. code-block:: c#
    :emphasize-lines: 3

    public void Configure(IApplicationBuilder applicationBuilder)
    {
      applicationBuilder.UseExtCore();
    }

Your modular and extendable web application is ready to use. Now you can create an extension or use the existing ones.
ExtCore automatically discovers types, controllers, views, styles, scripts, any other static content.
Entities and repositories are also discovered automatically. You can use events to notify other extensions
about something important.

How to Create an Extension
--------------------------

1. Create an empty .NET Core class library project.

2. Put some classes, controllers, views, styles, scripts, and any other static content you want there. Views and static content
must be embedded into the resulting assembly as resources using the following line inside the project file (.csproj):

.. code-block:: xml
    :emphasize-lines: 1

    <EmbeddedResource Include="Views\**;Styles\**;Scripts\**" />

3. Build your extension project and copy the resulting assembly’s DLL file into the extensions folder of your main
web application (or you can simply add implicit dependency on the created extension project, or on the NuGet package).

4. If your extension needs to execute some code inside the ``ConfigureServices`` method of the main
web application’s ``Startup`` class (for example, to register some service inside the DI),
you can implement the ``ExtCore.Infrastructure.Actions.IConfigureServicesAction`` interface.

5. If your extension needs to execute some code inside the ``Configure`` method of  the main
web application’s ``Startup`` class (for example, to configure the web application’s request pipeline),
you can implement the ``ExtCore.Infrastructure.Actions.IConfigureAction`` interface.

It is recommended to follow the
`unified extension structure <http://docs.extcore.net/en/latest/fundamentals/unified_extension_structure.html>`_
when developing your own extensions.

.. toctree::
   :titlesonly:

   samples
   tutorial_simple
   tutorial_mvc
   tutorial_data
   tutorial_service
   using_migrations
   using_identity