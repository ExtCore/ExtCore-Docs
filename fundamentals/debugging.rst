Debugging ExtCore Extensions
============================

To be able to debug an ExtCore extension you need to add direct dependencies to all of its packages to the
main web application project.json file. When development process is complete, you may remove that direct dependencies
and use extensions as DLL files.