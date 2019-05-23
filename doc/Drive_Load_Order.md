很多内核模块驱动都是有相互依赖关系，加载顺序需要保证，否则会加载失败

---

**主要文件**

* kernel/drivers/power/Makefile

**修改点**

````diff
diff --git a/kernel/drivers/power/Makefile b/kernel/drivers/power/Makefile
index aa28da6d7e..8c07960ca1 100644
--- a/kernel/drivers/power/Makefile
+++ b/kernel/drivers/power/Makefile
@@ -56,8 +56,6 @@ obj-$(CONFIG_CHARGER_BQ2415X) += bq2415x_charger.o
 obj-$(CONFIG_POWER_AVS)                += avs/
 obj-$(CONFIG_SMB349_USB_CHARGER)   += smb349-charger.o
 obj-$(CONFIG_SMB350_CHARGER)   += smb350_charger.o
-obj-$(CONFIG_SC2701_CHARGER)   += sc2701_charger.o
-obj-$(CONFIG_SY6410_BATTERY)   += sy6410_battery.o
 obj-$(CONFIG_SMB135X_CHARGER)   += smb135x-charger.o
 obj-$(CONFIG_SMB1360_CHARGER_FG) += smb1360-charger-fg.o
 obj-$(CONFIG_SMB358_CHARGER)   += smb358-charger.o
@@ -68,6 +66,8 @@ obj-$(CONFIG_QPNP_VM_BMS)     += qpnp-vm-bms.o batterydata-lib.o batterydata-interfa
 obj-$(CONFIG_QPNP_FG)          += qpnp-fg.o
 obj-$(CONFIG_QPNP_CHARGER)     += qpnp-charger.o
 obj-$(CONFIG_QPNP_LINEAR_CHARGER)      += qpnp-linear-charger.o
+obj-$(CONFIG_SC2701_CHARGER)   += sc2701_charger.o
+obj-$(CONFIG_SY6410_BATTERY)   += sy6410_battery.o
 obj-$(CONFIG_QPNP_SMBCHARGER)  += qpnp-smbcharger.o
 obj-$(CONFIG_CHARGER_SMB347)   += smb347-charger.o
 obj-$(CONFIG_CHARGER_TPS65090) += tps65090-charger.o

````

> 将两个power_supply模块驱动的加载放在pnp-linear-charger之后加载。此顺序也是编译的顺序
