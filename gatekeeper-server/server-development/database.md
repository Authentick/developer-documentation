---
description: Learn how to interact with the database.
---

# Database

Authentick uses Entity Framework Core to interact with all data. The tutorial provided by Microsoft provides a lot of references:

{% embed url="https://docs.microsoft.com/en-us/ef/core/" %}

As a underlying database we do use PostgreSQL using Npgsql. But this is an implementation detail that you likely don't have to deal with.

## Adminer

We include "Adminer" in the development environment which offers a web interface to the included database. 

{% embed url="https://www.adminer.org/" %}

To use Adminer, open your web browser and connect to port 8080 of your development server. For login, use the following information:

* **System:** PostgreSQL
* **Server:** localhost
* **User:** postgres
* **Password:** example

The database that contains the Authentick data is called "db".

![Using Adminer to access the Authentick Database.](../../.gitbook/assets/using-adminer-to-access-the-gatekeeper-database.png)



