ExtCore.Data
============

By default, ExtCore doesn’t know anything about data, but you can use ExtCore.Data extension to have
unified approach to working with data and the single data storage context among all the extensions.
Data storage might be represented by a database, a web API, a file structure or anything else.

Currently ExtCore.Data supports MySQL, PostgreSql, SQLite, and SQL Server with Dapper or Entity Framework Core as ORM.
You can add your own database or ORM support.

Packages
--------

* ExtCore.Data;
* ExtCore.Data.Abstractions;
* ExtCore.Data.Entities.Abstractions;
* ExtCore.Data.Dapper;
* ExtCore.Data.Dapper.MySql;
* ExtCore.Data.Dapper.PostgreSql;
* ExtCore.Data.Dapper.Sqlite;
* ExtCore.Data.Dapper.SqlServer;
* ExtCore.Data.EntityFramework;
* ExtCore.Data.EntityFramework.MySql;
* ExtCore.Data.EntityFramework.PostgreSql;
* ExtCore.Data.EntityFramework.Sqlite;
* ExtCore.Data.EntityFramework.SqlServer.