---
description: Use an integration test for anything that involves database operations.
---

# Writing integration tests

We use a shared database fixture as described in the [Entity Framework documentation](https://docs.microsoft.com/en-us/ef/core/testing/sharing-databases), after each tests the transaction is reverted and data restored to an initial state. \(defined in [`SharedDatabaseFixture.cs`](https://github.com/GetGatekeeper/Server/blob/42b356ba24be958c8933b7ca0af0ff1bffd34d64/Server.Tests/SharedDatabaseFixture.cs)\)

Inside the actual test logic, you can use the same assertions as in a regular unit test.

### Example

Note that the actual test here is performed between the following lines:

```csharp
using (var transaction = Fixture.Connection.BeginTransaction())
{
    using (var context = Fixture.CreateContext(transaction))
    {
      // test data here
    }
}
```

These lines setup the transaction for the `AuthDbContext`. After the test execution the changes are reverted in the database.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using AuthServer.Server.GRPC;
using AuthServer.Shared;
using AuthServer.Shared.Admin;
using Grpc.Core;
using Grpc.Core.Testing;
using Grpc.Core.Utils;
using Moq;
using Xunit;

namespace AuthServer.Server.Tests.GRPC
{
    public class TypeaheadServiceTest : IClassFixture<SharedDatabaseFixture>
    {
        public readonly SharedDatabaseFixture Fixture;

        public TypeaheadServiceTest(SharedDatabaseFixture fixture)
        {
            Fixture = fixture;
        }

        [Fact]
        public async Task SearchUser()
        {
            using (var transaction = Fixture.Connection.BeginTransaction())
            {
                using (var context = Fixture.CreateContext(transaction))
                {
                    TypeaheadService typeaheadService = new TypeaheadService(context);

                    SearchUserRequest request = new SearchUserRequest
                    {
                        SearchParameter = "Test User",
                    };

                    SearchUserReply actualReply = await typeaheadService.SearchUser(request, TestServerCallContext.Create("fooMethod", "test.example.com", DateTime.UtcNow.AddHours(1), new Metadata(), CancellationToken.None, "127.0.0.1", null, null, (metadata) => TaskUtils.CompletedTask, () => new WriteOptions(), (writeOptions) => { }));
                    Assert.Equal(2, actualReply.Entries.Count);
                }
            }
        }
    }
}
```

