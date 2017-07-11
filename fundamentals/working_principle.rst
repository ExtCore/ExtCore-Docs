Working Principle
=================

ExtCore is included into a project using two extension methods: ``AddExtCore`` and ``UseExtCore``.

First of all, inside the ``AddExtCore`` method ExtCore discovers and loads the assemblies (using the implementation of the
`IAssemblyProvider <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/IAssemblyProvider.cs#L13>`_ interface).
When the assemblies are discovered and loaded it caches it inside the
`ExtensionManager <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/ExtensionManager.cs#L16>`_ static class.
This class is the entry point for the ExtCore type discovery mechanism, all extensions can use it to get the information
about each other.

Then, using the ``AddExtCore`` and ``UseExtCore`` methods, ExtCore executes the actions (code fragments) inside the
``ConfigureServices`` and ``Configure`` methods. These actions are defined by the implementations of the
`IConfigureServicesAction <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/Actions/IConfigureServicesAction.cs#L13>`_ and
`IConfigureAction <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Infrastructure/Actions/IConfigureAction.cs#L13>`_
interfaces, and allows developer to specify the execution order by using the ``Priority`` property.
This is very important thing, because it allows the extensions to execute their own code during the
web application initialization and startup. They can register services, add middleware etc.

ExtCore.Mvc extension also defines two more action interfaces which might be used to configure MVC:
`IAddMvcAction <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc.Infrastructure/Actions/IAddMvcAction.cs#L13>`_ and
`IUseMvcAction <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc.Infrastructure/Actions/IUseMvcAction.cs#L13>`_ ones.

ExtCore.Mvc extension discovers all the views (added as resources and/or precompiled) and static content (added as resources)
and make it accessible using the
`custom implementation <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Mvc/CompositeFileProvider.cs#L20>`_
of the ``IFileProvider`` interface.