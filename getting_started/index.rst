Getting Started
===============

All you need to do to have modular and extendable web application is:

* add ExtCore.WebApplication as dependency to your main web application project;
* inherit your main web application’s Startup class from ExtCore.WebApplication.Startup;
* implement ExtCore.Infrastructure.IExtension interface in each of your extensions (optional);
* tell the main web application about the extensions.

.. toctree::
   :titlesonly:

   samples
   tutorial_simple
   tutorial_mvc
   tutorial_data
   tutorial_service