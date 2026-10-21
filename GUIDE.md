# UnrealSense — quick guide

UnrealSense adds Unreal Engine help to Visual Studio 2022 and 2026: **where your C++ is used in Blueprints**, reflection
specifier suggestions, Unreal-aware completion, navigation and warnings. It works with the Unreal Editor closed too.

## Shortcuts

| Key | Command |
|---|---|
| **Alt+Shift+Q** | Quick Menu: every command, filtered for where the caret is |
| **Alt+Shift+B** | Find Blueprint Usages |
| **Ctrl+click**, **F12**, **Alt+G** | Go to definition |
| **Alt+O** | Switch header / source |
| **Alt+Shift+T** / **Ctrl+Shift+T** | Go to Symbol / Go to File |
| **Alt+M** (or **Alt+Shift+M**) | Go to Symbol in File |
| **Alt+Shift+V** | Override virtual functions |
| **Alt+Shift+D** | Create implementation |
| **Alt+Shift+R** | Rename symbol |

Everything else is in **Extensions › UnrealSense** and in the Quick Menu.

## Install and update

From the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AndreaDecaro.UnrealSense):
**Extensions › Manage Extensions**, search **UnrealSense**, **Install**, then close Visual Studio to finish.

![Manage Extensions with UnrealSense found on the Marketplace](guide-img/install.svg?v=8ce7b894)

Updates install when you close Visual Studio. UnrealSense also checks by itself (at startup and every hour): when a newer
version is out, a window (or a bar, if Visual Studio is already open) offers **Open Manage Extensions**, **Later** or
**Skip this version**, plus a **Download the .vsix** link. After an update, **What's new in UnrealSense** shows the
changes once. Option: *Updates*.

**Without the Marketplace**: download `UnrealSense-<version>.vsix` from the
[releases](https://github.com/AndreaDecaro/Unreal-Sense/releases), close Visual Studio and double-click it (if your
Visual Studio is not listed, run `Install-UnrealSense.ps1` next to it with PowerShell). For automatic updates add
`https://andreadecaro.github.io/Unreal-Sense/gallery/atom.xml` under Tools › Options › Environment › Extensions ›
*Additional Extension Galleries* (replace the old `https://assoandrea.github.io/...` address if you had it).

## License

UnrealSense is free. About every 12 hours it downloads a small signed public file (`license-policy.json`); the request
carries nothing about you or your projects. It must succeed at least once every 48 hours, or the features turn off until
it does (the bar has **Check now**). If a paid key is ever required, a bar will say so with the days left, and the key
goes in **Extensions › UnrealSense › License...**.

![The information bar shown when a license key is needed](guide-img/license.svg?v=e2605536)
*This bar appears only if a key is ever needed: today UnrealSense is free.*

## First start

Open the project's solution: UnrealSense finds the project and reads its C++ and Blueprints in a few seconds (longer the
first time). **View › Output › UnrealSense** shows what it is doing.

![The Output window on the UnrealSense pane](guide-img/first-start.svg?v=365660a5)

## Blueprints

### Blueprint counters

A grey label next to reflected classes, functions and properties says how many Blueprints use them (`12 derived
Blueprints`, `3 Blueprint usages`), also through parent Blueprints or C++ subclasses ("via …"). **Click it** for the
list. On properties there are two counters, `2 Blueprint usages - 3 Blueprint overrides`: **usages** read or write it in
graphs, **overrides** change its value in Class Defaults, levels or data assets.

![Grey labels with the number of Blueprints next to a class and a function](guide-img/hints.svg?v=d64a8988)

### Find usages in Blueprints

Caret on a class, function or property, **Alt+Shift+B** (or right-click › **Find Blueprint Usages**). For calls,
events and variable get/set nodes, the **Detail** column says where the node is (`EventGraph › Call Fire`). Double-click
a row to open the asset in the running Unreal Editor (the status bar repeats where to look); the toolbar has *Show in
Explorer* and *Copy reference*.

For a property, the **Value** column shows the value each Blueprint gives it (`600 (from BP_Base)` when inherited,
`C++ default` when unchanged), the values on placed actors in levels (`3 actors: 600, 800, 1200`, World Partition
included) and in data assets. Hovering the property shows the first five values.

![The Blueprint Usages window](guide-img/blueprint-usages.svg?v=a822012f)

**Opening assets in the Unreal Editor** uses its Python Remote Execution: the first time, UnrealSense offers to enable it
in the project (restart the editor afterwards; files not checked out in Perforce are made writable and listed). Without
it, or with the editor closed, the file is shown in Explorer; **Extensions › UnrealSense › Open Blueprints in Unreal
Editor...** asks again.

