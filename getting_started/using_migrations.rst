Using Migrations
================

(It is only applicable for the ExtCore framework version 3.1.0-beta1 and higher.)

Migrations feature is only available if you are using Entity Framework Core as the ORM. To make it possible to use the Migrations tool,
please, follow these steps:

1. Create the ``DesignTimeStorageContextFactory`` class (you can use any name) and inherit it from the
`ExtCore.Data.EntityFramework.DesignTimeStorageContextFactoryBase<T> <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.Data.EntityFramework/DesignTimeStorageContextFactoryBase.cs#L18>`_
one (here ``T`` is the type of the storage context you are using). You don’t need to implement any methods in this class:

.. code-block:: c#
    :emphasize-lines: 1

    public class DesignTimeStorageContextFactory : DesignTimeStorageContextFactoryBase<StorageContext>
    {
    }

2. Initialize the ``StorageContextOptions.MigrationsAssembly`` property with the name of the assembly (project) where
the ``DesignTimeStorageContextFactory`` class is created:

.. code-block:: c#
    :emphasize-lines: 4

    services.Configure<StorageContextOptions>(options =>
      {
        options.ConnectionString = this.configurationRoot.GetConnectionString("Default");
        options.MigrationsAssembly = typeof(DesignTimeStorageContextFactory).GetTypeInfo().Assembly.FullName;
      }
    );

3. Call the ``DesignTimeStorageContextFactory.Initialize()`` static method after the ``StorageContextOptions`` class configuration:

.. code-block:: c#
    :emphasize-lines: 1

    DesignTimeStorageContextFactory.Initialize(services.BuildServiceProvider());

That’s all, now you can use the Migrations tool.