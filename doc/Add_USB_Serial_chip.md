内核中添加特定USB转串口芯片支持，例如PL2303,CH341等

---

**主要文件**

kernel/arch/arm/configs/msm8909-1gb_defconfig

**修改点**
```diff
diff --git a/kernel/arch/arm/configs/msm8909-1gb_defconfig b/kernel/arch/arm/configs/msm8909-1gb_defconfig
index c6a5230832..d057d77830 100755
--- a/kernel/arch/arm/configs/msm8909-1gb_defconfig
+++ b/kernel/arch/arm/configs/msm8909-1gb_defconfig
@@ -623,3 +624,6 @@ CONFIG_HAVE_ARCH_SECCOMP_FILTER=y
 CONFIG_SECCOMP=y
 CONFIG_SECCOMP_FILTER=y
 CONFIG_FINGERPRINT_ZWMA6=y
+CONFIG_USB_SERIAL_PL2303=y
+CONFIG_USB_SERIAL_CP210X=y
+CONFIG_USB_SERIAL_CH341=y
```

> 通过grep CONFIG_USB_SERIAL_PL2303 可以看到，需要内核中对应的驱动C文件支持
