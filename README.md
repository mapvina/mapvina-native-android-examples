# MapVina Native Android Examples

Tập hợp các ví dụ mẫu và hướng dẫn triển khai ứng dụng Android sử dụng **MapVina Android SDK**.

---

## 🚀 Giới thiệu

Kho lưu trữ này cung cấp các đoạn mã nguồn mẫu thực tế giúp các nhà phát triển nhanh chóng tích hợp bản đồ MapVina, tùy biến giao diện và triển khai các tính năng nâng cao trên nền tảng Android.

### Điểm nổi bật:
- Tích hợp chuẩn artifact từ Sonatype Maven Central (`io.github.mapvina:android-sdk:1.0.2`).
- Minh họa tùy biến đầy đủ các thành phần Ornament Core: Logo MapVina, La bàn (Compass), Điểm định vị người dùng (Location Puck) và Marker.
- Hỗ trợ các renderer đồ họa: Vulkan và OpenGL.

---

## 📦 Cấu hình Dependency

Thêm repository và thư viện vào `build.gradle` của module app:

```groovy
repositories {
    google()
    mavenCentral()
}

dependencies {
    // Core SDK
    implementation 'io.github.mapvina:android-sdk:1.0.2'

    // Tiện ích GeoJSON & Turf
    implementation 'io.github.mapvina:android-sdk-geojson:1.0.1'
    implementation 'io.github.mapvina:android-sdk-turf:1.0.1'
}
```

---

## 🗺️ Khởi tạo MapView cơ bản

### 1. Khai báo MapView trong Layout XML:
```xml
<io.github.mapvina.android.maps.MapView
    android:id="@+id/mapView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapvina_uiCompass="true"
    app:mapvina_uiLogo="true"
    app:mapvina_uiAttribution="true" />
```

### 2. Khởi tạo trong Activity:
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var mapView: MapView
    private lateinit var mapvinaMap: MapVinaMap

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Khởi tạo MapVina Singleton trước khi setContentView
        MapVina.getInstance(this, "public", WellKnownTileServer.MapVina)
        setContentView(R.layout.activity_main)

        mapView = findViewById(R.id.mapView)
        mapView.onCreate(savedInstanceState)
        mapView.getMapAsync { map ->
            this.mapvinaMap = map
            map.setStyle("https://maps.mapvina.com/styles/v2/streets.json?key=public") { style ->
                // Bản đồ sẵn sàng
            }
        }
    }

    override fun onStart() { super.onStart(); mapView.onStart() }
    override fun onResume() { super.onResume(); mapView.onResume() }
    override fun onPause() { super.onPause(); mapView.onPause() }
    override fun onStop() { super.onStop(); mapView.onStop() }
    override fun onDestroy() { super.onDestroy(); mapView.onDestroy() }
    override fun onLowMemory() { super.onLowMemory(); mapView.onLowMemory() }
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        mapView.onSaveInstanceState(outState)
    }
}
```

---

## 🎨 Tùy biến Ornaments & Icons Core

### 1. Tùy biến Logo MapVina
```kotlin
val uiSettings = mapvinaMap.uiSettings
uiSettings.isLogoEnabled = true
uiSettings.logoGravity = Gravity.BOTTOM or Gravity.START
uiSettings.setLogoMargins(16, 0, 0, 16)
```

### 2. Tùy biến La bàn (Compass)
```kotlin
uiSettings.isCompassEnabled = true
uiSettings.compassGravity = Gravity.TOP or Gravity.END
uiSettings.setCompassMargins(0, 16, 16, 0)
uiSettings.isCompassFadeFacingNorth = true

// Đổi icon la bàn sang Drawable riêng
val customCompass = ContextCompat.getDrawable(this, R.drawable.ic_custom_compass)
uiSettings.setCompassImage(customCompass)
```

### 3. Tùy biến User Location Puck (Điểm định vị GPS)
```kotlin
val options = LocationComponentOptions.builder(this)
    .foregroundDrawable(R.drawable.custom_location_puck)
    .bearingDrawable(R.drawable.custom_bearing_arrow)
    .gpsDrawable(R.drawable.custom_navigation_puck)
    .accuracyColor(ContextCompat.getColor(this, R.color.mapvina_blue))
    .accuracyAlpha(0.2f)
    .build()

val activationOptions = LocationComponentActivationOptions.builder(this, style)
    .locationComponentOptions(options)
    .build()

mapvinaMap.locationComponent.activateLocationComponent(activationOptions)
mapvinaMap.locationComponent.isLocationComponentEnabled = true
```

---

## 📚 Tài liệu & Tham khảo

- [MapVina Native SDK Core](https://github.com/mapvina/mapvina-native)
- [MapVina Document Android](https://github.com/mapvina/mapvina-document-android)
- [MapVina Sonatype Central Artifacts](https://central.sonatype.com/namespace/io.github.mapvina)
