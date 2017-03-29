Getting and Reading Logs
========================

ExtCore writes logs while initialization and startup process so you can understand what is
going on and where the problem is when something goes wrong. Also using logs you can see
which assemblies have been discovered and loaded, which actions have been executed by extensions
in the ``ConfigureServices`` and ``Configure`` methods etc.

Getting Logs
------------

By default ASP.NET Core web application (as well as ExtCore-based one) doesn’t have any logger
configured, so logs are not shown. To configure the logger, you can follow
`this article <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging>`_ or just add next
line of code in the constructor of your Startup class:

.. code-block:: c#

    this.serviceProvider.GetService<ILoggerFactory>().AddConsole();

This approach is used in our sample so you can take a look at it
`there <https://github.com/ExtCore/ExtCore-Sample/blob/master/src/WebApplication/Startup.cs#L18>`_.

It is an excerpt from our sample application log:

.. code-block:: bat

    info: ExtCore.WebApplication.AssemblyProvider[0]
          Discovering and loading assemblies from path 'C:\SomePath\WebApplication\Extensions'
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'WebApplication.ExtensionA, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'WebApplication.ExtensionB.Data.Abstractions, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'WebApplication.ExtensionB.Data.EntityFramework.Sqlite, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'WebApplication.ExtensionB.Data.Models, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'WebApplication.ExtensionB, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Discovering and loading assemblies from DependencyContext
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'WebApplication, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Data, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Data.Abstractions, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Data.EntityFramework.Sqlite, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Data.Models.Abstractions, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Mvc, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.Mvc.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.AssemblyProvider[0]
          Assembly 'ExtCore.WebApplication, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized ConfigureServices action 'AddStaticFiles' of ExtCore.Mvc.MvcExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized ConfigureServices action '<get_ConfigureServicesActionsByPriorities>b__1_0' of ExtCore.Data.DataExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized ConfigureServices action 'AddMvc' of ExtCore.Mvc.MvcExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized Configure action 'UseStaticFiles' of ExtCore.Mvc.MvcExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized Configure action 'UseMvc' of ExtCore.Mvc.MvcExtension
    info: ExtCore.Infrastructure.ExtensionBase[0]
          Executing prioritized UseMvc action '<get_UseMvcActionsByPriorities>b__1_0' of WebApplication.ExtensionA.ExtensionA+<>c
    info: ExtCore.Infrastructure.ExtensionBase[0]
          Executing prioritized UseMvc action '<get_UseMvcActionsByPriorities>b__1_0' of WebApplication.ExtensionB.ExtensionB+<>c

Reading Logs
------------

Let’s take a look at the log output.

The first 2 lines indicate that the process of the assemblies loading from the specific path has begun. The path is displayed too,
so you can check it:

.. code-block:: bat

    info: ExtCore.WebApplication.AssemblyProvider[0]
          Discovering and loading assemblies from path 'C:\SomePath\WebApplication\Extensions'

The first 2 lines show us the path ExtCore tries to discover and load the assemblies from:

The next 2 lines indicate that the process of the assemblies loading from the ``DependencyContext`` has begun:

.. code-block:: bat

    info: ExtCore.WebApplication.AssemblyProvider[0]
          Discovering and loading assemblies from DependencyContext

Discovered and loaded assemblies are displayed in the both cases.

After the assemblies are discovered and resolved, prioritized actions in ``ConfigureServices`` and ``Configure`` methods
are executed:

.. code-block:: bat

    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized ConfigureServices action 'AddStaticFiles' of ExtCore.Mvc.MvcExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized ConfigureServices action '<get_ConfigureServicesActionsByPriorities>b__1_0' of ExtCore.Data.DataExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized ConfigureServices action 'AddMvc' of ExtCore.Mvc.MvcExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized Configure action 'UseStaticFiles' of ExtCore.Mvc.MvcExtension
    info: ExtCore.WebApplication.Startup[0]
          Executing prioritized Configure action 'UseMvc' of ExtCore.Mvc.MvcExtension
    info: ExtCore.Infrastructure.ExtensionBase[0]
          Executing prioritized UseMvc action '<get_UseMvcActionsByPriorities>b__1_0' of WebApplication.ExtensionA.ExtensionA+<>c
    info: ExtCore.Infrastructure.ExtensionBase[0]
          Executing prioritized UseMvc action '<get_UseMvcActionsByPriorities>b__1_0' of WebApplication.ExtensionB.ExtensionB+<>c

The name of the action (if it is not anonymous one) and the extension class are displayed. You can check
the order of the execution too.

Initialization and startup process is now finished.