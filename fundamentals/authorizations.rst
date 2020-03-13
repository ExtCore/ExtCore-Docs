Usage for authorization middleware
==================================

If you must use `Microsoft.AspNetCore.Authorization` in your application, the call must be made in an extension.

Extcore uses internal priorities that start at 10000.

You must therefore give a priority higher than this number when calling Authorize.

.. code-block:: bat
    :emphasize-lines: 14

    using System;
    using ExtCore.Infrastructure.Actions;
    using Microsoft.AspNetCore.Builder;

    namespace Barebone.Actions
    {
        public class UseAuthorizationAction : IConfigureAction
        {
            public void Execute(IApplicationBuilder applicationBuilder, IServiceProvider serviceProvider)
            {
                applicationBuilder.UseAuthorization();
            }

            public int Priority => 10001;
        }
    }

Otherwise, you will get an meaningful error stating that ASP.NET Core cannot find the middleware.