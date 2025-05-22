<!DOCTYPE html>
<html>
  <head>
    <title>Heatmap from Google Sheets</title>
    <style>
      #map {
        height: 100vh;
        width: 100%;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>

    <script>
      const SHEET_ID = 'YOUR_SHEET_ID'; // นำจาก URL ของ Google Sheets
      const SHEET_RANGE = 'Sheet1!A2:D'; // แผ่นงานและช่วงข้อมูล
      const API_KEY = 'YOUR_API_KEY'; // ใส่ Google Maps API Key ของคุณ

      // ดึงข้อมูลจาก Google Sheets
      async function getSheetData() {
        const url = `https://sheets.googleapis.com/v4/spreadsheets/${SHEET_ID}/values/${SHEET_RANGE}?key=${API_KEY}`;
        const res = await fetch(url);
        const data = await res.json();
        return data.values.map(row => ({
          lat: parseFloat(row[1]),
          lng: parseFloat(row[2]),
          weight: parseFloat(row[3])
        }));
      }

      // สร้างแผนที่และ Heatmap Layer
      async function initMap() {
        const map = new google.maps.Map(document.getElementById('map'), {
          zoom: 12,
          center: { lat: 13.7563, lng: 100.5018 }, // Bangkok
          mapTypeId: 'roadmap'
        });

        const points = await getSheetData();
        const heatmapData = points.map(p => ({
          location: new google.maps.LatLng(p.lat, p.lng),
          weight: p.weight
        }));

        new google.maps.visualization.HeatmapLayer({
          data: heatmapData,
          map: map,
          radius: 30
        });
      }
    </script>

    <!-- โหลด Google Maps JS API -->
    <script async
      src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=visualization&callback=initMap">
    </script>
  </body>
</html>
