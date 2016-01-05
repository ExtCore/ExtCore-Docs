Quick Start
===========

All you need to do to have modular and extendable web application is:

* add ExtCore.WebApplication as dependency to your main application project;
* inherit your main application’s Startup class from ExtCore.WebApplication.Startup;
* add ExtCore.Infrastructure as dependency to each of your extension projects;
* implement ExtCore.Infrastructure.IExtension interface for each of your extensions (optional);
* tell main application about the extension.

.. toctree::
   :titlesonly:

   samples