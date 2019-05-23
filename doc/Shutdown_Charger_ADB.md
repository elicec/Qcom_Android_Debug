关机充电过程中，保证ADB可用，便于调试关机充电中的异常

---

**主要文件**

* device/qcom/common/rootdir/etc/init.qcom.usb.rc

**修改点**
````diff
diff --git a/device/qcom/common/rootdir/etc/init.qcom.usb.rc b/device/qcom/common/rootdir/etc/init.qcom.usb.rc
index a0886d069a..12df65f234 100644
--- a/device/qcom/common/rootdir/etc/init.qcom.usb.rc
+++ b/device/qcom/common/rootdir/etc/init.qcom.usb.rc
@@ -33,7 +33,15 @@ on init
     #//-->
 
 on charger
-    setprop sys.usb.config mass_storage
+    #setprop sys.usb.config mass_storage
+    mkdir /dev/usb-ffs 0770 shell shell
+    mkdir /dev/usb-ffs/adb 0770 shell shell
+    mount functionfs adb /dev/usb-ffs/adb uid=2000,gid=2000
+    write /sys/class/android_usb/android0/f_ffs/aliases adb
 
 on fs
     mkdir /dev/usb-ffs 0770 shell shell

````
