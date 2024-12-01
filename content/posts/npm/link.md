---
title: Using npm link to test packages locally
publishDate: 2024-12-01
tags: ['npm']
---
## Linking

From the folder where the package is being build run the following

```
npm link
```

this will create a global entry with your package name as defined in package.json file.



From the application consumming the package, simply do

```
npm link <package name>
```


## Listing the packages published globally

```
npm ls -g --depth=0 --link=true
```

## Removing a package

```
npm rm --global <package name>
```



