Tutorial: Create ExtCore-Based Web Application with Storage
===========================================================

We are going to create modular and extendable ExtCore-based web application with data.
Please follow `this tutorial <http://docs.extcore.net/en/latest/getting_started/tutorial_mvc.html>`_
to create ExtCore-based MVC web application first. We will use it as a base.

So, we have the main web application and extension projects. They work but currently don’t
know anything about data and storage. Let’s assume that we want our extension to display
some data (the list of persons for example) from the storage (SQLite database for example) in its
view. `ExtCore.Data <http://docs.extcore.net/en/latest/basic_extensions/extcore_data.html>`_
extension will help us to achieve that goal.

In short, we should do the following:

* create the storage (SQLite database in this case);
* describe the models (the only one ``Person`` model in this case);
* describe the repositories to work with that models (abstraction and one implementation for each
of the supported storages; SQLite database support only in this case);
* add logic for getting the persons from the database and displaying them.

Describe the Models
-------------------

To be able to share models among different projects (and following the
`ExtCore.Data <http://docs.extcore.net/en/latest/basic_extensions/extcore_data.html>`_ extension
design pattern) we will describe model classes in the separate project.

Create new .NET Core class library project:

.. image:: /images/tutorial_data/1.png

Open project.json file and add dependency on ExtCore.Data.Models.Abstractions version 1.0.0.
After that ``dependencies`` section of your project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2

    "dependencies": {
      "ExtCore.Data.Models.Abstractions": "1.0.0",
      "NETStandard.Library": "1.6.0"
    }

Create ``Person`` class that implements ``ExtCore.Data.Models.Abstractions.IEntity``. Create
``Id`` and ``Name`` properties. After that ``Person`` class should look like this:

.. code-block:: c#

    public class Person : IEntity
    {
      public int Id { get; set; }
      public string Name { get; set; }
    }

Describe the Repositories
-------------------------

`ExtCore.Data <http://docs.extcore.net/en/latest/basic_extensions/extcore_data.html>`_ extension
implements Unit of Work and Repository design patterns. It means that all the work with the storage
is performed in a single context, and it also means that every model have its own repository that
contains all methods you need to work with it. Repositories when created are resolved automatically,
so everything we need to do is to create corresponding repositories.

To be able to share repositories among different projects (and following the
`ExtCore.Data <http://docs.extcore.net/en/latest/basic_extensions/extcore_data.html>`_ extension
design pattern) we will describe repository classes in the separate projects (one for abstractions
and one for each of the supported storages).

Abstractions
~~~~~~~~~~~~

Create new .NET Core class library project:

.. image:: /images/tutorial_data/2.png

Open project.json file and add dependencies on ExtCore.Data.Models.Abstractions version 1.0.0
and on ExtCoreExtension.Data.Models version 1.0.0. After that ``dependencies`` section of your
project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2,3

    "dependencies": {
      "ExtCore.Data.Abstractions": "1.0.0",
      "ExtCoreExtension.Data.Models": "1.0.0",
      "NETStandard.Library": "1.6.0"
    }

Create ``IPersonRepository`` interface that implements ``ExtCore.Data.Abstractions.IRepository``.
Create ``All`` method. After that ``IPersonRepository`` interface should look like this:

.. code-block:: c#

    public interface IPersonRepository : IRepository
    {
      IEnumerable<Person> All();
    }

SQLite Storage Support
~~~~~~~~~~~~~~~~~~~~~~

Create new .NET Core class library project:

.. image:: /images/tutorial_data/3.png

Open project.json file and add dependencies on ExtCore.Data.Models.Abstractions version 1.0.0
and on ExtCoreExtension.Data.Abstractions version 1.0.0. After that ``dependencies`` section of your
project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2

    "dependencies": {
      "ExtCore.Data.EntityFramework.Sqlite": "1.0.0",
      "ExtCoreExtension.Data.Abstractions": "1.0.0",
      "NETStandard.Library": "1.6.0"
    }

Create ``ModelRegistrar`` class that implements ``ExtCore.Data.EntityFramework.Sqlite.IModelRegistrar``.
Override RegisterModels method in this way:

