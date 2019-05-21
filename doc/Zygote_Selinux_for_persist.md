设置zygote进程对persist文件的selinux权限

---

**主要文件**

* external/sepolicy/zygote.te

**修改点**

````diff
diff --git a/external/sepolicy/zygote.te b/external/sepolicy/zygote.te
index adbea06dab..1a391a591f 100644
--- a/external/sepolicy/zygote.te
+++ b/external/sepolicy/zygote.te
@@ -58,3 +58,7 @@ allow zygote labeledfs:filesystem remount;
 
 # Handle --invoke-with command when launching Zygote with a wrapper command.
 allow zygote zygote_exec:file rx_file_perms;
+
+allow zygote persist_file:dir search;
+allow zygote persist_file:dir {rw_dir_perms create setattr};
+allow zygote persist_file:file {rw_file_perms create setattr};
````

> selinux相关权限错误都可以类似修改。具体selinux错误信息可以在logcat搜索 avc相关日志。另一个selinux配置的目录在device/qcom/selpolicy/common/