### Search text inside Blueprints

**Extensions › UnrealSense › Search Text in Blueprints...** (or the Quick Menu) finds any text in Blueprints and data
assets: comments, pin values, variable defaults and tooltips, widget texts, DataTable rows, String Tables; with *Names*
also variable, function, pin and tag names. Each result says where it is (`EventGraph › Call PrintString › pin In
String`). Options: *Match case*, *Whole word*, *All assets* (textures, meshes, sounds...), *Editor caches*.

![The Search in Blueprints window](guide-img/search-in-blueprints.svg?v=7bb3ced3)

## Writing code

### Specifiers and hover info

Inside `UPROPERTY(...)`, `UFUNCTION(...)`, `UCLASS(...)` etc. the list offers the specifiers of your engine version with
their explanation, and values too (project `Category` names, the function for `ReplicatedUsing`). Hover a specifier for
its documentation, or a reflected name for its Blueprint usages.

![Completion of UPROPERTY specifiers](guide-img/specifiers.svg?v=0554d4a9)

![Tool tips on a specifier and on a class](guide-img/quick-info.svg?v=c7e2a926)

### Member completion (`.` and `->`)

After `.` or `->` the list shows the members of the class and its bases (engine included), on locals, parameters,
`this`, call results and members. Inaccessible private/protected members have a padlock. On a raw pointer `.` becomes
`->` by itself; on `TObjectPtr`, `TWeakObjectPtr`, `TSharedPtr`... the list shows both the wrapper's members (`Get`,
`IsValid`) and the pointed object's, and picking one of the latter writes `->`. A member just added in a `.h` is listed
in the `.cpp` before you save.

![Member completion on a TWeakObjectPtr](guide-img/member-completion.svg?v=69c0e2a6)

### Name completion

Wherever you type a name the list shows, in order: locals and parameters, class members, the values of the expected enum
(after `case `, `State = `, or as an enum argument), types/functions/variables/namespaces, macros, keywords.

- After `::`: the scope's names (`EMyEnum::`, `Super::`); in a `.cpp`, `void AMyActor::` lists the functions to define.
- Inside `#include "`: folders and headers.
- After `#if`, `#ifdef`, `#ifndef`, `#elif`, `#undef` and in `defined(`: macros, with the value of build configuration
  ones (`UE_BUILD_SHIPPING = 0`, `WITH_EDITOR = 1`).
- On a dynamic delegate, `.` also offers `AddDynamic`, `RemoveDynamic`, `IsAlreadyBound`.

Picking a name whose header the file does not see **adds the `#include`** (`"GameFramework/Actor.h"`); one Ctrl+Z
removes both. **Ctrl+Space** opens the list anywhere. In `.Build.cs`/`.Target.cs` files it completes module names.

