Tutorial: Registering and Using a Service Inside an Extension
=============================================================

Often, we need to register a service inside an extension. For example, we may want to implement
some interface in different ways and allow user to choose the implementation by using the specific extension.
ExtCore.Data extension uses this approach to provide different storages support.

We will create the main web application project, one shared project that will contain the service interface,
and 2 extension projects with the different implementations of that interface.

Create Main Web Application
---------------------------

Now let’s start Visual Studio and create new ASP.NET Core project:

.. image:: /images/tutorial_service/1.png

.. image:: /images/tutorial_service/2.png

Empty project is created.

Right click on your project in the Solution Explorer and open NuGet Package Manager. Switch to Browse tab and type
ExtCore.WebApplication in the Search field (be sure that Include prerelease checkbox is checked).
Click Install button:

.. image:: /images/tutorial_service/3.png

Also add dependency on Microsoft.Extensions.Configuration.Json package.

Create the appsettings.json file in the project root. We will use this file to provide configuration
parameters to ExtCore (such as path of the extensions folder). Now it should contain only one
parameter ``Extensions:Path`` and look like this:

.. code-block:: js
    :emphasize-lines: 2,4

    {
      "Extensions": {
        // Please keep in mind that you have to change '\' to '/' on Linux-based systems
        "Path": "\\Extensions"
      }
    }

Open Startup.cs file. Inside the ``ConfigureServices`` method call ``services.AddExtCore`` one. Pass the extensions
path as the parameter. Inside the ``Configure`` method call ``applicationBuilder.UseExtCore`` one with no parameters.

Now your ``Startup`` class should look like this:

.. code-block:: c#

    public class Startup
    {
      private string extensionsPath;

      public Startup(IHostingEnvironment hostingEnvironment, IConfiguration configuration)
      {
        this.extensionsPath = hostingEnvironment.ContentRootPath + configuration["Extensions:Path"];
      }

      public void ConfigureServices(IServiceCollection services)
      {
        services.AddExtCore(this.extensionsPath);
      }

      public void Configure(IApplicationBuilder applicationBuilder)
      {
        applicationBuilder.UseExtCore();
        applicationBuilder.Run(async (context) =>
        {
          await context.Response.WriteAsync("Hello World!");
        });
      }
    }

That’s all, you now have ExtCore-based web application. Now we need to create the shared project
that will contain the service interface. Both the main web application and the extension projects
will have explicit dependencies on this package:

.. image:: /images/tutorial_service/4.png

Create the ``IOperation`` interface in this project:

.. code-block:: c#

    public interface IOperation
    {
      int Calculate(int a, int b);
    }

Create Extensions
-----------------

Now create 2 more projects for the service interface implementations: PlusExtension and MultiplyExtension.
Add reference on the Shared project to both of them. After that, create corresponding classes in that projects:

.. code-block:: c#

    public class PlusOperation : IOperation
    {
      public int Calculate(int a, int b)
      {
        return a + b;
      }
    }

.. code-block:: c#

    public class MultiplyOperation : IOperation
    {
      int Calculate(int a, int b)
      {
        return a * b;
      }
    }

After that each extension needs to register its own implementation of the ``IOperation`` interface inside the
ASP.NET Core DI. To do that we need to implement the ``IConfigureServicesAction`` interface (it is defined inside the
ExtCore.Infrastructure package, don’t forget to add the dependency). This is the example for the PlusExtension extension:

.. code-block:: c#
    :emphasize-lines: 7

    public class AddOperationAction : IConfigureServicesAction
    {
      public int Priority => 1000;

      public void Execute(IServiceCollection services, IServiceProvider serviceProvider)
      {
        services.AddScoped(typeof(IOperation), typeof(PlusOperation));
      }
    }

Good. We are ready for the final step.

Put it Together
---------------

First of all, add reference on the Shared project to the main web application project.
Now modify the ``Configure`` method in next way:

.. code-block:: c#
    :emphasize-lines: 6

    public void Configure(IApplicationBuilder applicationBuilder, IOperation operation)
    {
      applicationBuilder.UseExtCore();
      applicationBuilder.Run(async (context) =>
        {

          await context.Response.WriteAsync(operation.Calculate(5, 10).ToString());
        }
      );
    }

The implementation of the ``IOperation`` interface, which is used to calculate the final value, will be provided
by the ASP.NET Core DI. Our code doesn’t know which implementation is used, it is registered by the selected extension.
To select the extension we need to copy its DLL file to the Extensions folder of the main web application,
or add implicit reference on that project.

So, let’s copy the PlusExtension.dll file to the Extensions folder and try to run our application:

.. image:: /images/tutorial_service/5.png

Everything works as expected. We can replace the PlusExtension.dll with the MultiplyExtension.dll, restart the
web application and the result will change.

You can find the complete source of this sample project on GitHub: 
`ExtCore framework 5.0.0 sample web application that registers a service inside the extension <https://github.com/ExtCore/ExtCore-Sample-Service>`_.