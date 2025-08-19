# Project-wide changes

Below are the updated files and snippets you need to modernize your app based on your existing code. The updates include: AndroidX migration, RecyclerView + ViewPager2, scoped storage for downloads, modern permission APIs, Material UI, safer FileProvider, and better organization.

---

## `app/build.gradle` (module)
```gradle
plugins {
    id 'com.android.application'
}

android {
    namespace 'com.app.ui'
    compileSdk 34

    defaultConfig {
        applicationId "com.app.ui"
        minSdk 21
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildFeatures {
        viewBinding true
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    implementation 'androidx.appcompat:appcompat:1.7.0'
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.recyclerview:recyclerview:1.3.2'
    implementation 'androidx.viewpager2:viewpager2:1.1.0'
    implementation 'androidx.activity:activity:1.9.2'
    implementation 'androidx.core:core-ktx:1.13.1'
}
```

---

## `src/main/AndroidManifest.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.app.ui">

    <!-- Network + download -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- For showing notifications on Android 13+ if DownloadManager posts them -->
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

    <!-- Needed to install APKs from your app (Android 8+) -->
    <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />

    <application
        android:allowBackup="true"
        android:label="@string/app_name"
        android:icon="@mipmap/ic_launcher"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.App.Modern">

        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="${applicationId}.provider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths" />
        </provider>

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

---

## `src/main/res/xml/file_paths.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Allows sharing files saved in getExternalFilesDir(...) -->
    <external-files-path name="external_files" path="." />
</paths>
```

---

## `src/main/res/values/styles.xml`
```xml
<resources>
    <style name="Theme.App.Modern" parent="Theme.Material3.Light.NoActionBar">
        <!-- Customize as needed -->
    </style>
</resources>
```

---

## `src/main/res/layout/activity_main.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#D5F7FF">

    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#00B1FF"
        android:theme="@style/ThemeOverlay.Material3.Dark.ActionBar"
        app:title="My Apps Store"
        app:titleCentered="true"
        app:navigationIconTint="@android:color/white"
        app:titleTextColor="@android:color/white" />

    <!-- Slider container -->
    <com.google.android.material.card.MaterialCardView
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:layout_margin="16dp"
        app:cardCornerRadius="12dp"
        app:cardUseCompatPadding="true">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <androidx.viewpager2.widget.ViewPager2
                android:id="@+id/viewPager"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1" />

            <com.google.android.material.tabs.TabLayout
                android:id="@+id/tabDots"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                app:tabIndicatorHeight="0dp"
                app:tabIndicatorColor="@android:color/transparent"
                app:tabSelectedTextColor="@android:color/transparent"
                app:tabTextColor="@android:color/transparent" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>

    <!-- Grid container -->
    <com.google.android.material.card.MaterialCardView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:layout_margin="16dp"
        app:cardCornerRadius="12dp"
        app:cardUseCompatPadding="true">

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/recyclerApps"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:padding="10dp" />
    </com.google.android.material.card.MaterialCardView>
</LinearLayout>
```

---

## `src/main/res/layout/item_app.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="12dp"
    android:background="@drawable/bg_item_card">

    <ImageView
        android:id="@+id/imgIcon"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:contentDescription="icon" />

    <TextView
        android:id="@+id/txtName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textColor="@android:color/black"
        android:textSize="14sp"
        android:layout_marginTop="6dp"
        android:gravity="center" />
</LinearLayout>
```

---

## `src/main/res/drawable/bg_item_card.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <corners android:radius="12dp" />
    <solid android:color="#FFFAFAFA" />
    <stroke android:width="1dp" android:color="#CCCCCC" />
</shape>
```

---

## `src/main/java/com/app/ui/MainActivity.java`
```java
package com.app.ui;

import android.app.AlertDialog;
import android.app.DownloadManager;
import android.content.*;
import android.database.Cursor;
import android.graphics.Color;
import android.net.ConnectivityManager;
import android.net.NetworkCapabilities;
import android.net.Uri;
import android.os.*;
import android.provider.Settings;
import android.util.SparseArray;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.ProgressBar;
import android.widget.TextView;
import android.widget.Toast;

