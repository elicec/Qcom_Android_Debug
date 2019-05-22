添加一个低电提醒警告框

---

**主要文件**
* frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerUI.java
* frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerNotificationWarnings.java

**修改点**
````diff
diff --git a/frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerNotificationWarnings.java b/frameworks/base/packages/SystemUI/
src/com/android/systemui/power/PowerNotificationWarnings.java
index 4391bfc882..0e728ca67c 100644
--- a/frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerNotificationWarnings.java
+++ b/frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerNotificationWarnings.java
@@ -28,6 +28,15 @@ import android.content.DialogInterface.OnDismissListener;
 import android.content.Intent;
 import android.content.IntentFilter;
 import android.media.AudioAttributes;
+import android.app.AlertDialog;
+import android.media.AudioManager;
+import android.media.Ringtone;
+import android.media.RingtoneManager;
+import android.util.Log;
+import android.view.WindowManager;
+import android.widget.TextView;
 import android.net.Uri;
 import android.os.AsyncTask;
 import android.os.Handler;
@@ -85,6 +94,11 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
     private int mBucket;
     private long mScreenOffTime;
     private int mShowing;
+    private AlertDialog mLowBatteryDialog;
+    private TextView mBatteryLevelTextView;
+    private boolean mShowAlertDlg = false;
 
     private long mBucketDroppedNegativeTimeMs;
 
@@ -99,6 +113,12 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
         mNoMan = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
         mPowerMan = (PowerManager) context.getSystemService(Context.POWER_SERVICE);
         mReceiver.init();
+        if(context.getResources().getBoolean(R.bool.config_showLowBatteryAlertDlg))
+        {
+               mShowAlertDlg = true;
+        }
     }
 
     @Override
@@ -166,7 +186,9 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
         if (n.headsUpContentView != null) {
             n.headsUpContentView.setViewVisibility(com.android.internal.R.id.right_icon, View.GONE);
         }
