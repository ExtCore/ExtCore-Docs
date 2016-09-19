Tutorial: Create ExtCore-Based MVC Web Application
=====================================================

We are going to create modular and extendable ExtCore-based MVC web application. Please follow
`this tutorial <http://docs.extcore.net/en/latest/getting_started/tutorial_simple.html>`_
to create simple ExtCore-based web application first. We will use it as a base.

So, we have the main web application and extension projects. They work but currently don’t
support MVC. We know that it is quite simple to add MVC support to ASP.NET Core web application
using extension methods ``AddMvc`` and ``UseMvc``. With ExtCore it is even a bit easier.

Modify Main Web Application
---------------------------

Open project.json file and add dependency to ExtCore.Mvc version 1.0.0-beta1. After that
``dependencies`` section of your project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2

    "dependencies": {
      "ExtCore.Mvc": "1.0.0-beta1",
      "ExtCore.WebApplication": "1.0.0-beta1",
      "Microsoft.AspNetCore.Server.IISIntegration": "1.0.0",
      "Microsoft.AspNetCore.Server.Kestrel": "1.0.0",
      "Microsoft.Extensions.Configuration.Json": "1.0.0",
      "Microsoft.Extensions.Logging.Console": "1.0.0",
      "Microsoft.NETCore.App": {
        "version": "1.0.0",
        "type": "platform"
      }
    }

That’s all, there is no second step!

Now open Startup.cs file and remove ``applicationBuilder.Run`` method calling, we don’t need it
anymore. We could even completely remove methods ``ConfigureServices`` and ``Configure`` because
they do nothing except calling of parent class ones.

Now your Startup class should look like this:

.. code-block:: c#

    public class Startup : ExtCore.WebApplication.Startup
    {
      public Startup(IServiceProvider serviceProvider)
        : base (serviceProvider)
      {
        this.serviceProvider.GetService<ILoggerFactory>().AddConsole();

        IConfigurationBuilder configurationBuilder = new ConfigurationBuilder()
          .SetBasePath(this.serviceProvider.GetService<IHostingEnvironment>().ContentRootPath)
          .AddJsonFile("config.json", optional: true, reloadOnChange: true);

        this.configurationRoot = configurationBuilder.Build();
      }

      public override void ConfigureServices(IServiceCollection services)
      {
        base.ConfigureServices(services);
      }

      public override void Configure(IApplicationBuilder applicationBuilder)
      {
        base.Configure(applicationBuilder);
      }
    }

Good. Now let’s add some MVC-related things like controller and views. Also we will add CSS file too.
It would be too obvious to add it to the main web application, so we will do that in the extension.

Modify Extension
----------------

First of all, open project.json file and replace dependency on ExtCore.Infrastructure with dependency
on ExtCore.Mvc.Infrastructure (same version). Then add dependency on Microsoft.AspNetCore.Mvc version
1.0.0. After that ``dependencies`` section of your project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2,3

    "dependencies": {
      "ExtCore.Mvc.Infrastructure": "1.0.0-beta1",
      "Microsoft.AspNetCore.Mvc": "1.0.0",
      "NETStandard.Library": "1.6.0"
    }

Open Extension.cs file and modify your ``Startup`` class as follows:

* inherit it from ``ExtCore.Mvc.Infrastructure.ExtensionBase`` instead of ``ExtCore.Infrastructure.ExtensionBase``;
* remove ``Name`` property (we will use its default implementation);
* override ``UseMvcActionsByPriorities`` property to add the default route.

``UseMvcActionsByPriorities`` property should look this way:

.. code-block:: c#

    public override IEnumerable<KeyValuePair<int, Action<IRouteBuilder>>> UseMvcActionsByPriorities
    {
      get
      {
        return new Dictionary<int, Action<IRouteBuilder>>()
        {
          [1000] = routeBuilder =>
          {
            routeBuilder.MapRoute(name: "Default", template: "{controller}/{action}", defaults: new { controller = "Default", action = "Index" });
          }
        };
      }
    }

With this code our extension registers the default route for the web application which will use it. Each
extension may register its own routes and make them have special order using the priorities.

Now we are ready to create controller and views.

Create ``DefaultController`` class and inherit it from ``Controller``. Add simple ``Index`` action:

.. code-block:: c#

    public class DefaultController : Controller
    {
      public ActionResult Index()
      {
        return this.View();
      }
    }

Create /Views/Shared/_Layout.cshtml and /Views/Default/Index.cshtml views.

_Layout.cshtml:

.. code-block:: html

    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <title>@Html.Raw(this.ViewBag.Title as string)</title>
    </head>
    <body>
      @RenderBody()
    </body>
    </html>

Index.cshtml:

.. code-block:: html

    <h1>Hello From the Extension</h1>

We need to tell the compiler to compile these views as resources to be able to use it later. Open
project.json file and add ``buildOptions`` section there:

.. code-block:: html

    "buildOptions": { "embed": [ "Views/**" ] }

It is enough for now. Rebuild the solution and copy ExtCoreExtension.dll file to the extensions folder
of the ExtCoreWebApplication. Run the web application:

.. image:: /images/tutorial_mvc/1.png

We can see that controller and views are resolved. Cool! Now let’s add some style to the our views.
Create default.css file inside the /Styles folder (you need to create it too):

.. code-block:: css

    body {
      color: red;
    }

Modify ``buildOptions`` section of your project.json file to tell the compiler to compile the styles
too:

.. code-block:: html

    "buildOptions": { "embed": [ "Styles/**", "Views/**" ] }

Finally, add the link to the CSS file to the Index.cshtml view:

.. code-block:: html

    <link href="Styles.default.css" rel="stylesheet" />

Note that resources have flat structure inside the assemblies so we need to replace ``/`` with ``.``
(dot) in the path to the CSS file.

Rebuild the solution again and replace ExtCoreExtension.dll file, run the web application:

.. image:: /images/tutorial_mvc/2.png

As we can see, the text turns red. It means that everything works as expected. In the next tutorials
we will see how to work with the storage.