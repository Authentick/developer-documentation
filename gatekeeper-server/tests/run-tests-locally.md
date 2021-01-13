# Run tests locally

If you use the recommended developer environment setup, the following commands will be sufficient to run the tests locally:

```bash
cd Gatekeeper.Client.Tests/ && dotnet test
cd Gatekeeper.Server.Web.Tests/ && dotnet test
```

Note that doing so will not affect your local data as a separate test database is used.