-        mNoMan.notifyAsUser(TAG_NOTIFICATION, ID_NOTIFICATION, n, UserHandle.ALL);
+//      mNoMan.notifyAsUser(TAG_NOTIFICATION, ID_NOTIFICATION, n, UserHandle.ALL);
     }
 
     private void showWarningNotification() {
@@ -262,6 +284,10 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
     @Override
     public void updateLowBatteryWarning() {
         updateNotification();
+        if(mShowAlertDlg)
+               updateLowBatteryDialog();
     }
 
     @Override
@@ -274,6 +300,10 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
         if (mWarning) Slog.i(TAG, "dismissing low battery notification");
         mWarning = false;
         updateNotification();
+        if(mShowAlertDlg)
+               dismissLowBatteryDialog();
     }
 
     private boolean hasBatterySettings() {
@@ -292,6 +322,16 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
         mPlaySound = playSound;
         mWarning = true;
         updateNotification();
+        if(mShowAlertDlg)
+        {
+               showLowBatteryDialog();
+               if (mPlaySound) {
+                   attachLowBatterySound(null);
+                   mPlaySound = false;
+               }
+        }
     }
 
     private void attachLowBatterySound(Notification.Builder b) {
@@ -318,7 +358,20 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
             if (soundPath != null) {
                 final Uri soundUri = Uri.parse("file://" + soundPath);
                 if (soundUri != null) {
-                    b.setSound(soundUri, AUDIO_ATTRIBUTES);
+                    if(b!=null)
+                    {
+                        b.setSound(soundUri, AUDIO_ATTRIBUTES);
+                    }
+                    else
+                    {
+                        final Ringtone sfx = RingtoneManager.getRingtone(mContext, soundUri);
+                        if (sfx != null) {
+                            sfx.setStreamType(AudioManager.STREAM_SYSTEM);
+                            sfx.play();
+                        }
+                    }
                     if (DEBUG) Slog.d(TAG, "playing sound " + soundUri);
                 }
             }
@@ -347,6 +400,88 @@ public class PowerNotificationWarnings implements PowerUI.WarningsUI {
         updateNotification();
     }
 
+    private void dismissLowBatteryDialog()
+    {
+        if (mLowBatteryDialog != null) {
+            Log.d("skysolf", "closing low battery warning: level=" + mBatteryLevel);
+            mLowBatteryDialog.dismiss();
+        }
+    }
+
+    private void updateLowBatteryDialog()
+    {
+        CharSequence levelText = mContext.getString(
+                R.string.battery_low_percent_format, mBatteryLevel);
+
+        if (mBatteryLevelTextView != null) {
+            if (java.util.Locale.getDefault().getLanguage().equals("ru")) {
+                mBatteryLevelTextView.setText(mContext.getString(R.string.battery_low_title)+""+levelText+"%"); 
+            } else {
+                mBatteryLevelTextView.setText(mContext.getString(R.string.battery_low_title)+", "+levelText+"%");
+            }
+        }
+    }
+
+    private void showLowBatteryDialog()
+    {
+        CharSequence levelText = mContext.getString(
+                R.string.battery_low_percent_format, mBatteryLevel);
+
+        if (mBatteryLevelTextView != null) {
+            mBatteryLevelTextView.setText(mContext.getString(R.string.battery_low_title)+", "+levelText+"%");
+
+        } else {
+            if(mLowBatteryDialog != null && mLowBatteryDialog.isShowing()){
+                return ;
+            }
+            View v = View.inflate(mContext, R.layout.battery_low, null);
+            mBatteryLevelTextView = (TextView)v.findViewById(R.id.subtitle);
+            mBatteryLevelTextView.setText(mContext.getString(R.string.battery_low_title)+", "+levelText+"%");
+
+
+            AlertDialog.Builder b = new AlertDialog.Builder(mContext, R.style.AlertDialogCustom);
+                b.setCancelable(false);
+                b.setTitle(R.string.battery_low_charger_title);
+                b.setView(v);
+                b.setIconAttribute(android.R.attr.alertDialogIcon);
+                b.setPositiveButton(android.R.string.ok, null);
+
+            final Intent intent = new Intent(Intent.ACTION_POWER_USAGE_SUMMARY);
+            intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK
+                    | Intent.FLAG_ACTIVITY_MULTIPLE_TASK
+                    | Intent.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS
+                    | Intent.FLAG_ACTIVITY_NO_HISTORY);
+            if (intent.resolveActivity(mContext.getPackageManager()) != null) {
+                       b.setPositiveButton(null,null);
+                    b.setNegativeButton(android.R.string.ok, new DialogInterface.OnClickListener() {
+
+                        @Override
+                        public void onClick(DialogInterface arg0, int arg1) {
+                            dismissLowBatteryWarning();
+                        }
+
+                    });
+            }
+
+            AlertDialog d = b.create();
+
+            d.setOnDismissListener(new DialogInterface.OnDismissListener() {
+                    @Override
+                    public void onDismiss(DialogInterface dialog) {
+                        mLowBatteryDialog = null;
+                        mBatteryLevelTextView = null;
+                    }
+                });
+            d.getWindow().setType(WindowManager.LayoutParams.TYPE_SYSTEM_ALERT);
+            d.getWindow().getAttributes().privateFlags |=
+                    WindowManager.LayoutParams.PRIVATE_FLAG_SHOW_FOR_ALL_USERS;
+            d.show();
+            mLowBatteryDialog = d;
+        }
+    }
+
     private void showStartSaverConfirmation() {
         if (mSaverConfirmation != null) return;
         final SystemUIDialog d = new SystemUIDialog(mContext);
diff --git a/frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerUI.java b/frameworks/base/packages/SystemUI/src/com/android/sy
stemui/power/PowerUI.java
index 945974042e..dfb0450e7e 100644
--- a/frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerUI.java
+++ b/frameworks/base/packages/SystemUI/src/com/android/systemui/power/PowerUI.java
@@ -193,7 +193,6 @@ public class PowerUI extends SystemUI {
                 }
 
                 if (!plugged
-                        && (bucket < oldBucket || oldPlugged)
                         && mBatteryStatus != BatteryManager.BATTERY_STATUS_UNKNOWN
                         && bucket < 0) {
                     // only play SFX when the dialog comes up or the bucket changes

````
