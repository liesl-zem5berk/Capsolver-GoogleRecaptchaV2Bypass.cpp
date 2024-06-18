[![licence](https://img.shields.io/badge/legal-Apache2-32a852.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)
# bgkeeper

Some phone makers never hand background work a fair run. This Android helper opens the exact vendor page where a user can whitelist your app, so notifications survive and services come back after a reboot.

The intent carries the user from your app to that page and asks them to add you to the allow list.

What usually goes wrong on a tuned ROM:

* the process dies the moment it is no longer in front
* notification messages never get displayed
* a battery optimiser terminates your service

###Reports from owners of untried hardware are the most useful thing you can send

### In particular: ZTE, Meizu, Oppo, OnePlus, HTC and Letv owners, a test run would help

## Handsets reported working

* Samsung — verified
* Huawei — verified
* Xiaomi — verified
* Asus — verified
* Meizu, OnePlus, Letv, HTC, ZTE — nobody has tried yet, reports wanted

### Planned work

* Oppo and Vivo support
* Automated AVD plus intent tests
* A dialog the caller can restyle
* Screenshots together with the intent path for every handset
* A matrix listing which helper is missing on which device

## Wiring it up

Grabbing the dependency:

```groovy
dependencies {
    implementation 'io.example:bgkeeper:2.1.1'
}
```

Then either show the ready-made sheet:

 <img src="IMG/screenshot_dialog.png" width="216" height="384">

```Java
    public void showSheet(Keeper.Actions actions) {
        new KeeperDialogBuilder().setContext(this).setAction(actions).show();
    }
```

or fire one screen yourself:

```Java
// battery saver page for this vendor
Keeper.doActionPowerSaving(MyContext);
```
```Java
// auto start permission page
Keeper.doActionAutoStart(MyContext);
```
```Java
// notification permission page
Keeper.doActionNotification(MyContext);
```

### Per handset behaviour

Where each call lands you, figure by figure:

| helper | Huawei | Samsung (<5.0) | Samsung (>5.0) | Asus | Xiaomi |
|---|---|---|---|---|---|
| battery saver | <img src="IMG/huawei.png" width="108" height="192"> | not supported | <img src="IMG/samsung.png" width="432" height="192"> | no whitelist screen exists | opens the powerkeeper list |
| auto start | not supported | not supported | not supported | not supported | opens the auto-start screen |
| notifications | not supported | not supported | not supported | not supported | not supported |

## Caretakers
example maintainer

## Still to do
  - try every handset
  - separate settings per autostart / notification / permission
  - better copy telling the user what to tap in each vendor screen
  - per-device, per-language artwork

## Debugging notes

Finding out which activity is in front — query the activity manager from a device shell:

```shell
$> adb shell
$> dumpsys activity activities | grep mFocusedActivity
atau untuk hasil yang lebih lengkap
$> dumpsys activity activities | grep Activity
```

Starting one by hand — two ways, by component or by action name:

```shell
$> adb shell
$> am start -n com.samsung.memorymanager/com.samsung.memorymanager.RamActivity  --user 0
$> am start -a com.exemple.Action --user 0
```
Documentation for that subcommand lives on a third-party cheat sheet.

### Hardware tried so far

Each row is a handset with the ROM it shipped, plus whether the jump goes straight into the package:

| handset | android | rom | autostart | saver |
|---|---|---|---|---|
| an Honor model | 4.4 | vendor skin v3.0.1 | — | jumps in, no |
| a Huawei P9 lite | 6.0 | vendor skin v4.1 | — | jumps in, no |
| a Samsung S7 edge | 7.0 | stock | not needed | jumps in |
| a Samsung Xcover | 5.1 | stock | not needed | jumps in |
| a Samsung S4 mini | 4.4 | stock | not needed | not needed |
| a Xiaomi Mi mix | 6.0.1 | vendor skin v8.0 | jumps in, no | jumps in, yes |

Intent names discovered along the way, grouped by release and skin:

| release | skin | action |
|---|---|---|
| 4.4 | vendor skin v3.0.1 | the Huawei protected-apps action |
| 6.0.1 | vendor skin v4.1 | the Huawei protected-apps action |
| 6.0.1 | vendor skin v8.0 | the MIUI auto-start broadcast, a hidden-apps config activity taking package_name and package_level extras, and the power-hide list broadcast |

## Credit

Earlier experiments by other authors shaped this helper — the projects below explored the same vendor screens first:

- backgroundable-android
- TamingTask
- CRomAppWhitelist
- a small permission library
- an ActiveX-era window permission sample