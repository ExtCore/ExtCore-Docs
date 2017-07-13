Getting Started
===============

All you need to do to have modular and extendable web application is:

* add dependencies on ExtCore.WebApplication and, optionally, on ExtCore.Mvc NuGet packages;
* call services.AddExtCore(extensionsPath) inside the ConfigureServices method of your Startup class;
* call applicationBuilder.UseExtCore() inside the Configure method of your Startup class;
* create .NET Core class library projects with controllers, views, styles, scripts, and other resources;
* implement the ExtCore.Infrastructure.Actions.IConfigureServicesAction interface in order to register any service inside the DI from the extension projects;
* implement the ExtCore.Infrastructure.Actions.IConfigureAction interface in order to configure a web application's request pipeline from the extension projects;
* copy the extension DLL files into the extension path;
* that’s it, your modular and extendable web application is ready to use.

.. toctree::
   :titlesonly:

   samples
   tutorial_simple
   tutorial_mvc
   tutorial_data
   tutorial_service