import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.FileProvider;
import androidx.recyclerview.widget.GridLayoutManager;

import com.app.ui.databinding.ActivityMainBinding;
import com.google.android.material.appbar.MaterialToolbar;
import com.google.android.material.tabs.TabLayout;
import com.google.android.material.tabs.TabLayoutMediator;

import java.io.File;
import java.util.*;

public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;

    // Data
    private final int[] appIcons = { R.drawable.app, R.drawable.fps, R.drawable.notify, R.drawable.hd, R.drawable.studio, R.drawable.calculator, R.drawable.profile };
    private final String[] appNames = { "App Manager", "FPS Monitor", "NotifyMe", "Hidden-Settings", "Android-IDE", "Calculator", "My-Profile" };

    private final String[] downloadLinks = {
            "https://github.com/MSI-Sirajul/App-Manager/releases/download/App-Manager/App_Manager-v1.0_Release.apk",
            "https://github.com/MSI-Sirajul/FPS-Monitor/releases/download/FPS-AND-RAM/FPS_Monitor_v5.2.5-debug.apk",
            "https://github.com/MSI-Sirajul/NotifyMe/releases/download/Sms-forwarder/NotifyMe_New_Release.apk",
            "https://github.com/MSI-Sirajul/Hidden-Settings/releases/download/Hidden-Settings/Hidden-Setting-v4.6.1_letest.apk",
            "https://github.com/MSI-Sirajul/Android-IDE/releases/download/Android-IDE/Android.IDE_v0.2.9.Sirajul26.20.apk",
            "https://github.com/MSI-Sirajul/Calculator/releases/download/Calculator_1.7/Calculator_V1.7_Released.apk",
            "https://github.com/MSI-Sirajul/WebView-Profile/releases/download/WebView/Profile_release.apk"
    };

    private final Map<String, String> appPackages = new HashMap<String, String>() {{
        put("App Manager", "com.app.manager");
        put("FPS Monitor", "com.view.fps");
        put("NotifyMe", "com.app.notify");
        put("Hidden-Settings", "com.hidden.settings");
        put("Android-IDE", "com.tyron.code");
        put("Calculator", "com.msi.calculator");
        put("My-Profile", "com.sirajul.profile");
    }};

    // Download tracking
    private final Map<Long, Integer> idToPosition = new HashMap<>();
    private final Map<Long, AlertDialog> idToDialog = new HashMap<>();

    // Periodic progress
    private final Handler handler = new Handler(Looper.getMainLooper());
    private final Runnable progressTick = new Runnable() {
        @Override public void run() {
            DownloadManager dm = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
            for (Long id : new ArrayList<>(idToPosition.keySet())) {
                DownloadManager.Query q = new DownloadManager.Query().setFilterById(id);
                try (Cursor c = dm.query(q)) {
                    if (c != null && c.moveToFirst()) {
                        int status = c.getInt(c.getColumnIndexOrThrow(DownloadManager.COLUMN_STATUS));
                        int downloaded = c.getInt(c.getColumnIndexOrThrow(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR));
                        int total = c.getInt(c.getColumnIndexOrThrow(DownloadManager.COLUMN_TOTAL_SIZE_BYTES));

                        AlertDialog dlg = idToDialog.get(id);
                        if (dlg != null) {
                            ProgressBar pb = dlg.findViewById(R.id.progressBar);
                            TextView tv = dlg.findViewById(R.id.txtProgress);
                            if (total > 0 && pb != null && tv != null) {
                                int percent = (int) ((downloaded * 100L) / total);
                                pb.setIndeterminate(false);
                                pb.setMax(100);
                                pb.setProgress(percent);
                                tv.setText("" + percent + "%");
                            }
                        }

                        if (status == DownloadManager.STATUS_FAILED || status == DownloadManager.STATUS_SUCCESSFUL) {
                            // close dialog; receiver will handle next steps
                            AlertDialog d = idToDialog.remove(id);
                            if (d != null && d.isShowing()) d.dismiss();
                        }
                    }
                }
            }
            handler.postDelayed(this, 1000);
        }
    };

    // Broadcast receiver for completion
    private final BroadcastReceiver downloadReceiver = new BroadcastReceiver() {
        @Override public void onReceive(Context context, Intent intent) {
            long id = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1);
            Integer pos = idToPosition.remove(id);
            if (pos == null) return;

            DownloadManager dm = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
            Cursor c = dm.query(new DownloadManager.Query().setFilterById(id));
            if (c != null && c.moveToFirst()) {
                String localUri = c.getString(c.getColumnIndexOrThrow(DownloadManager.COLUMN_LOCAL_URI));
                if (localUri != null) {
                    File file = new File(Uri.parse(localUri).getPath());
                    installApk(file);
                } else {
                    Toast.makeText(MainActivity.this, "Download failed", Toast.LENGTH_LONG).show();
                }
                c.close();
            }
        }
    };

    // Permission launchers
    private final ActivityResultLauncher<String> notifPermissionLauncher =
            registerForActivityResult(new ActivityResultContracts.RequestPermission(), granted -> {});

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        // Toolbar
        MaterialToolbar toolbar = binding.toolbar;
        toolbar.setTitleTextColor(Color.WHITE);
        toolbar.setNavigationIcon(R.drawable.settings_ic);
        toolbar.setNavigationOnClickListener(v -> showNetworkSettings());

        // ViewPager2 slider
        int[] images = { R.drawable.image1, R.drawable.image2 };
        SliderAdapter sliderAdapter = new SliderAdapter(images);
        binding.viewPager.setAdapter(sliderAdapter);
        new TabLayoutMediator(binding.tabDots, binding.viewPager, (tab, position) -> {}).attach();
        autoSlide();

        // RecyclerView grid
        binding.recyclerApps.setLayoutManager(new GridLayoutManager(this, 2));
        AppsAdapter adapter = new AppsAdapter(appIcons, appNames, position -> onAppClicked(position));
        binding.recyclerApps.setAdapter(adapter);

        // Register receiver
        registerReceiver(downloadReceiver, new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE));
        handler.post(progressTick);

        // Ask notification permission only on 33+
        if (Build.VERSION.SDK_INT >= 33) {
            notifPermissionLauncher.launch(android.Manifest.permission.POST_NOTIFICATIONS);
        }
    }

    private void autoSlide() {
        final Handler h = new Handler(Looper.getMainLooper());
        final int pageCount = binding.viewPager.getAdapter() != null ? binding.viewPager.getAdapter().getItemCount() : 0;
        h.postDelayed(new Runnable() {
            int idx = 0;
            @Override public void run() {
                if (pageCount > 0) {
                    idx = (binding.viewPager.getCurrentItem() + 1) % pageCount;
                    binding.viewPager.setCurrentItem(idx, true);
                }
                h.postDelayed(this, 3000);
            }
        }, 3000);
    }

    private void onAppClicked(int position) {
        String appName = appNames[position];
        String pkg = appPackages.get(appName);
        if (isInstalled(pkg)) {
            Intent launchIntent = getPackageManager().getLaunchIntentForPackage(pkg);
            if (launchIntent != null) startActivity(launchIntent);
        } else {
            if (!isNetworkAvailable()) {
                Toast.makeText(this, "No internet connection", Toast.LENGTH_SHORT).show();
                return;
            }
            startDownload(position);
        }
    }

    private boolean isInstalled(String packageName) {
        try { getPackageManager().getPackageInfo(packageName, 0); return true; }
        catch (Exception e) { return false; }
    }

    private boolean isNetworkAvailable() {
        ConnectivityManager cm = (ConnectivityManager) getSystemService(CONNECTIVITY_SERVICE);
        if (cm == null) return false;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            android.net.Network nw = cm.getActiveNetwork();
            if (nw == null) return false;
            NetworkCapabilities caps = cm.getNetworkCapabilities(nw);
            return caps != null && (caps.hasTransport(NetworkCapabilities.TRANSPORT_WIFI) || caps.hasTransport(NetworkCapabilities.TRANSPORT_CELLULAR));
        }
        return cm.getActiveNetworkInfo() != null && cm.getActiveNetworkInfo().isConnected();
    }

    private void startDownload(int position) {
        String url = downloadLinks[position];
        String appName = appNames[position];

        DownloadManager.Request req = new DownloadManager.Request(Uri.parse(url))
                .setTitle("Downloading " + appName)
                .setDescription("Please wait…")
                .setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE)
                // Save into app-specific external dir (scoped storage friendly)
                .setDestinationInExternalFilesDir(this, Environment.DIRECTORY_DOWNLOADS, appName + ".apk");

        // Apply network prefs
        SharedPreferences prefs = getSharedPreferences("NETWORK_PREFS", MODE_PRIVATE);
        boolean wifiOnly = prefs.getBoolean("WIFI_ONLY", false);
        boolean lowData = prefs.getBoolean("LOW_DATA", false);
        req.setAllowedOverMetered(!wifiOnly);
        if (lowData) {
            req.setNotificationVisibility(DownloadManager.Request.VISIBILITY_HIDDEN);
        }

        DownloadManager dm = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
        long id = dm.enqueue(req);
        idToPosition.put(id, position);
        showProgressDialog(id, appName);
    }

    private void showProgressDialog(long id, String appName) {
        View v = LayoutInflater.from(this).inflate(R.layout.dialog_progress, null, false);
        TextView title = v.findViewById(R.id.txtTitle);
        TextView percent = v.findViewById(R.id.txtProgress);
        ProgressBar pb = v.findViewById(R.id.progressBar);
        title.setText("Downloading " + appName);
        percent.setText("0%");
        pb.setIndeterminate(true);

        AlertDialog dlg = new AlertDialog.Builder(this)
                .setView(v)
                .setCancelable(true)
                .setNegativeButton("Cancel", (d, w) -> {
                    DownloadManager dm = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
                    dm.remove(id);
                    idToPosition.remove(id);
                    idToDialog.remove(id);
                })
                .create();
        dlg.show();
        idToDialog.put(id, dlg);
    }

    private void showNetworkSettings() {
        View v = LayoutInflater.from(this).inflate(R.layout.dialog_network, null, false);
        android.widget.CheckBox wifiOnly = v.findViewById(R.id.cbWifiOnly);
        android.widget.CheckBox lowData = v.findViewById(R.id.cbLowData);

        SharedPreferences prefs = getSharedPreferences("NETWORK_PREFS", MODE_PRIVATE);
        wifiOnly.setChecked(prefs.getBoolean("WIFI_ONLY", false));
        lowData.setChecked(prefs.getBoolean("LOW_DATA", false));

        new AlertDialog.Builder(this)
                .setTitle("Network Settings")
                .setView(v)
                .setPositiveButton("Save", (d, w) -> {
                    prefs.edit()
                            .putBoolean("WIFI_ONLY", wifiOnly.isChecked())
                            .putBoolean("LOW_DATA", lowData.isChecked())
                            .apply();
                    Toast.makeText(this, "Settings saved", Toast.LENGTH_SHORT).show();
                })
                .setNegativeButton("Cancel", null)
                .show();
    }

    private void installApk(@NonNull File file) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            if (!getPackageManager().canRequestPackageInstalls()) {
                // Ask user to allow unknown sources for this app
                Intent i = new Intent(Settings.ACTION_MANAGE_UNKNOWN_APP_SOURCES,
                        Uri.parse("package:" + getPackageName()));
                startActivity(i);
                Toast.makeText(this, "Enable 'Install unknown apps' and retry", Toast.LENGTH_LONG).show();
                return;
            }
        }

        try {
            Uri apkUri = FileProvider.getUriForFile(this, getPackageName() + ".provider", file);
            Intent install = new Intent(Intent.ACTION_VIEW)
                    .setDataAndType(apkUri, "application/vnd.android.package-archive")
                    .addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION | Intent.FLAG_ACTIVITY_NEW_TASK);
            startActivity(install);
        } catch (Exception e) {
            Toast.makeText(this, "Install failed", Toast.LENGTH_LONG).show();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        try { unregisterReceiver(downloadReceiver); } catch (Exception ignored) {}
        handler.removeCallbacksAndMessages(null);
    }
}
```

---

## `src/main/java/com/app/ui/AppsAdapter.java`
```java
package com.app.ui;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

