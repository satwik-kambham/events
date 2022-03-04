---
title: "Packaging Application"
description: ""
lead: ""
date: 2022-03-04T09:30:12+05:30
lastmod: 2022-03-04T09:30:12+05:30
draft: false
images: []
menu:
  docs:
    parent: ""
weight: 6
toc: true
---

In order to package and distribute your electron application, you can use [Electron Forge](https://www.electronforge.io/).

## Installing Electron Forge
Run the following command to install electron forge:
```
npm install --save-dev @electron-forge/cli
npx electron-forge import
```

## Package Application
To build your application for distribution, run the command:
```
npm run make
```

This will create a folder called `out` which contains your application ready for distribution.