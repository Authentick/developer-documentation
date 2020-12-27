---
description: Use a unit test for anything that does not involve database operations.
---

# Writing unit tests

Unit tests would usually test the behaviour of a given function. Consider to also test for negative scenarios instead only for positive scenarios.

{% hint style="info" %}
If you are accessing the database, you are encouraged to write an integration test instead. This ensures that your query will execute correctly.
{% endhint %}

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

