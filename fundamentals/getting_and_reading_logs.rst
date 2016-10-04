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
`this article <https://docs.asp.net/en/latest/fundamentals/logging.html>`_ or just add next
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