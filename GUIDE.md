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
| **Alt+Shift+H** | Call hierarchy |
| **Alt+click** | Class hierarchy (Shift+click: free; both set in Tools › Options › UnrealSense › General › Mouse clicks) |

Everything else is in **Extensions › UnrealSense** and in the Quick Menu.

Used to Visual Assist's keys? **Extensions › UnrealSense › Import Shortcuts...** (also shown at the very first start) gives
our commands the keys of Visual Assist, or of Visual Studio + Visual Assist, or our defaults: tick the rows, **Apply**.

## Install and update

From the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AndreaDecaro.UnrealSense):
**Extensions › Manage Extensions**, search **UnrealSense**, **Install**, then close Visual Studio to finish.

![Manage Extensions with UnrealSense found on the Marketplace](guide-img/install.svg?v=cd02e551)

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
it does (the bar has **Check now**). If a paid key is ever required, a bar will say so with the days left, and its
**Enter license key** link is where the key goes.

![The information bar shown when a license key is needed](guide-img/license.svg?v=1c19e140)
*This bar appears only if a key is ever needed: today UnrealSense is free.*

## First start

Open the project's solution, or its `.uproject` (Visual Studio 2026): UnrealSense finds the project and reads its C++ and Blueprints in a few seconds (longer the
first time). **View › Output › UnrealSense** shows what it is doing. With a `.slnx` in Visual Studio 2026 a bar at the
top suggests opening the `.uproject` instead: **Close** hides it for now, **Don't show again** for good.

![The Output window on the UnrealSense pane](guide-img/first-start.svg?v=64799b25)

## Blueprints

### Blueprint counters

A grey label next to reflected classes, functions and properties says how many Blueprints use them (`12 derived
Blueprints`, `3 Blueprint usages`), also through parent Blueprints or C++ subclasses ("via …"). **Click it** for the
list. On properties there are two counters, `2 Blueprint usages - 3 Blueprint overrides`: **usages** read or write it in
graphs, **overrides** change its value in Class Defaults, levels or data assets.

![Grey labels with the number of Blueprints next to a class and a function](guide-img/hints.svg?v=841ff8a7)

### Find usages in Blueprints

Caret on a class, function or property, **Alt+Shift+B** (or right-click › **Find Blueprint Usages**). For calls,
events and variable get/set nodes, the **Detail** column says where the node is (`EventGraph › Call Fire`). Double-click
a row to open the asset in the running Unreal Editor (the status bar repeats where to look); the toolbar has *Show in
Explorer* and *Copy reference*.

For a property, the **Value** column shows the value each Blueprint gives it (`600 (from BP_Base)` when inherited,
`C++ default` when unchanged), the values on placed actors in levels (`3 actors: 600, 800, 1200`, World Partition
included) and in data assets. Hovering the property shows the first five values.

![The Blueprint Usages window](guide-img/blueprint-usages.svg?v=442f9225)

**Opening assets in the Unreal Editor** uses its Python Remote Execution: the first time, UnrealSense offers to enable it
in the project (restart the editor afterwards; files not checked out in Perforce are made writable and listed). Without
it, or with the editor closed, the file is shown in Explorer; **Extensions › UnrealSense › Open Blueprints in Unreal
Editor...** asks again.

### Search text inside Blueprints

**Extensions › UnrealSense › Search Text in Blueprints...** (or the Quick Menu) finds any text in Blueprints and data
assets: comments, pin values, variable defaults and tooltips, widget texts, DataTable rows, String Tables; with *Names*
also variable, function, pin and tag names. Each result says where it is (`EventGraph › Call PrintString › pin In
String`). Options: *Match case*, *Whole word*, *All assets* (textures, meshes, sounds...), *Editor caches*.

![The Search in Blueprints window](guide-img/search-in-blueprints.svg?v=30bd5331)

## Writing code

### Specifiers and hover info

Inside `UPROPERTY(...)`, `UFUNCTION(...)`, `UCLASS(...)` etc. the list offers the specifiers of your engine version with
their explanation, and values too (project `Category` names, the function for `ReplicatedUsing`). Hover a specifier for
its documentation, or a reflected name for its Blueprint usages.

![Completion of UPROPERTY specifiers](guide-img/specifiers.svg?v=d7d06db3)

![Tool tips on a specifier and on a class](guide-img/quick-info.svg?v=a57e4bcc)

### Member completion (`.` and `->`)

