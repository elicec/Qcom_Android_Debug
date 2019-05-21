SD卡热插拔检测需要专门的GPIO配置，去支持，有些场景下需要节省GPIO，并且不需要SD卡热插拔功能。需要在kernel中禁用。

---

**主要文件**

* kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi

**修改点**

````diff
diff --git a/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi b/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
index 7f14312ca5..2a0f3f75c5 100755
--- a/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
+++ b/kernel/arch/arm/boot/dts/qcom/msm8909-qrd-skua.dtsi
@@ -479,7 +479,7 @@ qrd_batterydata: qcom,battery-data {
     interrupt-map = <0 &intc 0 125 0
         1 &intc 0 221 0>;
     interrupt-names = "hc_irq", "pwr_irq";
-    /*/delete-property/ cd-gpios;*/
+    /delete-property/ cd-gpios;
 };
 
 &pm8909_mpps {

````
