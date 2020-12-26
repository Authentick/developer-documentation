# Writing tests

Gatekeeper uses [xUnit.net](https://xunit.net/) for it's mix of unit and integration tests. For mocking we do use the [Moq4 library](https://github.com/Moq/moq4/wiki/Quickstart).

If you are not sure whether to write an unit or integration test, consider the following guidance:

* Use an integration test for anything that involves database operations
* Use a unit test for anything that does not involve database operations

The reason being that we want to make sure the queries compile and execute against the database driver.

## Writing unit tests

Unit tests would usually test the behaviour of a given function. Consider to also test for negative scenarios instead only for positive scenarios.

#### Example

The following test would check for a positive \(`Assert.True`\) and a negative \(`Assert.False`\) scenario: 

```csharp
using AuthServer.Server.Services.Crypto;
using Xunit;

namespace AuthServer.Server.Tests.Services.Crypto
{
    public class HasherTest
    {
        [Fact]
        public void TestValidHash()
        {
            string input = "ThisIsTheStringToHash";

            Hasher hasher = new Hasher();
            string hash = hasher.Hash(input);

            Assert.True(hasher.VerifyHash(hash, input));
        }

        [Fact]
        public void TestInvalidHash()
        {
            string input = "ThisIsTheStringToHash";

            Hasher hasher = new Hasher();
            string hash = hasher.Hash(input);

            Assert.False(hasher.VerifyHash(hash, "AnotherString"));
        }
    }
}
```

## Writing integration tests

We use a shared database fixture as described in the [Entity Framework documentation](https://docs.microsoft.com/en-us/ef/core/testing/sharing-databases), after each tests the transaction is reverted and data restored to an initial state. \(defined in [`SharedDatabaseFixture.cs`](https://github.com/GetGatekeeper/Server/blob/42b356ba24be958c8933b7ca0af0ff1bffd34d64/Server.Tests/SharedDatabaseFixture.cs)\)

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



