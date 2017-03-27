Introduction
============

`ExtCore <https://github.com/ExtCore/ExtCore>`_ is free, open source and cross-platform framework for creating
modular and extendable web applications based on ASP.NET Core. It is built using the best and the most modern
tools and languages (Visual Studio 2015, C# etc). Join our team!

ExtCore allows you to build your web applications from the different independent reusable modules or extensions.
Each of these modules or extensions may consist of one or more ASP.NET Core projects and each of these projects
may include everything you want as any other ASP.NET Core project. You don’t need to perform any additional
actions to make it all work: any ASP.NET Core project can be used as an ExtCore-based web application extension
by default. Controllers, view components, views (added as resources and/or precompiled), static content (added as
resources) are resolved automatically. These projects may be then added to the web application in two ways: as
direct dependencies (as source code or NuGet packages) or by copying compiled DLLs to the Extensions folder.
ExtCore supports both of these options out of the box and at the same time.

Furthermore, any project of the ExtCore-based web application is able to discover the types that are defined
inside all the projects (optionally using the predicates for assemblies filtering) and to get the implementations
or instances of that types.

Any module or extension can execute its own code during the web application initialization and startup. You can
use priorities to specify the correct order of the calls. This feature might be used for configuration,
to register services etc.

ExtCore consists of two general packages and two optional basic extensions.

General Packages
----------------

ExtCore general packages are:

* ExtCore.Infrastructure;
* ExtCore.WebApplication.

ExtCore.Infrastructure
~~~~~~~~~~~~~~~~~~~~~~

This package describes such basic shared things as ``IExtension`` interface and its abstract implementation –
``ExtensionBase`` class. Also it contains ``ExtensionManager`` class – the central element in the ExtCore types
discovering mechanism. Most of the modules or extensions need this package as dependency in order to be able
to discover types, extensions etc.

ExtCore.WebApplication
~~~~~~~~~~~~~~~~~~~~~~

This package describes basic web application behavior with ``Startup`` abstract class. This behavior includes
modules and extensions assemblies discovering, ``ExtensionManager`` initialization etc. Any ExtCore web
application must inherit its ``Startup`` class from ``ExtCore.WebApplication.Startup`` class in order to work
properly. Also this package contains ``IAssemblyProvider`` interface and its implementation –
``AssemblyProvider`` class which is used to discover assemblies and might be replaced with the custom one.

Basic Extensions
----------------

ExtCore basic extensions are:

* ExtCore.Data;
* ExtCore.Mvc.

ExtCore.Data
~~~~~~~~~~~~

By default, ExtCore doesn’t know anything about data and storage, but you can use ExtCore.Data extension to have
unified approach to working with data and single storage context among all the extensions. Currently it supports
Microsoft SQL Server, PostgreSql and SQLite, but it is very easy to add another storage support.

ExtCore.Mvc
~~~~~~~~~~~

By default, ExtCore web applications are not MVC ones. MVC support is provided for them by ExtCore.Mvc extension.
This extension initializes MVC, makes it possible to use controllers, view components, views (added as resources
and/or precompiled), static content (added as resources) from other extensions etc.