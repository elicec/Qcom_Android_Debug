添加自定义系统全局属性值，可以通过getprop获取该值

---

**主要文件**

* device/qcom/msm8909/system.prop

**修改点**

```diff
diff --git a/device/qcom/msm8909/system.prop b/device/qcom/msm8909/system.prop
index 528a1559e2..36f4948c0d 100755
--- a/device/qcom/msm8909/system.prop
+++ b/device/qcom/msm8909/system.prop
@@ -194,3 +194,11 @@ ro.sys.fw.bservice_age=5000
 
 #bug 7851
 persist.sys.isUsbOtgEnabled=true
+ro.home.enable=1
+ro.screenlock.enable=1
+ro.product.factory=company
+ro.product.version=1.00.00

```

> android全局系统属性可以通过getprop方法读取
