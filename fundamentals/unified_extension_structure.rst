Unified Extension Structure
===========================

Here X is your application name and Y is your extension name:

*	X.Y;
*	X.Y.Data.Models;
*	X.Y.Data.Abstractions;
*	X.Y.Data.SpecificStorageA;
*	X.Y.Data.SpecificStorageB;
*	X.Y.Data.SpecificStorageC;
*	X.Y.Frontend;
*	X.Y.Backend;
*	etc.

For example, we can take a look at ExtCore.Data extension structure:

* ExtCore.Data;
* ExtCore.Data.Models.Abstractions;
* ExtCore.Data.Abstractions;
* ExtCore.Data.EntityFramework.Sqlite;
* ExtCore.Data.EntityFramework.SqlServer.