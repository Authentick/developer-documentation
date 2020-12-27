---
description: Learn how to do design changes.
---

# Stylesheet

The SCSS code is located inside the `Client/Assets/CSS` folder. 

Whilst the development container is trying to start a Gulp watcher it may fail. So running Gulp manually after SCSS changes is more reliable.

```bash
cd Client/ && gulp
```

You do not need to commit any built files, in fact they are in the `.gitignore`. Our build pipeline will automatically build the final CSS files. 

