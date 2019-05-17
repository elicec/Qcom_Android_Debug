

长按8秒关机，修改为长按15秒重启设备

---


**主要文件**
* kernel/arch/arm/boot/dts/qcom/msm-pm8909.dtsi
* kernel\Documentation\devicetree\bindings\platform\msm\qpnp-power-on.txt

**修改点**

``` diff
diff --git a/kernel/arch/arm/boot/dts/qcom/msm-pm8909.dtsi b/kernel/arch/arm/boot/dts/qcom/msm-pm8909.dtsi
index 8da97a0084..f00d7356d3 100644
--- a/kernel/arch/arm/boot/dts/qcom/msm-pm8909.dtsi
+++ b/kernel/arch/arm/boot/dts/qcom/msm-pm8909.dtsi
@@ -37,9 +37,9 @@
                                qcom,pon-type = <0>;
                                qcom,support-reset = <1>;     
                                qcom,pull-up = <1>;
-                               qcom,s1-timer = <6720>;  
-                               qcom,s2-timer = <1000>;
-                               qcom,s2-type = <4>;                    
+                               qcom,s1-timer = <10256>;  
+                               qcom,s2-timer = <2000>;
+                               qcom,s2-type = <7>;                     
                                linux,code = <116>;
                        };
```
**DTS配置**
- qcom,support-reset   

0=关闭，1=开启
- qcom,s1-timer	

支持0, 32, 56, 80, 128, 184, 272, 408, 608, 904 1352, 2048, 3072, 4480, 6720, 10256
- qcom,s2-timer 

支持0, 10, 50, 100, 250, 500, 1000, 2000
> 长按时间 = (s-timer) + (s2-timer)

qcom,s2-type = <7>; 长按后执行的动作类型

7：重启
4：关机
1：HARD RESET系统进入dump，可用QPST Software Download 工具导出dump
