添加中国移动物联网卡APN参数

---

**主要文件**

* vendor/qcom/proprietary/qrdplus/Extension/apps/etc/apns-conf.xml

**修改点**

````diff
diff --git a/vendor/qcom/proprietary/qrdplus/Extension/apps/etc/apns-conf.xml b/vendor/qcom/proprietary/qrdplus/Extension/apps/etc/apns-conf.xml
index 02f2f22919..7fd17ae98f 100755
--- a/vendor/qcom/proprietary/qrdplus/Extension/apps/etc/apns-conf.xml
+++ b/vendor/qcom/proprietary/qrdplus/Extension/apps/etc/apns-conf.xml
@@ -11036,6 +11036,23 @@
       localized_name="APN_NAME_CMWAP"
       protocol="IPV4V6"
       roaming_protocol="IPV4V6"/>
+
+  <apn carrier="China Mobile cmiot"
+      apn="cmiot"
+      mcc="460"
+      mnc="04"
+      user=""
+      server=""
+      password=""
+      proxy=""
+      port=""
+      mmsproxy=""
+      mmsport=""
+      mmsc=""
+      type="default,net,supl"
+      preferred="true"
+      protocol="IPV4V6"
+      roaming_protocol="IPV4V6"/>
 <!-- add for apn weicx 2016/05/04 end -->
 
   <apn carrier="China Mobile"

````

> 此xml文件存有各大运营商的各种APN参数
