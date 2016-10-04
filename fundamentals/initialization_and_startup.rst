Initialization and Startup
==========================

As any other ASP.NET Core web application, ExtCore-based web application initialization
and startup process is implemented by ``Startup`` class and consists of 3 steps:

* constructor call;
* ``ConfigureServices`` method call;
* ``Configure`` method call.

Constructor Call
----------------

ExtCore-based web application initializes few protected variables in the constructor. Derived
web application must inherit its ``Startup`` class from ``ExtCore.WebApplication.Startup`` and
initialize ``configurationRoot`` protected variable in its constructor in order to provide
configuration parameters to ExtCore.

``ConfigureServices`` Method Call
---------------------------------

``Configure`` Method Call
-------------------------