Default Assembly Provider
=========================

ExtCore uses the `default implementation <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/AssemblyProvider.cs#L21>`_
of the `IAssemblyProvider interface <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/IAssemblyProvider.cs#L13>`_
to discover and load the assemblies.

The instance of that class is created and set in the constructor of the
`Startup class <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/Startup.cs#L33>`_. All the ExtCore-based
web applications must inherit their ``Startup`` classes from this one.

There is `another one <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/Startup.cs#L42>`_
overloaded constructor which gets the second parameter of the ``IAssemblyProvider`` type. You can use it to specify
your custom assembly provider. Also, you can use the existing one but change the
`IsCandidateAssembly <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/AssemblyProvider.cs#L44>`_ or
`IsCandidateCompilationLibrary <https://github.com/ExtCore/ExtCore/blob/master/src/ExtCore.WebApplication/AssemblyProvider.cs#L48>`_ filter.