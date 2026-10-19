# UnrealSense changelog

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
