Working Principle
=================

First of all, ExtCore discovers and loads the assemblies (using the implementation of the
`IAssemblyProvider <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/IAssemblyProvider.cs#L13>`_ interface).
When the assemblies are discovered and loaded it caches it inside the
`ExtensionManager <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/ExtensionManager.cs#L16>`_ static class.
This class is the entry point for the ExtCore type discovery mechanism, all extensions can use it to get the information
about each other.

Then, ExtCore executes prioritized (defined in a specific order) actions (code fragments) inside the
``ConfigureServices`` and ``Configure`` methods. These actions are defined in the extensions using the
`ConfigureServicesActionsByPriorities <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/IExtension.cs#L30>`_ and
`ConfigureActionsByPriorities <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/IExtension.cs#L38>`_ properties of the
`IExtension <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/IExtension.cs#L17>`_ interface.
This is very important thing, because it allows the extensions to execute their own code during the
web application initialization and startup. They can register services, add middleware etc.

ExtCore.Mvc extension defines
`2 ConfigureServices method actions <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc/MvcExtension.cs#L29>`_ and
`2 Configure method actions <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc/MvcExtension.cs#L45>`_.
The first 2 actions turn on static files and MVC support. The second 2 actions add static files and MVC middleware.

ExtCore.Mvc extension discovers all views (added as resources and/or precompiled) and static content (added as resources)
and make it accessible using the
`custom implementation <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc/CompositeFileProvider.cs#L20>`_
of the ``IFileProvider`` interface.