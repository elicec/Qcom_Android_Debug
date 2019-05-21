保持L6 LDO电源为常开状态。避免某些重要模块被断电

---

**主要文件**
* kernel/arch/arm/boot/dts/qcom/msm8909-regulator.dtsi

**修改点**

````diff
diff --git a/kernel/arch/arm/boot/dts/qcom/msm8909-regulator.dtsi b/kernel/arch/arm/boot/dts/qcom/msm8909-regulator.dtsi
index 24acaf8e92..4605853e64 100644
--- a/kernel/arch/arm/boot/dts/qcom/msm8909-regulator.dtsi
+++ b/kernel/arch/arm/boot/dts/qcom/msm8909-regulator.dtsi
@@ -133,6 +133,7 @@
                        status = "okay";
                pm8909_l6: regulator-l6 {
                        status = "okay";
+                       regulator-always-on;
                        regulator-min-microvolt = <1800000>;
                        regulator-max-microvolt = <1800000>;
                        qcom,init-voltage = <1800000>;

````

> 此dtsi文件对所有的ldo进行配置。另外 /d/regulator 调试目录也可以查看和配置所有ldo
