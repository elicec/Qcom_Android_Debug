设置system app对cache目录的访问权限

---

**主要文件**

* device/qcom/sepolicy/common/system_app.te

**修改点**

```diff
diff --git a/device/qcom/sepolicy/common/system_app.te b/device/qcom/sepolicy/common/system_app.te
index a70e633790..a38c856b2b 100755
--- a/device/qcom/sepolicy/common/system_app.te
+++ b/device/qcom/sepolicy/common/system_app.te
@@ -68,3 +68,5 @@ allow system_app RIDL_socket:dir r_dir_perms;
 unix_socket_connect(system_app, RIDL, RIDL)
 allow system_app RIDL_socket:sock_file r_file_perms;
 allow system_app sysfs:file write;
+allow system_app cache_file:dir create_dir_perms;
+allow system_app cache_file:file create_file_perms;

```

> selinux权限相关的问题，都可以类似修改。具体权限报错，在logcat中会有 avc相关的提示错误