public class AppsAdapter extends RecyclerView.Adapter<AppsAdapter.VH> {
    public interface OnItemClick { void onClick(int position); }

    private final int[] icons;
    private final String[] names;
    private final OnItemClick onItemClick;

    public AppsAdapter(int[] icons, String[] names, OnItemClick onItemClick) {
        this.icons = icons;
        this.names = names;
        this.onItemClick = onItemClick;
    }

    @NonNull @Override public VH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_app, parent, false);
        return new VH(v);
    }

    @Override public void onBindViewHolder(@NonNull VH h, int position) {
        h.icon.setImageResource(icons[position]);
        h.name.setText(names[position]);
        h.itemView.setOnClickListener(v -> onItemClick.onClick(position));
    }

    @Override public int getItemCount() { return names.length; }

    static class VH extends RecyclerView.ViewHolder {
        ImageView icon; TextView name;
        VH(@NonNull View itemView) {
            super(itemView);
            icon = itemView.findViewById(R.id.imgIcon);
            name = itemView.findViewById(R.id.txtName);
        }
    }
}
```

---

## `src/main/java/com/app/ui/SliderAdapter.java`
```java
package com.app.ui;

import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

public class SliderAdapter extends RecyclerView.Adapter<SliderAdapter.VH> {
    private final int[] images;
    public SliderAdapter(int[] images) { this.images = images; }

