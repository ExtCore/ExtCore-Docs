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
    :emphasize-lines: 3

    public Startup(ILoggerFactory loggerFactory)
    {
      loggerFactory.AddConsole();
    }

This approach is used in our sample so you can take a look at it
`there <https://github.com/ExtCore/ExtCore-Sample/blob/master/src/WebApplication/Startup.cs#L27>`_.
It is important to do that in the constructor and not in the ``Configure`` method, because otherwise
you will miss the logs before the ``Configure`` method is called.

It is an excerpt from our sample application log:

.. code-block:: bat

    info: ExtCore.WebApplication[0]
          Discovering and loading assemblies from path 'C:\Path\WebApplication\Extensions'
    info: ExtCore.WebApplication[0]
          Assembly 'WebApplication.ExtensionA, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'WebApplication.ExtensionB.Data.Abstractions, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'WebApplication.ExtensionB.Data.Entities, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'WebApplication.ExtensionB.Data.EntityFramework.Sqlite, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'WebApplication.ExtensionB, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Discovering and loading assemblies from DependencyContext
    info: ExtCore.WebApplication[0]
          Assembly 'WebApplication, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Data, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Data.Abstractions, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Data.Entities.Abstractions, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Data.EntityFramework, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Data.EntityFramework.Sqlite, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Infrastructure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Mvc, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.Mvc.Infrastructure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'ExtCore.WebApplication, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'Newtonsoft.Json, Version=9.0.0.0, Culture=neutral, PublicKeyToken=30ad4fe6b2a6aeed' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Assembly 'Remotion.Linq, Version=2.1.0.0, Culture=neutral, PublicKeyToken=fee00910d6e5f53b' is discovered and loaded
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Data.Actions.AddStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Data.EntityFramework.Actions.AddStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Mvc.Actions.AddStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Mvc.Actions.AddMvcAction'
    info: ExtCore.WebApplication[0]
          Executing Configure action 'ExtCore.Mvc.Actions.UseStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing Configure action 'ExtCore.Mvc.Actions.UseMvcAction'
    info: ExtCore.Mvc[0]
          Executing UseMvc action 'ExtensionA.Actions.UseMvcAction'
    info: ExtCore.Mvc[0]
          Executing UseMvc action 'ExtensionB.Actions.UseMvcAction'

Reading Logs
------------

Let’s take a look at the log output.

The first 2 lines indicate that the process of the assemblies loading from the specific path has begun. The path is displayed too,
so you can check it:

.. code-block:: bat

    info: ExtCore.WebApplication[0]
          Discovering and loading assemblies from path 'C:\Path\WebApplication\Extensions'

Then we can see few lines that show the assemblies that are discovered and loaded.
		  
The next 2 lines indicate that the process of the assemblies loading from the ``DependencyContext`` has begun:

.. code-block:: bat

    info: ExtCore.WebApplication[0]
          Discovering and loading assemblies from DependencyContext

Discovered and loaded assemblies are displayed again.

After the assemblies are discovered and resolved, user actions inside the ``ConfigureServices`` and ``Configure`` methods
are executed:

.. code-block:: bat

    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Data.Actions.AddStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Data.EntityFramework.Actions.AddStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Mvc.Actions.AddStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing ConfigureServices action 'ExtCore.Mvc.Actions.AddMvcAction'
    info: ExtCore.WebApplication[0]
          Executing Configure action 'ExtCore.Mvc.Actions.UseStaticFilesAction'
    info: ExtCore.WebApplication[0]
          Executing Configure action 'ExtCore.Mvc.Actions.UseMvcAction'
    info: ExtCore.Mvc[0]
          Executing UseMvc action 'ExtensionA.Actions.UseMvcAction'
    info: ExtCore.Mvc[0]
          Executing UseMvc action 'ExtensionB.Actions.UseMvcAction'

It is easy to understand what is going on and what is executed and to check the execution order.

Initialization and startup process is now finished.