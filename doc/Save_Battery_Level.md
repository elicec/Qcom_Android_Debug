保留10%系统电量，将本来0-100的电量区间，均匀的分布到10-100。并且保证用户显示仍然是0-100

---

**主要文件**

* system/core/healthd/BatteryMonitor.cpp
* system/core/healthd/BatteryMonitor.h

**修改点**

````diff
diff --git a/system/core/healthd/BatteryMonitor.cpp b/system/core/healthd/BatteryMonitor.cpp
index aa3fa97ed4..c23bc8804e 100644
--- a/system/core/healthd/BatteryMonitor.cpp
+++ b/system/core/healthd/BatteryMonitor.cpp
@@ -173,6 +173,28 @@ int BatteryMonitor::getIntField(const String8& path) {
     return value;
 }
 
+int  BatteryMonitor::batteryLevelAdapter(int oldLevel)
+{
+    /*
+     *    newLevel = oldLevel*K + b
commit abe093a0b57c9df63f7b2c1b05988800cc6306b5
Author: elicec <penghong@vanstone.com.cn>
Date:   Tue Jun 5 14:23:13 2018 +0800

    [boot.img][feat] add save power 10%

diff --git a/system/core/healthd/BatteryMonitor.cpp b/system/core/healthd/BatteryMonitor.cpp
index aa3fa97ed4..c23bc8804e 100644
--- a/system/core/healthd/BatteryMonitor.cpp
+++ b/system/core/healthd/BatteryMonitor.cpp
@@ -173,6 +173,28 @@ int BatteryMonitor::getIntField(const String8& path) {
     return value;
 }
 
+int  BatteryMonitor::batteryLevelAdapter(int oldLevel)
+{
+    /*
+     *    newLevel = oldLevel*K + b
+     *    K = 100/(100-L)
+     *    b = -L*K
+     */
+    int L = 10;
+    char pval[PROPERTY_VALUE_MAX];
+    property_get("ro.battery.save_level", pval, "10");
+    L = strtol(pval, NULL, 10);
+    if(oldLevel <= L)
+        return 0;
+    KLOG_WARNING(LOG_TAG, "BatteryMonitor batteryLevelAdapter ->  L= %d\n", L);
+    if(L < 0 || L > 90)
+        L = 10;
+    float K = 100.0f/(100.0f - L);
+    float b = -L*K;
+    float newLevel = K*oldLevel + b;
+    return (int)(newLevel + 0.5f);
+}
+
 bool BatteryMonitor::update(void) {
     bool logthis;
 
@@ -190,6 +212,12 @@ bool BatteryMonitor::update(void) {
     props.batteryLevel = mBatteryFixedCapacity ?
         mBatteryFixedCapacity :
         getIntField(mHealthdConfig->batteryCapacityPath);
+
+    KLOG_WARNING(LOG_TAG, "BatteryMonitor update -> old battery level %d\n", props.batteryLevel);
+    //save some battery level
+    props.batteryLevel = batteryLevelAdapter(props.batteryLevel);
+    KLOG_WARNING(LOG_TAG, "BatteryMonitor update -> new battery level %d\n", props.batteryLevel);
+
     props.batteryVoltage = getIntField(mHealthdConfig->batteryVoltagePath) / 1000;
 
     props.batteryTemperature = mBatteryFixedTemperature ?
diff --git a/system/core/healthd/BatteryMonitor.h b/system/core/healthd/BatteryMonitor.h
index 3425f27711..5af5600b18 100644
--- a/system/core/healthd/BatteryMonitor.h
+++ b/system/core/healthd/BatteryMonitor.h
@@ -56,6 +56,7 @@ class BatteryMonitor {
     PowerSupplyType readPowerSupplyType(const String8& path);
     bool getBooleanField(const String8& path);
     int getIntField(const String8& path);
+    int batteryLevelAdapter(int oldLevel);
 };
 
 }; // namespace android

````

> 实际保留电量可以通过属性值ro.battery.save_level配置。 system/core/healthd/目录主要包含内核power_supply和用户空间的纽带