    @NonNull @Override public VH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        ImageView iv = new ImageView(parent.getContext());
        iv.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
        iv.setScaleType(ImageView.ScaleType.CENTER_CROP);
        return new VH(iv);
    }

    @Override public void onBindViewHolder(@NonNull VH holder, int position) {
        ((ImageView) holder.itemView).setImageResource(images[position]);
    }

    @Override public int getItemCount() { return images.length; }

    static class VH extends RecyclerView.ViewHolder {
        VH(@NonNull View itemView) { super(itemView); }
    }
}
```

---

## `src/main/res/layout/dialog_progress.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="20dp">

    <TextView
        android:id="@+id/txtTitle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textStyle="bold"
        android:textSize="16sp"
        android:text="Downloading" />

    <ProgressBar
        android:id="@+id/progressBar"
        style="@android:style/Widget.Material.ProgressBar.Horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp" />

    <TextView
        android:id="@+id/txtProgress"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="0%" />
</LinearLayout>
```

---

## `src/main/res/layout/dialog_network.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="20dp">

    <CheckBox
        android:id="@+id/cbWifiOnly"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Download only on Wi‑Fi" />

    <CheckBox
        android:id="@+id/cbLowData"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="12dp"
        android:text="Low data mode (hide notifications)" />
</LinearLayout>
```

---

# What changed vs your original
- Migrated to **AndroidX**. Removed deprecated `ProgressDialog`. Custom dialog with `ProgressBar`.
- Replaced `GridView` with **RecyclerView** (grid) for better performance.
- Replaced manual slideshow with **ViewPager2** + `TabLayoutMediator` dots and auto-advance.
- Download destination uses **scoped storage**: `setDestinationInExternalFilesDir(...)`. No `WRITE_EXTERNAL_STORAGE`.
- Added **POST_NOTIFICATIONS** permission request for Android 13+.
- **FileProvider** authority uses `${applicationId}.provider` and `external-files-path`.
- Kept your package names, icons, links, and behaviors. Kept network prefs UI in a modern dialog.
- Network checks added before starting downloads.
- Install flow handles Android 8+ unknown sources setting.

# Notes
- Keep your existing drawables (`app`, `fps`, `notify`, `hd`, `studio`, `calculator`, `profile`, `image1`, `image2`, `settings_ic`). Update resource names if they differ.
- If you want foreground notifications for progress, convert the progress UI to a Foreground Service. This version keeps in-app dialogs to avoid extra complexity.
- Build with **targetSdk 34**. Ensure Android Gradle Plugin and Gradle match your environment.