![Name completion and the #include added automatically](guide-img/name-completion.svg?v=96d18712)

### Log and on-screen message templates

At the start of a line type one of these and pick it from the list:

| Type | You get |
|---|---|
| `uelog`, `uewarn`, `ueerror` | `UE_LOG(LogMyGame, Log, TEXT("Message"));` |
| `uelogf` | `UE_LOG(LogMyGame, Log, TEXT("Message: %s"), *Value);` |
| `screenmsg` | `if (GEngine) { GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Yellow, TEXT("Message")); }` |

After a value, `.log`, `.warn`, `.error` or `.screen` prints it with the right format: `Health.log` →
`UE_LOG(LogMyGame, Log, TEXT("Health = %f"), Health);`. The category is one your module declares, else `LogTemp`.
**Tab** / **Shift+Tab** move between the fields, **Enter** ends.

### Warnings and quick fixes

Unreal mistakes are underlined before you compile (missing `.generated.h` or `GENERATED_BODY()`, wrong prefixes, missing
`_Implementation`, UObject pointers without `UPROPERTY`...), plus certain C++ errors (`const` violations, unknown
members, wrong argument count, inaccessible members) and warnings (`if (A = B)`, `UE_LOG` with the wrong number of
`%`). The **light bulb** applies the fix.

![A warning with its light bulb fixes](guide-img/warnings.svg?v=4d162703)

IntelliSense's false errors on Unreal code (a file it could not configure, an outdated `.generated.h`) are hidden; real
errors stay. Option: *IntelliSense errors in Unreal files*.

### Code colours

UnrealSense colours the project's C++ by itself: UObject types apart from value types, enums, functions, fields, locals,
parameters, macros, reflection macros and their specifiers; members exposed to Blueprints in bold. Colours: Tools ›
Options › Environment › Fonts and Colors, entries `UnrealSense - …`.

![UnrealSense code colours with a legend](guide-img/code-colours.svg?v=e78874f1)

## Navigation

### Go to definition

**Ctrl+click**, **F12** or **Alt+G** (or the modifier chosen in Text Editor › General › *Enable mouse click to perform
Go to Definition*): from a call to its implementation, from the implementation to the declaration and back, from a
variable or type to its declaration. On a BlueprintNativeEvent or RPC it goes to `_Implementation`/`_Validate` and back.
Works on unsaved code; where UnrealSense knows nothing, Visual Studio's own runs.

![Ctrl+click on a call opens its implementation](guide-img/go-to-definition.svg?v=819f5a9c)

### Find a symbol or a file

**Alt+Shift+T** searches classes, functions, variables, macros... of the project, plugins and engine; initials are
enough (`GAL` → `GetActorLocation`). **Ctrl+Shift+T** searches files (`File.cpp:120` opens at line 120). **Alt+M**
searches the open file, locals included, grouped by kind (*Group*, Ctrl+G); the buttons under the search box show only
functions, fields, locals... (Alt+1…9, Alt+0 for all). **Ctrl+1…4** switch tab. Words separated by spaces are searched separately;
*Fuzzy* (Ctrl+F) also accepts scattered letters and typos.

![The Go to Symbol window](guide-img/go-to-symbol.svg?v=3cf89217)

### Find Usages

Right-click › **Find Usages** lists where a C++ class, function, variable or macro is used (project, plugins, engine),
grouped by file, with the Blueprints at the bottom. *Declarations*, *Calls*, *Reads*, *Writes* filter the results; uses
inside macros (`UE_LOG`, `AddDynamic`, `ReplicatedUsing`) are found too, and uses whose type is not certain are listed
under **Uncertain**. Double-click opens the line.

### What an #include is used for

Each `#include` line ends with how many times the file uses what that header brings in (*9 uses*). Click it to list
those places; double-click one to go there. *No use found* marks an include the file does not seem to need.

### Switch between .h and .cpp

**Alt+O** opens the matching file, following `Public` ⇄ `Private` (also in engine and plugin files).

![Alt+O between header and source](guide-img/switch-header-source.svg?v=1876e3f9)

### Class hierarchy, base function and overrides

Caret on a class › Quick Menu › **Class Hierarchy...**: its bases and its derived classes as a tree; click to open.
On a virtual function or event, **Base and Overrides of X...** lists the base versions and the overrides in subclasses,
also the Blueprints implementing an event.

![The Class Hierarchy drop-down](guide-img/class-hierarchy.svg?v=c653b93f)

![The Base and Overrides drop-down](guide-img/base-and-overrides.svg?v=7705fecc)

### Unreal Explorer

**Extensions › UnrealSense › Unreal Explorer**: modules (with `.Build.cs`, dependencies and reflected types), plugins,
Blueprints by C++ parent class and config files, with search. Double-click opens the file.

![The Unreal Explorer window](guide-img/unreal-explorer.svg?v=539d6d64)

## Generating code

### Quick Menu

**Alt+Shift+Q** opens, where the mouse is, the commands that make sense at the caret. Type to filter, **Enter** to run.
It also has **Open in Unreal Editor** (starts the editor with the project), **Rebuild UnrealSense Index** and
**Enable/Disable Visual Assist**.

![The Quick Menu](guide-img/quick-menu.svg?v=4fe36168)

### Override virtual functions

Caret inside a class, **Alt+Shift+V** (or the light bulb on the class name): tick the inherited virtual functions to declare them in the `.h` and define them
(calling `Super::`) in the `.cpp`.

![The Override Virtual Functions dialog](guide-img/override.svg?v=a6823f86)

### Create implementation

Caret on a declaration in the `.h`, **Alt+Shift+D** (or the light bulb): the body goes into the `.cpp` next to its
neighbours. RPCs and BlueprintNativeEvents get `_Implementation` (and `_Validate` with `WithValidation`); overrides call
`Super::` when a parent implements it.

![Create Implementation on a Server RPC](guide-img/create-implementation.svg?v=ae37e2e7)

### Declare a delegate

Caret in a header › Quick Menu › **Declare Delegate...**: choose kind, name, return type and parameters with a live
preview; **Create** writes the `DECLARE_..._DELEGATE` macro and the member, **Copy** puts it on the clipboard.
*Usage example* shows how to bind and call it.

![The Declare Delegate dialog](guide-img/declare-delegate.svg?v=3754cd7e)

### Add an #include or a forward declaration

Caret on a name › Quick Menu › **Add #include** (the header written as Unreal does) or, in a header,
**Add Forward Declaration** (`class UMyComponent;`).

![Add #include and Add Forward Declaration](guide-img/add-include.svg?v=7550cf92)

### New Unreal class

Solution Explorer › right-click › **Add › Unreal Class...** (or the Unreal Explorer's **New class**): name, parent class
(**Common** or **All Classes**) and folder, in any module. The files are written like the Unreal Editor's, added to the
project and opened; a missing module dependency can be added to the `.Build.cs`. Includes stay red until **Generate
Project Files**.

![The New Unreal Class dialog](guide-img/new-unreal-class.svg?v=a39675b7)

### Rename a symbol

Caret on a function, variable, local, class or struct, **Alt+Shift+R**: type the new name, pick the options, then
**Preview...** shows every change with a checkbox; **Apply** writes them. Overrides and `_Implementation`/`_Validate`
are renamed too. For Blueprint-visible symbols, tick **Add a CoreRedirect** so saved assets keep working. Open files
can be undone with **Ctrl+Z**; read-only (Perforce) files are made writable and listed at the end for check-out.

### Sort members

In a header, right-click › **Sort Members...** (or the Quick Menu): methods or properties first, then as declared,
alphabetical or by size (fields with less padding), inside each `public`/`protected`/`private` section. Select some members first to sort only those.
Comments above a member move with it. Options: a blank line between members (on by default), merge repeated
sections, reorder the `.cpp` to match, sort the `#include` lines. **Ctrl+Z** undoes it.

### Sort #includes

In a `.h` or `.cpp`, right-click › **Sort #includes** (or the Quick Menu): the `#include` lines in alphabetical order,
group by group (blank lines and `#if` blocks keep their groups). The `.cpp`'s own header, `CoreMinimal.h` and the
`.generated.h` keep their places.

## Settings

**Tools › Options › UnrealSense** (in Visual Studio 2026: *All Settings › UnrealSense*). Every feature can be turned off
there. The ones you may want to change:

![Tools › Options › UnrealSense](guide-img/settings.svg?v=9cdfcf20)

- **Unreal project**: only if UnrealSense picks the wrong project in a solution with several.
- **Show hints for unused Blueprint API**: also labels reflected members no Blueprint uses.
- **UI theme**: `Unreal` (default, Unreal Editor look) or `Classic` (Visual Studio colours).
- **Inspections** section: each group of warnings (Unreal, `TObjectPtr` suggestion, C++ checks, C++ warnings, Error
  List) can be turned off.
- **Include engine symbols and files**: off, Go to Symbol/File search only the project.
- **Show UHT-generated references**: off by default; on, Find Usages also lists `.gen.cpp`/`.generated.h`.
- **Don't wait for Visual Studio's C++ list**: on by default, so the completion list opens without waiting for
  IntelliSense.
- **Use the own C++ index** and **Index the whole engine**: on by default. The index runs as `usindex` in Task Manager
  (about 0.3 GB for a Lyra-sized project, about 3.5 GB with the whole engine); turning the index off also turns off
  completion and Find Usages.

## If something goes wrong

Report problems at [github.com/AndreaDecaro/Unreal-Sense/issues](https://github.com/AndreaDecaro/Unreal-Sense/issues),
with the log if you can (it contains your project paths: check it first).

- **No counters**: wait a few seconds; after saving Blueprints, **Extensions › UnrealSense › Rebuild UnrealSense Index**.
- **A shortcut does nothing**: another extension uses it; the `KeyBindingCheck:` log line says which. Change it in
  Tools › Options › Environment › Keyboard (search `UnrealSense.`).
- **Nothing happens in some files**: UnrealSense works on the open solution's project only (another checkout or game is
  left alone); in a solution with several games pick one in *Unreal project*.
- **All features off, with a bar about the license check**: the license file was unreachable for 48 hours; check the
  network or proxy and press **Check now**.
- **Find All References stopped working**: run **Extensions › UnrealSense › Restore Visual Studio Indexing** if shown,
  then restart.
- **Logs**: `%LOCALAPPDATA%\UnrealSense\Logs`, one `UnrealSense_<date>_<time>_pid<id>.log` per session (last five kept).
  For freezes or crashes also attach the `Hangs_...log` of the same session.

![The UnrealSense Logs folder](guide-img/logs.svg?v=0b866ea7)
