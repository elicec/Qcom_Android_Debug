每次开机自动加载ko驱动到系统

---

**主要文件**

* system/core/rootdir/init.rc

**修改点**

````diff
diff --git a/system/core/rootdir/init.rc b/system/core/rootdir/init.rc
index 9e69b1be0e..4ecf237afc 100755
--- a/system/core/rootdir/init.rc
+++ b/system/core/rootdir/init.rc
@@ -328,7 +328,7 @@ on post-fs-data
     #setprop vold.post_fs_data_done 1
 
     chmod 0770 /system/lib/modules/printer.ko
+    insmod /system/lib/modules/printer.ko
     chmod 0770 /system/lib/modules/posevent.ko
     insmod /system/lib/modules/posevent.ko
     chmod 0770 /system/lib/modules/wakeup.ko

````

> init.rc脚本中可以完成很多需要每次开机就自动加载的任务
