# My Daily Life 🌸

A simple, pastel (lavender · pink · mint) daily routine app for Android.
Kotlin + Jetpack Compose · package `com.mydailylife.routine` · Android 8.0+ · no account, no internet permission.

> **Build status, please read:** the source project is complete, but the APK has **not** been built or
> run on a phone yet. The workspace it was written in could not reach Google's servers (needed to
> download the Android SDK and Compose libraries). What *was* verified: all progress / daily-reset /
> task / routine logic compiles and passes 21 unit tests. The Compose screens, storage and photo code
> are written to standard APIs but have not been compiled. Build once using one of the options below
> (≈10 minutes), then walk through the test checklist at the bottom.

---

## 1. Get the APK

### Option A: GitHub builds it for you (no Android Studio needed)
1. Create a free account at github.com and make a **new private repository**.
2. Upload the whole project folder contents (use `git push`, or GitHub Desktop, or *Add file → Upload files*).
   Make sure the hidden folder `.github/workflows/build-apk.yml` is included.
3. Open the repo's **Actions** tab → **Build My Daily Life APK** → *Run workflow* (it also runs on every push).
4. When the run turns green, open it and download the **MyDailyLife-apk** artifact (a zip containing `MyDailyLife.apk`).
   The workflow runs the unit tests, builds the release APK, signs it with the key in `signing/`, and verifies the signature.

### Option B: Android Studio
1. Install Android Studio (Ladybug or newer), choose **Open**, and select this folder.
2. Wait for the Gradle sync (needs internet the first time).
3. **Build → Generate App Bundles or APKs → Generate APKs**, or run in a terminal:
   `./gradlew assembleRelease`
4. The signed APK is at `app/build/outputs/apk/release/app-release.apk`.

To test quickly on a phone with USB debugging, press ▶ Run in Android Studio instead.

## 2. Install on your phone
1. Copy `MyDailyLife.apk` to the phone (USB cable, Google Drive, WhatsApp "message yourself", email…).
2. Tap the file. If Android says *"For your security, your phone isn't allowed to install unknown apps from this source"*,
   tap **Settings**, switch on **Allow from this source**, go back and tap the file again.
3. Tap **Install**. If Google Play Protect shows *"Unknown developer / app not scanned"*, choose
   **More details → Install anyway** (the app is not from the Play Store).
4. Open **My Daily Life**, enter your name, optionally add a photo, and tap **Get started**.

Updates: install a newer APK over the old one. This works as long as it is signed with the same key (the one in `signing/`).

## 3. What the app does

| # | Feature | Where |
|---|---------|-------|
| 1 | Name + profile picture, dashboard says "Hello, *name*" | Welcome page on first launch; **Me** tab to change |
| 2 | Breakfast, lunch, dinner + 8-glass water tracker | **Today → Meals** |
| 3 | Morning (wash face, moisturizer, sunscreen) and night skincare | **Today → Skincare** |
| 4 | Walking, exercise, sleep schedule, self-care | **Today → Healthy habits** |
| 5 | Add / edit / delete / complete tasks | **Tasks** tab (pencil ✎ → edit or delete) |
| 6 | Done count, percentage ring, last-7-days bars | Top of **Today** |
| 7 | Fresh checklist every day, custom routines kept | Automatic (see below) |
| 8 | Everything saved on the device | Private app storage |
| 9 | Lavender / pink / mint, rounded cards, emoji icons | Whole app |
| 10 | No account, no internet | The manifest declares **no permissions at all** |

Tap a row to tick it. Tap **✎** on a routine row to rename or delete it, or **＋ Add** to add your own item to that group
(for example "Read 10 pages" under Healthy habits, or change "Sleep by 10:30 PM" to your own bedtime).

**Daily reset.** Each time the app is opened (or comes back to the front) it compares today's date with the saved day.
If a new day has started: checkmarks and water are cleared, finished tasks are removed, **unfinished tasks carry over**, and all
routines (including the ones you added) stay. Yesterday's percentage is saved for the 7-day bars. If the phone clock is moved
backwards, nothing is wiped. *Me → Reset today's checklist* does the same clearing on demand.

**Progress** counts every routine item, every task, and the water goal (8 glasses = 1 item). The percentage is rounded down,
so 100% appears only when everything is truly done.

## 4. Data & privacy
- Profile, routines, tasks and history are stored as one small JSON text in the app's private preferences; the profile photo is a
  resized copy saved in the app's private folder. Nothing is sent anywhere; the app has no internet permission.
- Cloud backup is switched off (`allowBackup="false"`), so uninstalling the app deletes its data.
- The photo is chosen with Android's built-in photo picker, so the app needs no storage permission.

## 5. Adding Urdu later
All screen text is in `app/src/main/res/values/strings.xml`. To add Urdu: copy it to `app/src/main/res/values-ur/strings.xml`,
translate the values (keep the names and `%1$s` / `%1$d` placeholders). RTL layout is already enabled (`supportsRtl`).
Note: the starter routine names are copied into the saved list the first time the app opens, so an existing install keeps its
current wording (users can rename items with ✎).

## 6. Signing key
`signing/mydailylife.jks` (details in `keystore.properties`) is a self-made key created for this project so every build is signed
identically. That is fine for installing on your own phones. **Keep the repository private**, and if you ever publish to Google Play,
create and keep your own separate key. If `keystore.properties` is deleted, the release build falls back to the debug key.

## 7. Project layout
```
app/src/main/java/com/mydailylife/routine/
  MainActivity.kt        entry point; refreshes the day on every resume
  AppViewModel.kt        state + saving after every change
  model/Models.kt        AppData, RoutineItem, TaskItem, Section
  logic/DailyLogic.kt    progress, daily reset, add/edit/delete (pure Kotlin, unit-tested)
  data/Storage.kt        JSON save/load        data/ProfilePhoto.kt   photo resize + save
  ui/                    Theme, Components, TodayScreen, TasksScreen, ProfileScreen, AppRoot
app/src/test/.../DailyLogicTest.kt   21 unit tests (./gradlew testDebugUnitTest)
```
Tech: Kotlin 2.0.21, AGP 8.7.2, Gradle 8.10.2, Compose BOM 2024.10.01, Material 3, minSdk 26, targetSdk 35, JDK 17.

## 8. Manual test checklist (run this on the first build)
- [ ] First launch shows the welcome page; "Get started" is disabled until a name is typed.
- [ ] Add a photo from the gallery; it appears on the welcome page, **Today** header and **Me** tab. Remove photo works.
- [ ] Today says "Hello, *name*" with today's date. Percentage starts at 0%.
- [ ] Tick items in all three cards; ring, "X of Y done" and percent update. Un-tick works.
- [ ] Water: tap glass 3 → "3/8"; tap glass 3 again → "2/8"; fill all 8 → counts as one done item.
- [ ] ✎ on a routine → rename it; delete it; **＋ Add** creates a new one in the right group.
- [ ] Tasks: add, tick (moves to Completed), edit, delete. Empty-state message shows with no tasks.
- [ ] Close the app completely and reopen: name, photo, custom routines, ticks, tasks are all still there.
- [ ] Turn on airplane mode: everything still works.
- [ ] Daily reset: tick a few items, leave one task unfinished, finish another, then set the phone date to tomorrow and reopen.
      Ticks and water are cleared, the finished task is gone, the unfinished task and your custom routines remain,
      and yesterday appears in the 7-day bars. (Set the date back afterwards.)
- [ ] Rotate / switch apps and return: nothing is lost. Try a small screen and large font size.
