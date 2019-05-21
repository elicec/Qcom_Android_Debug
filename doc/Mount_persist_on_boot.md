关机充电时挂载persist分区

---

**主要文件**

system/core/rootdir/init.rc

**修改点**

```diff
diff --git a/system/core/rootdir/init.rc b/system/core/rootdir/init.rc
index fb0f7445d8..772f7ec2f9 100755
--- a/system/core/rootdir/init.rc
+++ b/system/core/rootdir/init.rc
@@ -467,6 +467,10 @@ on property:sys.init_log_level=*
     loglevel ${sys.init_log_level}
 
 on charger
+    wait /dev/block/bootdevice/by-name/persist
+    mount ext4 /dev/block/bootdevice/by-name/persist /persist nosuid nodev barrier=1
+    mkdir /persist/data 0700 system system
+    restorecon_recursive /persist
     class_start charger
 
 on property:vold.decrypt=trigger_reset_main
```

> init.rc脚本中可以定制需要开机启动相关项。例如开机加载某个ko驱动，开机执行某个脚本，开机启动某个服务，开机设置某个权限
