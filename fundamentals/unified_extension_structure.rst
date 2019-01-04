Unified Extension Structure
===========================

Here X is your application name and Y is your extension name:

*	X.Y;
*	X.Y.Data.Entities;
*	X.Y.Data.Abstractions;
*	X.Y.Data.SpecificStorageA;
*	X.Y.Data.SpecificStorageB;
*	X.Y.Data.SpecificStorageC;
*	X.Y.Frontend;
*	X.Y.Backend;
*	etc.

For example, we can take a look at ExtCore.Data extension structure:

* ExtCore.Data;
* ExtCore.Data.Entities.Abstractions;
* ExtCore.Data.Abstractions;
* ExtCore.Data.EntityFramework;
* ExtCore.Data.EntityFramework.PostgreSql;
* ExtCore.Data.EntityFramework.Sqlite;
* ExtCore.Data.EntityFramework.SqlServer.

The idea is that your extension is logically split into the projects. Entities are just entities (Data.Entities project),
they don’t need to have any links to the different extension’s projects (but entities project may have dependency on the entities project
of the different extension to be able to work with them; for example, your Statistics extension Report entity
may need to be able to work with a Product entity from the Ecommerce extension).

Repository abstractions are put inside the Data.Abstractions. This project needs to know about the Data.Entities one, and that’s all.
All the code that needs to work with the entities should do that using the repository abstractions,
without any explicit links on the concrete implementations.

If your extension has some services, split them into the abstractions (Services.Abstractions) and the default implementations (Services.Default).

Main extension project shouldn’t contain any shared content, like utility classes etc. None of the other extension projects
should have dependencies on it. The purpose of the main extension’s project is to keep extension metadata and to resolve and register services.
It should look for the existing implementations of the given services using the ExtensionManager class and register them in this way,
without having an explicit dependency on the implementations project (please look how it is done in the ExtCore.Data extension).
In this case it will be possible to replace the services implementations just by copying another DLL file into the extensions folder
or adding some NuGet package.
