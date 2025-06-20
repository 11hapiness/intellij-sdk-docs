<!-- Copyright 2000-2025 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# Internal Actions – UI Inspector

<web-summary>
Explore the UI Inspector tool to check properties of any IDE UI component. Learn how to use it to inspect, find code locations, and get details on specific component properties.
</web-summary>

<link-summary>UI Inspector allows checking properties of any UI component in the running IDE instance.</link-summary>

The _UI Inspector_ is a tool to interrogate elements of the IDE's UI to get an internal description of each element.

<include from="internal_actions_intro.md" element-id="enable_internal_mode_tip"></include>

## Using the UI Inspector

Centering the cursor on a UI element and pressing <shortcut>Ctrl+Alt</shortcut> (<shortcut>Ctrl+Option</shortcut> on macOS) when _clicking_ the left mouse button reveals the properties of the Swing component.

For example, to get information about the <control>Build Project</control> button's "hammer" icon on the toolbar (highlighted in green), put the mouse cursor on the icon and press <shortcut>Ctrl/Cmd+Alt</shortcut> while clicking the mouse.

The _UI Inspector_ displays the icon details:

![Internal Icon Info](internal_ui_inspector_icon_info.png)

## `added-at` Property

Sometimes, inspecting a complex component's properties is not enough to understand how the component was created and configured.
_UI Inspector_ gives the possibility of finding the code where the selected component was added, which makes it much easier to understand which APIs can be used to build custom components with similar complexity.

To find the place where the component was added, select the <control>added-at</control> property to show the stack trace:

![added-at stack trace](internal_ui_inspector_added_at.png)

## Specific Component Properties

Various components used in the IntelliJ Platform expose additional properties.
These can be useful to locate the underlying implementation, related Action, and so on.

Custom Swing components can also provide additional properties via [`UiInspectorContextProvider`](%gh-ic%/platform/platform-api/src/com/intellij/internal/inspector/UiInspectorContextProvider.java) or its dedicated subclasses.

### Editor

| Type                                                                                                                                           | Properties                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <p>[`ActiveGutterRenderer`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/editor/markup/ActiveGutterRenderer.java)<br/>(2023.1+)</p> | <p><control>Clicked Renderer (Class)</control> - `ActiveGutterRenderer` instance/class</p>                                                                                                                                                                                                                                                                                                                                                                    |
| <p>[`GutterIconRenderer`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/editor/markup/GutterIconRenderer.java)<br/>(2023.1+)</p>     | <p><control>Clicked Renderer (Class)</control> - `GutterIconRenderer` instance/class</p><p><control>Accessible Name</control> - `GutterIconRenderer.getAccessibleName()`</p><p><control>Icon</control> - `GutterIconRenderer.getIcon()`</p><p><control>Marker Info - Element / Navigation Handler</control> - [`LineMarkerInfo.getElement() / getNavigationHandler()`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/daemon/LineMarkerInfo.java)</p> |
| [`GutterMark`](%gh-ic%/platform/editor-ui-api/src/com/intellij/codeInsight/daemon/GutterMark.java)                                             | <p><control>gutter renderer</control> - [`GutterMark`](%gh-ic%/platform/editor-ui-api/src/com/intellij/codeInsight/daemon/GutterMark.java) implementation</p>                                                                                                                                                                                                                                                                                                 |
| <p>[`Inlay`](inlay_hints.md)<br/>(2023.1+)</p>                                                                                                 | <p><control>Inlay Renderer (Class)</control> - `Inlay.getRenderer()` instance/class</p><p><control>Inlay Gutter Renderer</control> - `Inlay.getGutterIconRenderer()`</p><p><control>Inlay Properties</control> - `Inlay.getProperties()`</p>                                                                                                                                                                                                                  |
| [`IntentionAction`/`QuickFix`](code_inspections_and_intentions.md)                                                                             | <p><control>intention action</control>/<control>quick fix</control> - [`IntentionAction`](%gh-ic%/platform/analysis-api/src/com/intellij/codeInsight/intention/IntentionAction.java) / [`QuickFix`](%gh-ic%/platform/analysis-api/src/com/intellij/codeInspection/QuickFix.java) implementation</p>                                                                                                                                                           |

### Action

| Type                                | Properties                                                                                                                                                                                                                                                          |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`AnAction`](action_system.md)      | <p><control>Action</control> - [`AnAction`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java) implementation</p><p><control>Action ID</control> - Action `id`</p><p><control>Action Plugin ID</control> - contributing plugin</p> |
| [`ActionToolbar`](action_system.md) | <p><control>Toolbar Group</control> - Action Group ID</p><p><control>All Toolbar Groups</control> - contained Action Group IDs</p><p><control>Target component</control> - `ActionToolbar.setTargetComponent()`</p>                                                 |

