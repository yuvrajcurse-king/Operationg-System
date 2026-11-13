# NOVA Engine v0.4 - Mobile (Android / iOS)

Phone version of the NOVA 2D engine/editor, written in Dart + Flutter.
Edit scenes with touch, press **Play**, and the game runs live inside the app.

## Honest status

**Verified (in the sandbox where this was built)**
- Native C++ runtime from v0.3 builds with g++ and passes all 37 tests.
- The new Dart simulation (`lib/core/runtime/sim.dart`) was first mirrored
  in Python and compared with the native runtime: dynamic body positions and
  per-emitter particle counts match exactly at frames 1, 60, 120 and 240.
  Those numbers are pinned in `test/sim_test.dart`.

**NOT verified**
- No Dart/Flutter SDK was available, so **nothing was compiled, analyzed or
  run**, same as v0.3. I reviewed every Dart file line by line and found no
  definite compile error, but expect a few small ones on the first build.
  Run `flutter analyze` and fix what it reports. Never tested on a device.

## Build and run

```
flutter create . --platforms=android,ios --project-name nova_engine
flutter pub get
flutter analyze
flutter test
flutter run            # with a phone or emulator connected
```
(`flutter create .` only adds the android/ and ios/ folders; it keeps lib/.)

## What changed from v0.3 (desktop)

| Problem on a phone | Fix |
|---|---|
| Play launched `nova_runtime` as a child process - impossible on Android/iOS | Dart port of physics + particles; Play runs in-app at 60 Hz (`GameScreen`) |
| Desktop 3-panel layout, splitters, long toolbar | One tab at a time (Scene / Objects / Inspector / Assets / Console) + bottom bar, app-bar menu |
| Mouse-only viewport (right-drag, wheel) | Tap select, drag move, drag empty = pan, pinch zoom |
| Right-click menu, drag-and-drop reparent | Long-press menu, "Set parent..." dialog, eye toggle |
| Projects in `$HOME` (does not exist on phones) | `path_provider` documents folder; projects are discovered by scanning it |
| Typing folder paths to open/create projects | Project list, "New project" button, delete |
| Import asset by typing a file path | Removed (no sprites yet; see roadmap) |
| Ctrl shortcuts, tiny 16 px targets | Removed; 44-48 dp targets |
| Data lost if the OS kills the app | Auto-save when the app goes to the background |
| `ThemeData(inputDecorationTheme:)` (type being migrated by Flutter) | Per-field `novaInput()` decoration |
| Stale "v0.1" labels | Updated |

The desktop editor + native runtime are not part of this build; use the v0.3
archive for them. `native/` is kept as the reference the Dart port is tested
against.

## Play mode notes
- Camera: first active Camera object (zoom, backdrop). The 800x600 reference
  frame is scaled to fit the screen; extra world area is visible too.
- Same limits as the native runtime: AABB physics (static vs root-level
  dynamic bodies), no input, audio, scripting, sprites or export.
- Needs 64-bit ints (Android/iOS/desktop). Not for Flutter web.

## Roadmap (not done)
Sprites/textures + file picker, touch input events, scripting, animation,
export/share project, Android/iOS app icons and store packaging.

## Layout
```
lib/core/runtime/sim.dart        Dart port of the native simulation
lib/editor/ui/game_screen.dart   Play mode + painter
lib/editor/ui/editor_screen.dart phone shell
lib/editor/ui/viewport_2d.dart   touch scene view
lib/editor/ui/hierarchy_panel.dart, inspector_panel.dart, asset_browser.dart, ...
test/sim_test.dart               parity + behaviour tests
```
