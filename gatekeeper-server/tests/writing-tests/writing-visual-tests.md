---
description: Use visual tests for anything that may be relevant UI changes.
---

# Writing visual tests

We use [Percy](https://percy.io) and [Puppeteer](https://pptr.dev/) for visual tests, the file that is being executed can be found in [`Gatekeeper.Server.Web.Tests.Visual/snapshot.js`](https://github.com/GetGatekeeper/Server/blob/main/Gatekeeper.Server.Web.Tests.Visual/snapshots.js).

The Percy documentation includes details on how to write these tests:

{% embed url="https://docs.percy.io/docs/puppeteer" %}



