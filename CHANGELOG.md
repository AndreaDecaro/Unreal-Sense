# UnrealSense changelog

## 1.1.0

UnrealSense 1.1.0: all changes from 1.0.26 to 1.1.0 (1.0.27 and 1.0.28 were internal builds, never released).

Call Hierarchy
- Alt+Shift+H (also the editor context menu, the Quick Menu on a function and Extensions > UnrealSense) opens the Call Hierarchy window on the function at the caret: who calls it, each caller expandable to its own callers. Each row shows the function, its class, file:line and the call line (x3 when it calls more than once). Calls through a base virtual function or a Blueprint event are marked "possible via ..."; recursion is shown once. A UFUNCTION called from Blueprints lists those Blueprints too: double-click opens one in the Unreal Editor. "Calls from" switches to what the function calls. Needs the own C++ index. Log lines start with "CallHierarchy:".

Log categories and console variables
- Inside UE_LOG, UE_LOGF, UE_LOGFMT and UE_CLOG the list offers the log categories (your module's first, then the project's, plugins' and engine's), adding the #include when needed, then the verbosities. Go to definition on a category goes to its declaration and from there to its DEFINE_LOG_CATEGORY.
- Warnings: a category nothing declares, or another .cpp's static one (CPP0221, fix: define a static category here); a DECLARE_LOG_CATEGORY_EXTERN with no DEFINE_LOG_CATEGORY in its module (CPP0222, fix: adds it). Log lines start with "LogCategories:".
- Console variables and commands registered in C++ are found by their console name in Go to Symbol (r.shadow), with type, default and help. Hovering a string naming one shows them; go to definition opens its declaration; FindConsoleVariable(TEXT(" and the [ConsoleVariables]/[SystemSettings] keys of .ini files complete them. Log lines start with "CVars:".

Config and descriptor files
- .ini files: [/Script/ completes modules and their classes, keys complete the class's Config properties (inherited ones too, after +/-/./! prefixes), values of bool and enum properties. Quick info on a key shows its C++ declaration; F12 opens the class or property.
- .uproject/.uplugin: descriptor fields, module Type and LoadingPhase, module names from Source, plugin names, platforms.
- Option: Code completion > Completion in .ini, .uproject and .uplugin. Log lines start with "ConfigCompletion:".

New inspections
- Missing module dependency (CPP0201): an #include of a header from a module the file's module cannot reach through its .Build.cs. The light bulb adds the module to the .Build.cs (Private, or Public from a public header). Option: Inspections > Module dependencies missing from the Build.cs. Log lines start with "BuildDeps:".
- A lifecycle override (BeginPlay, EndPlay, Tick, PostInitializeComponents, NativeConstruct...) that never calls its parent (CPP0231; fix adds the Super:: call).
- A function bound to a delegate whose signature differs from the delegate's (CPP0232), and AddDynamic/BindDynamic on a function that is not a UFUNCTION (CPP0233; fix adds UFUNCTION()).
- Replication: a Replicated/ReplicatedUsing property that GetLifetimeReplicatedProps never registers (fix adds its DOREPLIFETIME); the missing GetLifetimeReplicatedProps fix writes the whole definition; the missing OnRep fix declares the UFUNCTION and writes its body.

Shortcuts
- The commands that act on the code at the caret are bound in the Text Editor scope, so they win over other extensions' Global keys while you type.
- Extensions > UnrealSense > Import Shortcuts... (also offered at the very first start) sets our keys from a preset with a preview: UnrealSense defaults, Visual Assist (customized keys included, also when Visual Assist is disabled), or Visual Studio + Visual Assist.
- Keys found empty after an update are repaired once, keeping a command's other keys; assignment works on localized Visual Studio.

Other changes
- Completion's automatic #include asks, in a header, between #include and forward declaration; an absolute-path #include is added only after confirming.
- The own index follows files changed on disk outside Visual Studio (Perforce or git sync, branch switch): one update about 3 seconds after the changes stop.
- Less repeated work: batched index rebuilds, one parse per analysis, no indexer requests for unchanged text.
- Templates: one Tab commits the list open in a template field and moves to the next field.
- Quick fixes write the line endings of the file they edit.
- The Enable/Disable Visual Assist command is no longer in the menus.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.26

UnrealSense 1.0.26: all changes from 1.0.23 to 1.0.26 (1.0.24 and 1.0.25 were internal builds, never released).

Rename Symbol
- Alt+Shift+R (also the editor context menu and the Quick Menu) renames the function, variable, local, parameter, class, struct or enum at the caret everywhere the own C++ index resolves it: the whole override tree of a virtual function, the _Implementation/_Validate of a BlueprintNativeEvent or RPC, a class's constructors and destructor. Locals and parameters are renamed in the open file, keeping same-named locals of other scopes apart. Symbols declared in the engine are refused.
- The dialog checks the new name as you type (C++ identifier, keyword, Unreal prefix, b for bools) and offers a CoreRedirect in Config/DefaultEngine.ini for reflected types and members (on by default when Blueprints use them), renaming the class's .h/.cpp with their #include lines, renaming in comments and strings, and editing open documents (undoable) or opening every changed file.
- Preview lists every change by file with a checkbox each; uncertain places are listed unchecked with the reason. Read-only (Perforce) files are made writable and listed at the end. Log lines start with "Rename:".

Sort Members
- Header context menu or Quick Menu (no shortcut): orders the members of every class in the header, or only the selected ones: methods or properties first, then, among themselves, as declared, alphabetical, or by size to avoid padding (properties by decreasing alignment, estimated from their type; static members last).
- Members stay in their access section; comments right above a member, its UPROPERTY/UFUNCTION line and trailing comment move with it; an #if block moves as one piece; nested types, GENERATED_BODY, DECLARE_* macros and comments followed by a blank line stay where they are.
- Options in the dialog (saved under Tools > Options > UnrealSense > Sort Members): a blank line between members (on by default), merge repeated public/protected/private sections (with a selection too, for the sections it holds whole), reorder the definitions in the matching .cpp, also sort the #include lines.
- Every result is checked to hold the same code before it is applied; only the lines that move are changed (clean diffs in reviews), and one Ctrl+Z undoes it. Log lines start with "SortMembers:".

Sort #includes
- Context menu of .h and .cpp files, Quick Menu: alphabetical order, each run of consecutive includes on its own, keeping Unreal's order (own header first in a .cpp, CoreMinimal.h at the top, .generated.h last, <system> includes after quoted ones). With a selection only the selected lines. Log lines start with "SortIncludes:".

Include hints
- At the end of each #include line, how many times the file uses what that header brings in ("9 uses", or "no use found"): types, functions, variables, enum values and macros resolved through it. Click the hint to list those places in the Find Usages window. Needs the own C++ index. Option: Find Usages (own index) > Show include hints. Log lines start with "IncludeHints:".

Blueprints
- Property hints count graph usages and overrides apart, and the Blueprint Usages window shows the values: each Blueprint's class default (or what it inherits, or the C++ default), values set on actors placed in levels (World Partition included) and in data assets.
- The Detail column of Blueprint Usages names the graph and node of each call, event and variable get/set ("EventGraph > Call Fire").

Go to
- Go to Symbol in File (Alt+M) groups the symbols by kind (types, functions, fields, variables, enum values, macros, parameters, locals; "Group" checkbox, Ctrl+G, option "Group In File results by kind"). Buttons under the search box show one or more kinds only, with their counts (Alt+1...9, Alt+0 for all).

Other changes
- Completion after #if, #ifdef, #ifndef, #elif, #undef and in defined(): macro names, with the value of the build configuration ones.
- Dialogs and the Go to window open centred on the Visual Studio window you are working in, on its monitor, instead of always on the main window's.
- The What's new and update dialogs show readable version headings.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.23

UnrealSense 1.0.23: all changes from 1.0.22 to 1.0.23.

Updates
- Visual Studio looks for extension updates only now and then, so UnrealSense now checks the latest release itself (the update feed on GitHub Pages) every time Visual Studio starts. When a newer version exists, the "UnrealSense update available" window offers Open Manage Extensions (where Visual Studio installs it when closed), Later (asks again at the next start) and Skip this version (no reminder until a newer version comes out), plus a link to download the .vsix directly when Manage Extensions does not list it yet.
- While Visual Studio stays open the check runs every hour: a release published meanwhile shows an info bar with the same choices instead of the window. Each version is offered once per session.
- Option: Tools > Options > UnrealSense > Updates > Tell me when an update is available (on by default). Log lines start with "Update:".

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.22

UnrealSense 1.0.22: all changes from 1.0.21 to 1.0.22.

Navigation
- Go to definition on a BlueprintNativeEvent or RPC UFUNCTION (its declaration or a call) goes to its Name_Implementation (and Name_Validate with WithValidation) in the .cpp, and from Name_Implementation/Name_Validate back to the UFUNCTION, now for engine and plugin classes too (the own C++ index knows them; before, only the project's own modules were resolved and the click could land on UHT's .gen.cpp thunk).

Install
- UnrealSense is published to the Visual Studio Marketplace with each release.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.21

UnrealSense 1.0.21: all changes from 1.0.20 to 1.0.21.

Code templates
- Live templates: at the start of a statement the name list offers uelog, uewarn, ueerror (UE_LOG with verbosity Log, Warning, Error), uelogf (UE_LOG with a %s argument) and screenmsg (if (GEngine) GEngine->AddOnScreenDebugMessage(...), adding #include "Engine/Engine.h" when the file lacks it).
- Postfix templates: after a value, .log, .warn, .error and .screen (at the bottom of the member list) print it with the format its type needs: %d for int32, %lld for int64, %f for float, true/false for a bool, *Name for an FString, *X.ToString() for FName/FVector/FRotator, the object's name for a UObject pointer, Num() for an array. An unknown type leaves the argument as a field to check.
- The log category starts as one the module declares that the file already includes (or a DEFINE_LOG_CATEGORY_STATIC of the same file), otherwise LogTemp; no include is added for it by itself. A category picked from the list in the field brings its #include.
- The fields are boxed: Tab / Shift+Tab move between them, Enter ends the edit, Esc leaves the caret where it is, one Ctrl+Z removes everything.
- Option: Code completion › Code templates (uelog, screenmsg, .log), on by default.

What's new
- The first time Visual Studio starts after an update, a small "What's new in UnrealSense" window sums up the important changes since the version you had, once (minor fixes in one short line; Full changelog opens all the details). Not shown on a fresh install.

Guide and install
- The user guide (GUIDE.html) has the Unreal Editor look and an illustration in every section.
- UnrealSense is on the Visual Studio Marketplace (Extensions › Manage Extensions, search for UnrealSense); the .vsix and Install-UnrealSense.ps1 stay on the GitHub releases for offline PCs. Problems and requests go to GitHub issues.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.20

UnrealSense 1.0.20: all changes from 1.0.19 to 1.0.20.

License
- UnrealSense must now reach its small signed license file at least once every 48 hours (directly or through your proxy). A single failed check changes nothing; past 48 hours without a successful check, the features turn off until one succeeds, even with a license key. The info bar offers "Check now", and the project reopens as soon as a check succeeds.
- The License... dialog shows the last successful check and the offline limit.
- The project moved to the GitHub account AndreaDecaro: the extension reads the license file from the new addresses first. If you added the old update gallery address, replace it with https://andreadecaro.github.io/Unreal-Sense/gallery/atom.xml.

Guide
- The user guide is now in English (GUIDE.html); the old GUIDA.html address opens it.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.19

UnrealSense 1.0.19: all changes from 1.0.12 to 1.0.19.

License and updates
- UnrealSense is free. The license agreement (LICENSE.txt, shown on install) lets the author require a paid license key in the future, also for installed copies. To know, the extension reads a small signed public file about every 12 hours; nothing about you, your PC or your projects is sent, and without network it keeps working.
- The same file can ask a version with a security problem to be updated: an info bar links the new release, and after the announced date that version's features stay off until it is updated.
- New command Extensions › UnrealSense › License... (also in the Quick Menu): license state and key field. Log lines start with "License:".

Completion and code checks
- Name completion no longer waits for Visual Studio's C++ list: where UnrealSense has names, its list shows as soon as the own index answers (option Code completion › Don't wait for Visual Studio's C++ list).
- New check: access to a private or protected member from outside the class.
- Faster lists while typing in a header with very large projects.

Index and performance
- Saving a file that declares nothing new reuses the previous symbol table: Lyra with the whole engine about 1 s instead of 2 s.
- Less memory between index builds (Lyra + engine: 2.4 GB instead of up to 3.7 GB).

Settings and install
- Visual Studio 2026: the options are on the new Settings page (Unified Settings).
- Automatic updates: add https://andreadecaro.github.io/Unreal-Sense/gallery/atom.xml under Tools › Options › Environment › Extensions › Additional Extension Galleries.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.12

UnrealSense 1.0.12: all changes from 1.0.0 to 1.0.12.

New code checks
- C++ checks as you type: writing to a const variable or object, calling a member that doesn't exist, a call with the wrong number of arguments. Warnings for "if (A = B)", a stray ";" after if/for/while, "X = X;" and a UE_LOG whose % count doesn't match its arguments. Each check can be turned on or off in the options.
- New error: a UObject pointer member without UPROPERTY (invisible to the garbage collector), with quick fixes (UPROPERTY() + TObjectPtr, or TWeakObjectPtr).
- Separate squiggle colours: red errors, yellow warnings, blue suggestions (editable in Fonts and Colors).

Completion and search
- Member completion respects friend declarations; members you can't access are shown with a lock.
- Members you add to a header are known right away in other files.
- Go to File and the other searches: each word matches on its own, fuzzy search on by default.

Stability and performance
- Saving a file no longer freezes Visual Studio.
- The index is no longer rebuilt every time you pause typing; index files are written only when VS is idle.
- Go to File no longer gets stuck on "Building..." when the index build fails.
- More detailed hang reports, for easier diagnosis.

Fixes
- Popups placed correctly on multi-monitor setups with different DPI.
- Quick Menu: a word selected by double-click is used as the whole word.
- Blueprint hints open the usages window when you press the key, not when you release it.
- New Unreal Class no longer adds engine-module files to UE5.vcxproj.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.10

UnrealSense 1.0.10: hang diagnostics.

- Hang log: when Visual Studio stays blocked for 5 seconds or more, each report in Hangs_<session>.log now includes the UI thread's call stack, so a freeze inside Visual Studio itself or another extension (for example the first save after opening a large solution) shows where it is stuck.

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.

## 1.0.1

UnrealSense 1.0.1: fixes.

- Go to File/Symbol: a go-to index build that hit an unreadable or vanished file no longer stays on "Building…" forever; such files are skipped, and a failure is shown in the window and logged with its cause (the log no longer calls it a Blueprint index failure).
- Blueprint hints: clicking "N Blueprint usages" opens the Blueprint Usages window again on machines where the click never reached it.
- Quick Menu: after a double-click on a symbol, Find Usages, Find Blueprint Usages and the other symbol commands show again (Class/Function Hierarchy too).
- Errors in the log name the exception that was thrown instead of "One or more errors occurred".

Install: run Install-UnrealSense.ps1 next to the .vsix, or open the .vsix.
