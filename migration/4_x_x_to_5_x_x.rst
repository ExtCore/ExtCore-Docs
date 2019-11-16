4.x.x to 5.x.x
==============

There are 3 main differences between ExtCore 4.x.x and 5.x.x.

.NET Core 3.0 applications can’t run on top of the full .NET Framework.

Razor runtime compilation is now (starting from .NET Core 3.0) turned off by default.
So far (5.0.0), ExtCore does not support Razor runtime compilation at all, so views and pages added as resources won’t be resolved anymore.

To make them work you have to convert all the projects containing Razor files to
`Razor class libraries <https://docs.microsoft.com/en-us/aspnet/core/razor-pages/ui-class?view=aspnetcore-3.0&tabs=visual-studio>`_.

4.x.x:

.. code-block:: xml

    <Project Sdk="Microsoft.NET.Sdk.Web">

      <PropertyGroup>
        <TargetFramework>netcoreapp2.2</TargetFramework>
      </PropertyGroup>
      
      <ItemGroup>
        <EmbeddedResource Include="Views\**;Images\**" />
      </ItemGroup>

      ...

    </Project>

5.x.x:

.. code-block:: xml

    <Project Sdk="Microsoft.NET.Sdk.Razor">

      <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
        <AddRazorSupportForMvc>true</AddRazorSupportForMvc>
      </PropertyGroup>

      <ItemGroup>
        <EmbeddedResource Include="Images\**" />
      </ItemGroup>

      ...

    </Project>
    
Routes are replaced with endpoints, so ``ExtCore.Mvc.Infrastructure.Actions.IUseMvcAction`` is replaced with
``ExtCore.Mvc.Infrastructure.Actions.IUseEndpointsAction``.