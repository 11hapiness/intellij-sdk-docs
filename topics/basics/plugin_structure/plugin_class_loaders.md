<!-- Copyright 2000-2025 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# Class Loaders

<link-summary>Introduction and tips for using plugin class loaders.</link-summary>

Each plugin has a dedicated class loader, which is used to load the plugin's classes.
This allows each plugin to use a different library version, even if the same library is used by the IDE itself or by another plugin.

## Overriding IDE Dependencies

Gradle 7 introduced the `implementation` scope that replaced the `compile` scope.
For this setup, to use a library dependency declared by a plugin instead of the version bundled in the IDE, add the following snippet to the Gradle build script:

```kotlin
configurations.all {
  resolutionStrategy.sortArtifacts(ResolutionStrategy.SortOrder.DEPENDENCY_FIRST)
}
```

> [Third-Party Software and Licenses](https://www.jetbrains.com/legal/third-party-software/) list all bundled libraries and their versions for each IDE.
>
{title="Bundled Libraries"}

## Loading Classes from Plugin Dependencies

By default, the main IDE class loader loads classes that are not found in the plugin class loader.
However, in the <path>[plugin.xml](plugin_configuration_file.md)</path> file, you may use the [`<depends>`](plugin_configuration_file.md#idea-plugin__depends) element to specify that a [plugin depends](plugin_dependencies.md) on one or more other plugins.
In this case, the class loaders of those plugins will be used for classes not found in the current plugin.
This allows a plugin to reference classes from other plugins.

## Using `ServiceLoader`

Some libraries use [`ServiceLoader`](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/util/ServiceLoader.html) to detect and load implementations.
To make it work in a plugin, the context class loader must be set to the plugin's classloader and restored afterward with the original one around initialization code:

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
val currentThread = Thread.currentThread()
val originalClassLoader = currentThread.contextClassLoader
val pluginClassLoader = this.javaClass.classLoader
try {
  currentThread.contextClassLoader = pluginClassLoader
  // code working with ServiceLoader here
} finally {
  currentThread.contextClassLoader = originalClassLoader
}
```

</tab>
<tab title="Java" group-key="java">

```java
Thread currentThread = Thread.currentThread();
ClassLoader originalClassLoader = currentThread.getContextClassLoader();
ClassLoader pluginClassLoader = this.getClass().getClassLoader();
try {
  currentThread.setContextClassLoader(pluginClassLoader);
  // code working with ServiceLoader here
} finally {
  currentThread.setContextClassLoader(originalClassLoader);
}
```

</tab>
</tabs>