After `.` or `->` the list shows the members of the class and its bases (engine included), on locals, parameters,
`this`, call results and members. Inaccessible private/protected members have a padlock. On a raw pointer `.` becomes
`->` by itself; on `TObjectPtr`, `TWeakObjectPtr`, `TSharedPtr`... the list shows both the wrapper's members (`Get`,
`IsValid`) and the pointed object's, and picking one of the latter writes `->`. A member just added in a `.h` is listed
in the `.cpp` before you save.

![Member completion on a TWeakObjectPtr](guide-img/member-completion.svg?v=0f8fc320)

### Name completion

Wherever you type a name the list shows, in order: locals and parameters, class members, the values of the expected enum
(after `case `, `State = `, or as an enum argument), types/functions/variables/namespaces, macros, keywords.

- After `::`: the scope's names (`EMyEnum::`, `Super::`); in a `.cpp`, `void AMyActor::` lists the functions to define.
- Inside `#include "`: folders and headers.
- After `#if`, `#ifdef`, `#ifndef`, `#elif`, `#undef` and in `defined(`: macros, with the value of build configuration
  ones (`UE_BUILD_SHIPPING = 0`, `WITH_EDITOR = 1`).
- On a dynamic delegate, `.` also offers `AddDynamic`, `RemoveDynamic`, `IsAlreadyBound`.
- Inside `UE_LOG(`: the log categories (your module's first), then the verbosities.
- Inside `FindConsoleVariable(TEXT("`: console variable names; hover one for its type, default and help.

Picking a name whose header the file does not see **adds the `#include`** (`"GameFramework/Actor.h"`); one Ctrl+Z
removes both. In a header, a small list asks first: **#include** (Enter) or **forward declaration** (`2`); Esc adds
nothing. An `#include` that would be an absolute path is only added if you confirm it. To never add one, turn off **Code completion › Add missing
#include**. **Ctrl+Space** opens the list anywhere. In `.Build.cs`/`.Target.cs` files it completes module names.

![Name completion and the #include added automatically](guide-img/name-completion.svg?v=b62b2689)

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

### Loop and switch templates

After a value the list also offers the loops that fit its type:

| Type | You get |
|---|---|
| `Targets.foreach` | `for (AActor* Actor : Targets) { }` |
| `Targets.for`, `Targets.forr` | `for (int32 Index = 0; Index < Targets.Num(); ++Index)`, or from the last to the first |
| `Targets.forit` | `for (auto It = Targets.CreateIterator(); It; ++It)` (remove with `It.RemoveCurrent()`) |
| `Node.while` | `while (Node) { }` |
| `State.switch` | `switch (State)` with a `case ...: break;` for every value of the enum |

In an existing `switch`, Quick Menu › **Generate Missing Cases** adds the values that have no `case` yet.

### .ini, .uproject and .uplugin files

In `.ini` files the list completes `[/Script/Module.Class]` sections, the class's `Config` properties as keys, and their
`true`/`false` or enum values; hover a key for its C++ declaration, **F12** opens it. In `.uproject`/`.uplugin` it
completes the descriptor fields, module types, loading phases, module and plugin names.

### Warnings and quick fixes

Unreal mistakes are underlined before you compile (missing `.generated.h` or `GENERATED_BODY()`, wrong prefixes, missing
`_Implementation`, UObject pointers without `UPROPERTY`...), plus certain C++ errors (`const` violations, unknown
members, wrong argument count, inaccessible members) and warnings (`if (A = B)`, `UE_LOG` with the wrong number of
`%`). The **light bulb** applies the fix.

Also checked: an `#include` from a module your `.Build.cs` does not list (the fix adds it to the `.Build.cs`), an
override of `BeginPlay`, `Tick`, `EndPlay`... that never calls `Super::`, a function bound to a delegate with the wrong
signature or, with `AddDynamic`, without `UFUNCTION`, a replicated property missing from `GetLifetimeReplicatedProps`
(the fix adds its `DOREPLIFETIME`), and a log category nothing declares or defines.

![A warning with its light bulb fixes](guide-img/warnings.svg?v=6519b628)

IntelliSense's false errors on Unreal code (a file it could not configure, an outdated `.generated.h`) are hidden; real
errors stay. Option: *IntelliSense errors in Unreal files*.

### Code colours

UnrealSense colours the project's C++ by itself: UObject types apart from value types, enums, functions, fields, locals,
parameters, macros, reflection macros and their specifiers; members exposed to Blueprints in bold. Colours: Tools ›
Options › Environment › Fonts and Colors, entries `UnrealSense - …`.

![UnrealSense code colours with a legend](guide-img/code-colours.svg?v=6e21c77f)

## Navigation

### Go to definition

**Ctrl+click**, **F12** or **Alt+G** (or the modifier chosen in Text Editor › General › *Enable mouse click to perform
Go to Definition*): from a call to its implementation, from the implementation to the declaration and back, from a
variable or type to its declaration. On a BlueprintNativeEvent or RPC it goes to `_Implementation`/`_Validate` and back.
Works on unsaved code; where UnrealSense knows nothing, Visual Studio's own runs.

![Ctrl+click on a call opens its implementation](guide-img/go-to-definition.svg?v=f634b4ac)

### Find a symbol or a file

**Alt+Shift+T** searches classes, functions, variables, macros... of the project, plugins and engine; initials are
enough (`GAL` → `GetActorLocation`). **Ctrl+Shift+T** searches files (`File.cpp:120` opens at line 120). **Alt+M**
searches the open file, locals included, grouped by kind (*Group*, Ctrl+G); the buttons under the search box show only
functions, fields, locals... (Alt+1…9, Alt+0 for all). **Ctrl+1…4** switch tab. Words separated by spaces are searched separately;
*Fuzzy* (Ctrl+F) also accepts scattered letters and typos.
Go to Symbol also finds log categories and console variables by their console name (`r.shadow`).

![The Go to Symbol window](guide-img/go-to-symbol.svg?v=a4778b8b)

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

![Alt+O between header and source](guide-img/switch-header-source.svg?v=85c2a199)

### Class hierarchy, base function and overrides

**Alt+click** a class (or caret on it › Quick Menu › **Class Hierarchy...**): its bases and its derived classes as a tree; click to open.
On a virtual function or event, **Base and Overrides of X...** lists the base versions and the overrides in subclasses,
also the Blueprints implementing an event.

![The Class Hierarchy drop-down](guide-img/class-hierarchy.svg?v=8b054d44)

![The Base and Overrides drop-down](guide-img/base-and-overrides.svg?v=e2c0904b)

### Call hierarchy

Caret on a function, **Alt+Shift+H** (or right-click › **Call Hierarchy...**): who calls it, each caller expandable to
its own callers. Blueprints calling a `UFUNCTION` are listed too (double-click opens them in the Unreal Editor).
*Calls from* shows what the function calls instead. Double-click a row to go to the call.

### Unreal Explorer

**Extensions › UnrealSense › Unreal Explorer**: modules (with `.Build.cs`, dependencies and reflected types), plugins,
Blueprints by C++ parent class and config files, with search. Double-click opens the file.

![The Unreal Explorer window](guide-img/unreal-explorer.svg?v=794d5c6a)

## Generating code

### Quick Menu

**Alt+Shift+Q** opens, where the mouse is, the commands that make sense at the caret. Type to filter, **Enter** to run.
It also has **Open in Unreal Editor** (starts the editor with the project) and **Rebuild UnrealSense Index**.
When a command cannot do what you asked, a small popup under the caret says why (**Esc** or a click closes it).

![The Quick Menu](guide-img/quick-menu.svg?v=7e903278)

### Override virtual functions

Caret inside a class, **Alt+Shift+V** (or the light bulb on the class name): tick the inherited virtual functions to declare them in the `.h` and define them
(calling `Super::`) in the `.cpp`.

![The Override Virtual Functions dialog](guide-img/override.svg?v=0c798596)

### Create implementation

Caret on a declaration in the `.h`, **Alt+Shift+D** (or the light bulb): the body goes into the `.cpp` next to its
neighbours. RPCs and BlueprintNativeEvents get `_Implementation` (and `_Validate` with `WithValidation`); overrides call
`Super::` when a parent implements it.

![Create Implementation on a Server RPC](guide-img/create-implementation.svg?v=e0b6f77c)

### Generate getters/setters and constructor

Caret in a class of a header › right-click (or the Quick Menu, or the light bulb on the class name) ›
**Generate Getters/Setters...**: tick the members, choose inline in the `.h` or declared there and defined in the
`.cpp`. Unreal names and types: `bDead` gives `IsDead()`/`SetDead(bool)`, a `TObjectPtr<T>` is returned as `T*`,
`FString` and containers as `const&`. Members that already have them are not listed.

**Generate Constructor** declares the constructor if the class has none and fills its body in the `.cpp`: tick enabled
when the class overrides `Tick`, and `CreateDefaultSubobject` for each component `UPROPERTY` (the first one becomes the
root, the others attach to it), with their `#include`s. Run it again after adding components: only what is missing is added.

### Declare a delegate

Caret in a header › Quick Menu › **Declare Delegate...**: choose kind, name, return type and parameters with a live
preview; **Create** writes the `DECLARE_..._DELEGATE` macro and the member, **Copy** puts it on the clipboard.
*Usage example* shows how to bind and call it.

![The Declare Delegate dialog](guide-img/declare-delegate.svg?v=0f8bdd2c)

### Add an #include or a forward declaration

Caret on a name › Quick Menu › **Add #include** (the header written as Unreal does) or, in a header,
**Add Forward Declaration** (`class UMyComponent;`).

![Add #include and Add Forward Declaration](guide-img/add-include.svg?v=23145c13)

### New Unreal class

Solution Explorer › right-click › **Add › Unreal Class...** (or the Unreal Explorer's **New class**): name, parent class
(**Common** or **All Classes**) and folder, in any module. The files are written like the Unreal Editor's, added to the
project and opened; a missing module dependency can be added to the `.Build.cs`. Includes stay red until **Generate
Project Files**. With a `.uproject` opened instead of a solution (Extensions › UnrealSense › **Unreal Class...**) the
files are only created and opened: nothing to regenerate.

![The New Unreal Class dialog](guide-img/new-unreal-class.svg?v=89fecc85)

### Rename a symbol

Caret on a function, variable, local, class or struct, **Alt+Shift+R**: type the new name, pick the options, then
**Preview...** shows every change with a checkbox; **Apply** writes them. Overrides and `_Implementation`/`_Validate`
are renamed too. For Blueprint-visible symbols, tick **Add a CoreRedirect** so saved assets keep working. Open files
can be undone with **Ctrl+Z**; read-only (Perforce) files are made writable and listed at the end for check-out.

### Introduce a variable or extract a function

Select an expression (or put the caret on a call) and pick **Introduce Variable** in the right-click menu, the Quick
Menu or the light bulb: `GetOwner()` becomes `AActor* Owner = GetOwner();` on the line before, with the type and a
name filled in and selected for a rename. If the same expression appears again in the function, you can replace all of
them.

In a `.cpp`, select whole statements and pick **Extract Method...**: type the name, check the signature, **Extract**.
The statements move to a new function (declared in the class's header when it is a member) and are replaced by a call;
the variables they need become parameters, the one they produce becomes the return value. **Ctrl+Z** undoes it.

### Sort members

In a header, right-click › **Sort Members...** (or the Quick Menu): methods or properties first, then as declared,
alphabetical or by size (fields with less padding), inside each `public`/`protected`/`private` section. Select some members first to sort only those.
Comments above a member move with it. Options: a blank line between members (on by default), merge repeated
sections, reorder the `.cpp` to match, sort the `#include` lines. **Ctrl+Z** undoes it.

### Sort #includes

In a `.h` or `.cpp`, right-click › **Sort #includes** (or the Quick Menu): the `#include` lines in alphabetical order,
group by group (blank lines and `#if` blocks keep their groups). The `.cpp`'s own header, `CoreMinimal.h` and the
`.generated.h` keep their places.

### Remove Unused #includes

In a `.h` or `.cpp`, right-click › **Remove Unused #includes...** (or the Quick Menu): the `#include` lines that can go
are ticked, the others are greyed out with the reason they stay. Press **Remove**; **Ctrl+Z** undoes it. On an
`#include` whose hint says *no use found*, the light bulb (**Ctrl+.**) offers **Remove unused #include** when it is safe.
Build once afterwards: other configurations (`#if` blocks) are not checked.

## Settings

**Tools › Options › UnrealSense** (in Visual Studio 2026: *All Settings › UnrealSense*). Every feature can be turned off
there. The ones you may want to change:

![Tools › Options › UnrealSense](guide-img/settings.svg?v=436dfeec)

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
- **A shortcut does nothing**: another extension uses it; the `KeyBindingCheck:` log line says which. **Import
  Shortcuts...** › *UnrealSense defaults* puts ours back, or change it in Tools › Options › Environment › Keyboard (search `UnrealSense.`).
- **Nothing happens in some files**: UnrealSense works on the open solution's project only (another checkout or game is
  left alone); in a solution with several games pick one in *Unreal project*.
- **All features off, with a bar about the license check**: the license file was unreachable for 48 hours; check the
  network or proxy and press **Check now**.
- **Find All References stopped working**: run **Extensions › UnrealSense › Restore Visual Studio Indexing** if shown,
  then restart.
- **Logs**: `%LOCALAPPDATA%\UnrealSense\Logs`, one `UnrealSense_<date>_<time>_pid<id>.log` per session (last five kept).
  For freezes or crashes also attach the `Hangs_...log` of the same session.

![The UnrealSense Logs folder](guide-img/logs.svg?v=ff4b7bf3)
