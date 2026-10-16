# UnrealSense changelog

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