### IDE and UI Components

| Type                                 | Properties                                                                                                                                                                                                                                              |
|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`DialogWrapper`](dialog_wrapper.md) | <control>dialogWrapperClass</control> - [`DialogWrapper`](%gh-ic%/platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java) implementation                                                                                                  |
| [`ToolWindow`](tool_windows.md)      | <p><control>Tool Window ID</control> - `id`</p><p><control>Tool Window Icon</control> - `icon`</p><p><control>Tool Window Factory</control> - [`ToolWindowFactory`](%gh-ic%/platform/platform-api/src/com/intellij/openapi/wm/ToolWindowFactory.kt)</p> |
| [`Tree`](lists_and_trees.md)         | <control>treeModelClass</control> - `javax.swing.tree.TreeModel` implementation                                                                                                                                                                         |

## Inspecting Settings

Enable <ui-path>Internal Actions | UI | Debugging Info in UI</ui-path> (2024.2+)
(<ui-path>View | Appearance | Details in Tree View</ui-path> in earlier versions)
in [Internal Mode](enabling_internal.md) to show Settings page `id` in the tree.

Some additional properties are available when inspecting the <control>Settings</control> dialog (2023.1+).

> _UI Inspector_ must be invoked only after opening the <control>Settings</control> dialog.

| <p>Settings page</p><p>Reference</p>                                                                                             | Properties                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <p>_All settings_</p><p>[](settings_guide.md)</p>                                                                                | <p><control>Configurable class</control> - [`Configurable`](%gh-ic%/platform/ide-core/src/com/intellij/openapi/options/Configurable.java) class</p><p><control>Configurable ID</control> - `id` attribute</p><p><control>Configurable weight</control> - `groupWeight` attribute</p>                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| <p><ui-path>Editor &#124; Color Scheme</ui-path></p><p>[](syntax_highlighting_and_error_highlighting.md#text-attributes-key)</p> | <p><control>Text Attributes Key</control> - [`TextAttributesKey`](%gh-ic%/platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java) external name</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| <p><ui-path>Editor &#124; File Types</ui-path></p><p>[](registering_file_type.md)</p>                                            | <p><control>FileTypeID</control> - `FileType.getName()`</p><p><control>FileType Class</control> - [`FileType`](%gh-ic%/platform/core-api/src/com/intellij/openapi/fileTypes/FileType.java) implementation</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| <p><ui-path>Editor &#124; Inspections</ui-path></p><p>[](code_inspections.md)</p>                                                | <p><control>Inspection key</control> - Inspection `id`</p><p><control>Inspection tool class</control> - Inspection implementation</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <p><ui-path>Editor &#124; Inlays</ui-path></p><p>[](inlay_hints.md)</p>                                                          | <p><control>Inlay Group Key</control> - [`InlayGroupSettingProvider.getGroup().key`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/settings/InlayGroupSettingProvider.kt)</p><p><control>Inlay Group Key</control> - [`InlayGroup.key`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/InlayHintsProvider.kt)</p><p><control>Inlay Provider Model ID</control> - [`InlayProviderSettingsModel.id`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/settings/InlayProviderSettingsModel.kt)</p><p><control>Inlay ImmediateConfigurable ID</control> - [`ImmediateConfigurable.Case.id`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/InlayHintsProvider.kt)</p> |
| <p><ui-path>Editor &#124; Intentions</ui-path></p><p>[](code_intentions.md)</p>                                                  | <p><control>Intention Class</control> - [`IntentionAction`](%gh-ic%/platform/analysis-api/src/com/intellij/codeInsight/intention/IntentionAction.java) class</p><p><control>Intention description directory</control> - `<descriptionDirectoryName>`</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| <p><ui-path>Plugins</ui-path></p><p>[](plugin_configuration_file.md)</p>                                                         | <p><control>Plugin ID</control> - Plugin `<id>`</p><p><control>Plugin Dependencies</control> - IDs of dependent plugins</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

## Accessibility Checks

Since 2025.2, the _UI Inspector_ includes the <control>Show Accessibility Issues</control> button that enables inspections for common accessibility problems, similar to browser tools like Google Chrome Lighthouse or apps like Accessibility Insights for Windows.
These issues are usually invisible in the UI, but they can still prevent users who rely on assistive technologies like screen readers or voice control from interacting with the UI.

For each problem, the affected value in the Properties table contains a description that explains what it affects and how to fix it.

![Accessibility Checks](internal_ui_inspector_accessibility_checks.png)
