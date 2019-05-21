特殊应用场景下，例如热敏打印。需要时间精度非常高，避免烧坏打印头。有时候，就需要长时间关闭系统中断，抢占。但是高通平台有一个watchdog模块，长时间得不到中断喂狗，系统会直接自动挂掉。下面修改可以关闭此watchdog

---
**主要文件**

* kernel/driver/soc/qcom/watchdog_v2.c
* kernel/arch/arm/boot/dts/qcom/msm8909.dtsi
* kernel/arch/arm/configs/msm8909-1gb_defconfig

**修改点**

````diff
diff --git a/kernel/arch/arm/configs/msm8909-1gb_defconfig b/kernel/arch/arm/configs/msm8909-1gb_defconfig
index 14d8a2ddf9..395290c04e 100755
--- a/kernel/arch/arm/configs/msm8909-1gb_defconfig
+++ b/kernel/arch/arm/configs/msm8909-1gb_defconfig
@@ -49,7 +49,7 @@ CONFIG_MSM_DIRECT_SCLK_ACCESS=y
 CONFIG_MSM_AVTIMER=y
 CONFIG_MSM_BUS_SCALING=y
 CONFIG_BUS_TOPOLOGY_ADHOC=y
-CONFIG_MSM_WATCHDOG_V2=y
+#CONFIG_MSM_WATCHDOG_V2=y
 CONFIG_MSM_DLOAD_MODE=y
 CONFIG_MSM_ADSP_LOADER=y
 CONFIG_SENSORS_ADSP=y
````

> msm8909.dtsi中可以通过 qcom,bark-time 和qcom,pet-time配置对应的喂狗时间
