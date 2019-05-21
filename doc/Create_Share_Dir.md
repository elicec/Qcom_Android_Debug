在android的data目录创建全局共享目录，任何人都有读写访问权限

---

**主要文件**

* system/core/rootdir/init.rc

**修改点**

````diff
diff --git a/system/core/rootdir/init.rc b/system/core/rootdir/init.rc
index e1801c7475..ed73fa05a2 100755
--- a/system/core/rootdir/init.rc
+++ b/system/core/rootdir/init.rc
@@ -64,6 +64,9 @@ on init
     mkdir /data 0771 system system
     mkdir /cache 0770 system cache
     mkdir /config 0500 root root
+    symlink /data/epay/share /Share
+    symlink /data/epay/share /tmp
+    symlink /data/epay/share /data/cloudpossdk
 
     # See storage config details at http://source.android.com/tech/storage/
     mkdir /mnt/shell 0700 shell shell
@@ -308,6 +311,8 @@ on post-fs-data
     mkdir /data/mediadrm 0770 mediadrm mediadrm
 
     mkdir /data/adb 0700 root root
+    mkdir /data/epay 0751 everybody everybody
+    mkdir /data/epay/share 0777 everybody everybody
 
     # symlink to bugreport storage location
     symlink /data/data/com.android.shell/files/bugreports /data/bugreports

````

> 创建/data/epay/share 目录作为全局可读写目录。并创建了几个软连接指向这儿