.. code-block:: c#

    public void RegisterModels(ModelBuilder modelbuilder)
    {
      modelbuilder.Entity<Person>(etb =>
        {
          etb.HasKey(e => e.Id);
          etb.Property(e => e.Id);
          etb.ForSqliteToTable("Persons");
        }
      );
    }

Create ``PersonRepository`` class that implements ``ExtCoreExtension.Data.Abstractions.IPersonRepository``
and inherit it from ``ExtCore.Data.EntityFramework.Sqlite.RepositoryBase<Person>``. Create ``All`` method.
After that ``PersonRepository`` class should look like this:

.. code-block:: c#

    public IEnumerable<Person> All()
    {
      return this.dbSet.OrderBy(p => p.Name);
    }

Modify Main Web Application
---------------------------

Now when we have everything we need to work with data and storage let’s display the list of persons
in the view.

First of all create the SQLite database with one Persons (pay attention to the case of the characters)
table and few rows. You can use `SqliteBrowser <https://github.com/sqlitebrowser/sqlitebrowser>`_ for
that.

The second step is to add ``Data:DefaultConnection:ConnectionString`` parameter to config.json file:

.. code-block:: js
    :emphasize-lines: 1,2,4

    "Data": {
      "DefaultConnection": {
        // Please keep in mind that you have to change '\' to '/' on Linux-based systems
        "ConnectionString": "Data Source=..\\..\\..\\db.sqlite"
      }
    }

Finally open project.json file and add dependencies on ExtCore.Data.EntityFramework.Sqlite version
1.0.0 and on ExtCore.Data version 1.0.0. After that ``dependencies`` section of your
project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 2

    "dependencies": {
      "ExtCore.Data": "1.0.0",
      "ExtCore.Data.EntityFramework.Sqlite": "1.0.0",
      "ExtCore.Mvc": "1.0.0",
      "ExtCore.WebApplication": "1.0.0",
      "Microsoft.AspNetCore.Server.IISIntegration": "1.0.0",
      "Microsoft.AspNetCore.Server.Kestrel": "1.0.0",
      "Microsoft.Extensions.Configuration.Json": "1.0.0",
      "Microsoft.Extensions.Logging.Console": "1.0.0",
      "Microsoft.NETCore.App": {
        "version": "1.0.0",
        "type": "platform"
      }
    }

Modify Extension
----------------

Open project.json file and add dependency on ExtCoreExtension.Data.Abstractions version 1.0.0.
After that ``dependencies`` section of your project.json file should look like this:

.. code-block:: js
    :emphasize-lines: 3

    "dependencies": {
      "ExtCore.Mvc.Infrastructure": "1.0.0",
      "ExtCoreExtension.Data.Abstractions": "1.0.0",
      "Microsoft.AspNetCore.Mvc": "1.0.0",
      "NETStandard.Library": "1.6.0"
    }

Modify your ``DefaultController`` class to make it get parameter of type ``IStorage`` from the DI
in the constructor and save that object to the private variable:

.. code-block:: c#
    :emphasize-lines: 1,3

    public DefaultController(IStorage storage)
    {
      this.storage = storage;
    }

Now modify your ``Index`` action to get persons from the database and put them to the view:

.. code-block:: c#
    :emphasize-lines: 3

    public ActionResult Index()
    {
      return this.View(this.storage.GetRepository<IPersonRepository>().All());
    }

Now open your /Views/Default/Index.cshtml view and modify it in following way:

.. code-block:: html

    @model IEnumerable<ExtCoreExtension.Data.Models.Person>
    <h1>Hello From the Extension</h1>
    @foreach (var person in this.Model)
    {
      <p>@person.Name</p>
    }

Rebuild the solution, put files ExtCoreExtension.dll, ExtCoreExtension.Data.Models.dll,
ExtCoreExtension.Data.Abstractions.dll and ExtCoreExtension.Data.EntityFramework.Sqlite.dll
to the extensions folder of the ExtCoreWebApplication, run the web application:

.. image:: /images/tutorial_data/4.png

As we can see, data from the database is displayed.