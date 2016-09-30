Getting and Reading Logs
========================

ExtCore writes logs while initialization and startup process so you can understand what is
going on and where the problem is when something goes wrong. Also using logs you can see
what assemblies have been discovered and loaded, what actions have been executed by extensions
in the ``ConfigureServices`` and ``Configure`` methods etc.

By default ASP.NET Core web application (as well as ExtCore-based one) doesn't have any logger
`this article <https://docs.asp.net/en/latest/fundamentals/logging.html>`_ or just add next
line of code in the constructor of your Startup class:

.. code-block:: c#

    this.serviceProvider.GetService<ILoggerFactory>().AddConsole();

This approach is used in our sample so you can take a look at it
`there <https://github.com/ExtCore/ExtCore-Sample/blob/master/src/WebApplication/Startup.cs#L18>`_.
