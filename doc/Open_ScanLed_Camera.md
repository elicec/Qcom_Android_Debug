打开相机预览时，自动打开自定义的扫码补光灯

---

**主要文件**
* frameworks/av/services/camera/libcameraservice/api1/CameraClient.cpp
* frameworks/av/services/camera/libcameraservice/api1/CameraClient.h
* kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
* system/core/rootdir/init.rc

**修改点**

```diff
diff --git a/frameworks/av/services/camera/libcameraservice/api1/CameraClient.cpp b/frameworks/av/services/camera/libcameraservice/api1/CameraClient.cpp
index eef90408ff..16e581d1a5 100644
--- a/frameworks/av/services/camera/libcameraservice/api1/CameraClient.cpp
+++ b/frameworks/av/services/camera/libcameraservice/api1/CameraClient.cpp
@@ -19,6 +19,7 @@
 
 #include <cutils/properties.h>
 #include <gui/Surface.h>
+#include <fcntl.h>
 
 #include "api1/CameraClient.h"
 #include "device1/CameraHardwareInterface.h"
@@ -369,9 +370,29 @@ status_t CameraClient::setPreviewCallbackTarget(
 // start preview mode
 status_t CameraClient::startPreview() {
     LOG1("startPreview (pid %d)", getCallingPid());
+    setScanLed(true);
     return startCameraMode(CAMERA_PREVIEW_MODE);
 }
 
+void CameraClient::setScanLed(bool on){
+    int fd;
+    char buffer[10];
+    ALOGE("%s:111111start0 ", __FUNCTION__);
+    fd = open("/sys/class/leds/scan_light/brightness", O_RDWR);
+    if(fd < 0){
+        ALOGE("%s: can not open scan_light file node,errno:%d", __FUNCTION__,errno);
+        return;
+
+    }
+    if(on)
+        snprintf(buffer, sizeof(int), "%d\n", 255);
+    else
+        snprintf(buffer, sizeof(int), "%d\n", 0);
+    if(write(fd, buffer, strlen(buffer)) < 0)
+        ALOGE("could not write to scan led node,errno:%d",errno);
+    close(fd);
+}
+
 // start recording mode
 status_t CameraClient::startRecording() {
     LOG1("startRecording (pid %d)", getCallingPid());
@@ -455,6 +476,7 @@ status_t CameraClient::startRecordingMode() {
 void CameraClient::stopPreview() {
     LOG1("stopPreview (pid %d)", getCallingPid());
     Mutex::Autolock lock(mLock);
+    setScanLed(false);
     if (checkPidAndHardware() != NO_ERROR) return;
 
 
diff --git a/frameworks/av/services/camera/libcameraservice/api1/CameraClient.h b/frameworks/av/services/camera/libcameraservice/api1/CameraClient.h
index eda1a9f836..a1d3660189 100644
--- a/frameworks/av/services/camera/libcameraservice/api1/CameraClient.h
+++ b/frameworks/av/services/camera/libcameraservice/api1/CameraClient.h
@@ -164,6 +164,7 @@ private:
     // This function keeps trying to grab mLock, or give up if the message
     // is found to be disabled. It returns true if mLock is grabbed.
     bool                    lockIfMessageWanted(int32_t msgType);
+    void setScanLed(bool on);
 };
 
 }
diff --git a/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi b/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
index fa397bcadc..fc39a8f8e4 100755
--- a/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
+++ b/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
@@ -26,6 +26,16 @@
         compatible = "qcom,printer";
         qcom,printer-vadc = <&pm8909_vadc>;
     };
+    gpio-leds{
+        compatible = "gpio-leds";
+        status = "okay";
+        scan_light{
+            gpios=<&msm_gpio 4 0>;
+            label = "scan_light";
+            linux,default-trigger="none";
+            default-state = "off";
+        };
+    };
     i2c@78b9000 { /* BLSP1 QUP5 */
         status = "ok";
diff --git a/system/core/rootdir/init.rc b/system/core/rootdir/init.rc
index ed73fa05a2..fb0f7445d8 100755
--- a/system/core/rootdir/init.rc
+++ b/system/core/rootdir/init.rc
@@ -427,6 +427,7 @@ on boot
     chown system system /sys/class/leds/green/blink
     chown system system /sys/class/leds/green_breathe/brightness
     chown system system /sys/class/leds/green_breathe/blink
+    chown media  system /sys/class/leds/scan_light/brightness
     chmod          0660 /sys/class/leds/green_breathe/brightness
     chown system system /sys/class/leds/blue/brightness
     chown system system /sys/class/leds/blue/blink

```

> 注意init.rc中配置节点权限




