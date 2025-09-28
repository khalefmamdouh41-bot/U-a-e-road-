# U-a-e-road-
Learn to drive cars on real UAE roads
<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>لعبة سواقة بسيطة — Leaflet + OSM</title>

  <!-- Leaflet CSS -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
    integrity="sha256-sA+4g3k3kQ2q9rK+Q6o6p3i0bQ2g9+K2H4Jy7u9g7m0=" crossorigin=""/>
  <style>
    html,body,#map { height: 100%; margin:0; padding:0; font-family: Tahoma, Arial, sans-serif; }
    #hud {
      position: absolute;
      right: 12px;
      top: 12px;
      background: rgba(255,255,255,0.9);
      padding: 8px 12px;
      border-radius: 8px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.15);
      z-index: 1000;
    }
    #hud button { margin-left:8px; }
  </style>
</head>
<body>
  <div id="map"></div>
  <div id="hud">
    موقع البداية: <span id="coords">30.0444, 31.2357</span>
    <button id="toggleFollow">تتبع: تشغيل</button>
  </div>

  <!-- Leaflet JS -->
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
    integrity="sha256-QVYg2q6g2Yjqz7k2m3rpsg2Hq2Y9wq9s8vKxg9j8zJw=" crossorigin=""></script>

  <script>
    // بداية: موقع مبدئي (القاهرة)
    let carPos = { lat: 30.0444, lng: 31.2357 };
    let follow = true;

    // انشاء الخريطة
    const map = L.map('map', { zoomControl: true }).setView([carPos.lat, carPos.lng], 15);

    // بلاطات OpenStreetMap (مجانية)
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19,
      attribution: '© OpenStreetMap contributors'
    }).addTo(map);

    // ايقونة العربية
    const carIcon = L.icon({
      iconUrl: 'https://img.icons8.com/color/48/car--v1.png', // مصدر مجاني خارجي
      iconSize: [40, 40],
      iconAnchor: [20, 20],
    });

    // Marker للعربية
    const carMarker = L.marker([carPos.lat, carPos.lng], { icon: carIcon, rotationAngle: 0 }).addTo(map);

    // تحديث HUD
    function updateHUD() {
      document.getElementById('coords').innerText = carPos.lat.toFixed(5) + ', ' + carPos.lng.toFixed(5);
      if (follow) map.setView([carPos.lat, carPos.lng]);
    }
    updateHUD();

    // تحكم بالاسهم — نحرك الإحداثيات تدريجيًا
    document.addEventListener('keydown', (e) => {
      const step = 0.0006; // غيّره لتسريع/تبطئة الحركة
      let moved = false;
      if (e.key === 'ArrowUp') { carPos.lat += step; moved = true; }
      if (e.key === 'ArrowDown') { carPos.lat -= step; moved = true; }
      if (e.key === 'ArrowLeft') { carPos.lng -= step; moved = true; }
      if (e.key === 'ArrowRight') { carPos.lng += step; moved = true; }

      if (moved) {
        carMarker.setLatLng([carPos.lat, carPos.lng]);
        updateHUD();
      }
    });

    // زر للتبديل بين تتبع الخريطة وعدم التتبع
    document.getElementById('toggleFollow').addEventListener('click', () => {
      follow = !follow;
      document.getElementById('toggleFollow').innerText = 'تتبع: ' + (follow ? 'تشغيل' : 'إيقاف');
      if (follow) map.setView([carPos.lat, carPos.lng]);
    });

    // لمس: دعم تحريك بسيط عبر الضغط على الخريطة (لمستخدمي الموبايل)
    map.on('click', function(e) {
      carPos.lat = e.latlng.lat;
      carPos.lng = e.latlng.lng;
      carMarker.setLatLng([carPos.lat, carPos.lng]);
      updateHUD();
    });

    // ملاحظة: هذا Prototype — تقدر تضيف فيزيكا، طرق (polylines)، checkpoints، واجهة سباق، إلخ.
  </script>
</body>
</html>
