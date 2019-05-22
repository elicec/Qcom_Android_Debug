电量0%自动关机修改为低于5%自动关机

---

**主要文件**

* frameworks/base/services/core/java/com/android/server/BatteryService.java

**修改点**.
````diff
diff --git a/frameworks/base/services/core/java/com/android/server/BatteryService.java b/frameworks/base/services/core/java/com/android/server/BatteryService.java
index a470338e8e..3d63a4e644 100644
--- a/frameworks/base/services/core/java/com/android/server/BatteryService.java
+++ b/frameworks/base/services/core/java/com/android/server/BatteryService.java
@@ -358,7 +358,7 @@ public final class BatteryService extends SystemService {
     private void shutdownIfNoPowerLocked() {
         // shut down gracefully if our battery is critically low and we are not powered.
         // wait until the system has booted before attempting to display the shutdown dialog.
-        if (mBatteryProps.batteryLevel == 0 && !isPoweredLocked(BatteryManager.BATTERY_PLUGGED_ANY)) {
+        if (mBatteryProps.batteryLevel <= 5 && !isPoweredLocked(BatteryManager.BATTERY_PLUGGED_ANY)) {
             mHandler.post(new Runnable() {
                 @Override
                 public void run() {

````
