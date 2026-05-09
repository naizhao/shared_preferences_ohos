# Fork Info

This repository is a single-package fork of `shared_preferences_ohos`, lifted out
of the OpenHarmony TPC monorepo at:

- **Upstream repo:** https://gitcode.com/openharmony-tpc/flutter_packages
- **Upstream branch:** `br_shared_preferences-v2.5.3_ohos`
- **Upstream subtree path:** `packages/shared_preferences/shared_preferences_ohos/`
- **Imported at upstream commit:** `23594043b9794e28bb770219cd49950a50e9e9d0`
  (subject: *"Fix preferences to get values in a synchronous way"*)
- **Branch HEAD when imported:** `72cfb7ff3520778aaa35b40bb13b11b2f08140ab`

## Why This Fork Exists

The upstream `shared_preferences_ohos` (both v2.5.3 and v2.5.4 branches) ships
a contract mismatch between the Dart pigeon client and the ArkTS native plugin
that crashes the app on every set/return-bool call:

- **Pigeon spec** (`pigeons/messages.dart`): `setBool`, `setString`, `setInt`,
  `setDouble`, `setEncodedStringList`, `clear`, `remove` — all declared
  `Future<bool>`.
- **Dart client** (`lib/src/messages.g.dart`): expects `replyList[0]` to be the
  bool return value.
- **ArkTS native handler**
  (`ohos/src/main/ets/shared_preferences/SharedPreferencesOhosPlugin.ets`):
  on success replies with an empty `Array<Object> = []` rather than `[true]`.

Result on every `setBool` etc.:

```
RangeError (length): Invalid value: Valid value range is empty: 0
#0  _Array.[] (dart:core-patch/array.dart)
#1  SharedPreferencesApi.setBool (package:shared_preferences_ohos/src/messages.g.dart:107:35)
```

This fork is the place where that bug (and any follow-up issues) gets fixed.
