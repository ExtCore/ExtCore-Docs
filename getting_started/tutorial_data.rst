Tutorial: Create ExtCore-Based Web Application with Storage
===========================================================

We are going to create modular and extendable ExtCore-based web application with data.
Please follow `this tutorial <http://docs.extcore.net/en/latest/getting_started/tutorial_mvc.html>`_
to create ExtCore-based MVC web application first. We will use it as a base.

So, we have the main web application and extension projects. They work but currently don’t
know anything about data and storage. Let’s assume that we want our extension to display
some data (the list of persons for example) from the storage (SQLite database for example) in its
view. The `ExtCore.Data <http://docs.extcore.net/en/latest/extensions/extcore_data.html>`_
and `ExtCore.Data.EntityFramework <http://docs.extcore.net/en/latest/extensions/extcore_data_entityframework.html>`_
extensions will help us to achieve that goal.

In short, we should do the following:

* create the storage (SQLite database in this case);
* describe the entities (the only one ``Person`` entity in this case);
* describe the repositories to work with that entities (abstraction and one implementation for each
of the supported storages; SQLite database support only in this case);
* add logic for getting the persons from the database and displaying them.

Describe the Entities
---------------------

To be able to share entities among different projects (and following the
`ExtCore.Data <http://docs.extcore.net/en/latest/extensions/extcore_data.html>`_ extension
design pattern) we will describe entity classes in the separate project.

Create new .NET Core class library project:

.. image:: /images/tutorial_data/1.png

Open NuGet Package Manager and add dependency on the ExtCore.Data.Entities.Abstractions version 6.0.0 package
(be sure that Include prerelease checkbox is checked).

Create ``Person`` class that implements ``ExtCore.Data.Entities.Abstractions.IEntity``. Create
``Id`` and ``Name`` properties. After that ``Person`` class should look like this:

.. code-block:: c#

    public class Person : IEntity
    {
      public int Id { get; set; }
      public string Name { get; set; }
    }

Describe the Repositories
-------------------------

`ExtCore.Data <http://docs.extcore.net/en/latest/extensions/extcore_data.html>`_ extension
implements Unit of Work and Repository design patterns. It means that all the work with the storage
is performed in a single context, and it also means that every entity has its own repository that
contains all methods you need to work with it. Repositories when created are resolved automatically,
so everything we need to do is to create corresponding repositories.

To be able to share repositories among different projects (and following the
`ExtCore.Data <http://docs.extcore.net/en/latest/extensions/extcore_data.html>`_ extension
design pattern) we will describe repository classes in the separate projects (one for abstractions
and one for each of the supported storages).

Abstractions
~~~~~~~~~~~~

Create new .NET Core class library project:

.. image:: /images/tutorial_data/2.png

Open NuGet Package Manager and add dependency on the ExtCore.Data.Abstractions version 6.0.0 package.
Also add dependency on your local Extension.Data.Entities project.

Create ``IPersonRepository`` interface that implements the ``ExtCore.Data.Abstractions.IRepository`` one.
Create ``All`` method there. After that the ``IPersonRepository`` interface should look like this:

.. code-block:: c#

    public interface IPersonRepository : IRepository
    {
      IEnumerable<Person> All();
    }

SQLite Storage Support
~~~~~~~~~~~~~~~~~~~~~~

Create one more .NET Core class library project:

.. image:: /images/tutorial_data/3.png

Open NuGet Package Manager and add dependency on the ExtCore.Data.EntityFramework.Sqlite version 6.0.0 package.
Also add dependency on your local Extension.Data.Abstractions project.

Create ``EntityRegistrar`` class that implements the ``ExtCore.Data.EntityFramework.IEntityRegistrar`` interface.
Override ``RegisterEntities`` method in this way:

.. code-block:: c#

    public void RegisterEntities(ModelBuilder modelbuilder)
    {
      modelbuilder.Entity<Person>(etb =>
        {
          etb.HasKey(e => e.Id);
          etb.Property(e => e.Id);
          etb.ToTable("Persons");
        }
      );
    }

Now create ``PersonRepository`` class that implements ``Extension.Data.Abstractions.IPersonRepository`` interface
and inherit it from the ``ExtCore.Data.EntityFramework.RepositoryBase<Person>`` class. Create ``All`` method there.
After that ``PersonRepository`` class should look like this:

.. code-block:: c#

    public class PersonRepository : RepositoryBase<Person>, IPersonRepository
    {
      public IEnumerable<Person> All()
      {
        return this.dbSet.OrderBy(p => p.Name);
      }
    }

Modify Main Web Application
---------------------------

Now when we have everything we need to work with data and storage let’s display the list of persons
in the view.

First of all create the SQLite database with one Persons (pay attention to the case of the characters)
table and few rows. You can use `SqliteBrowser <https://github.com/sqlitebrowser/sqlitebrowser>`_ for
that.

The second step is to add ``ConnectionStrings:Default`` parameter to the appsettings.json file:

.. code-block:: js
    :emphasize-lines: 1,3

    "ConnectionStrings": {
      // Please keep in mind that you have to change '\' to '/' on Linux-based systems
      "Default": "Data Source=..\\..\\..\\db.sqlite"
    }

Finally, open NuGet Package Manager and add dependencies on the ExtCore.Data version 6.0.0 and
ExtCore.Data.EntityFramework.Sqlite version 6.0.0 packages.

Modify Extension
----------------

Add dependency on your local Extension.Data.Abstractions project.

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

    @model IEnumerable<Extension.Data.Entities.Person>
    <h1>Hello From the Extension</h1>
    @foreach (var person in this.Model)
    {
      <p>@person.Name</p>
    }

Rebuild the solution, put files Extension.dll, Extension.Data.Entities.dll, Extension.Data.Abstractions.dll,
and Extension.Data.EntityFramework.Sqlite.dll to the extensions folder of the WebApplication, run the web application:

.. image:: /images/tutorial_data/4.png

As we can see, data from the database is displayed.

You can find the complete source of this sample project on GitHub: 
`ExtCore framework 6.0.0 sample web application that uses a database <https://github.com/ExtCore/ExtCore-Sample-Data>`_.
