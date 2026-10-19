<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My GPS Map</title>

  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
  >

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f3f6f8;
      color: #203040;
    }

    header {
      background: #176b5b;
      color: white;
      padding: 22px 18px;
    }

    h1 {
      margin: 0 0 6px;
      font-size: 25px;
    }

    header p {
      margin: 0;
      font-size: 14px;
    }

    main {
      padding: 16px;
      max-width: 760px;
      margin: auto;
    }

    #map {
      height: 350px;
      width: 100%;
      border-radius: 14px;
      border: 1px solid #ccd5dc;
      z-index: 1;
    }

    button {
      width: 100%;
      padding: 14px;
      margin: 14px 0;
      border: 0;
      border-radius: 10px;
      background: #176b5b;
      color: white;
      font-size: 16px;
      font-weight: bold;
    }

    .info {
      background: white;
      padding: 16px;
      border-radius: 12px;
      margin-top: 12px;
      overflow-wrap: anywhere;
    }

    .info p {
      margin: 9px 0;
    }

    #status {
      color: #176b5b;
      font-weight: bold;
    }

    footer {
      text-align: center;
      padding: 18px;
      color: #607080;
      font-size: 12px;
    }
  </style>
</head>

<body>
  <header>
    <h1>📍 My GPS Map</h1>
    <p>Peta lokasi perangkatmu</p>
  </header>

  <main>
    <div id="map"></div>

    <button id="locate">Temukan Lokasiku</button>

    <section class="info">
      <p id="status">Tekan tombol untuk memulai GPS.</p>
      <p>Latitude: <span id="lat">—</span></p>
      <p>Longitude: <span id="lon">—</span></p>
      <p>Akurasi: <span id="accuracy">—</span></p>
      <p>
        <a id="mapsLink" href="#" target="_blank"
           rel="noopener noreferrer" hidden>
          Buka lokasi di Google Maps
        </a>
      </p>
    </section>
  </main>

  <footer>
    Lokasi hanya diambil setelah kamu memberi izin.
  </footer>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <script>
    const map = L.map("map").setView([0, 0], 2);

    L.tileLayer(
      "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      {
        maxZoom: 19,
        attribution: "&copy; OpenStreetMap contributors"
      }
    ).addTo(map);

    let marker = null;
    let circle = null;

    const status = document.getElementById("status");

    document.getElementById("locate").addEventListener("click", () => {
      if (!navigator.geolocation) {
        status.textContent = "Browser tidak mendukung GPS.";
        return;
      }

      status.textContent = "Meminta lokasi dari perangkat...";

      navigator.geolocation.getCurrentPosition(
        (position) => {
          const lat = position.coords.latitude;
          const lon = position.coords.longitude;
          const accuracy = position.coords.accuracy;

          document.getElementById("lat").textContent =
            lat.toFixed(6);

          document.getElementById("lon").textContent =
            lon.toFixed(6);

          document.getElementById("accuracy").textContent =
            Math.round(accuracy) + " meter";

          status.textContent = "Lokasi berhasil ditemukan!";

          map.setView([lat, lon], 16);

          if (marker) map.removeLayer(marker);
          if (circle) map.removeLayer(circle);

          marker = L.marker([lat, lon])
            .addTo(map)
            .bindPopup("Lokasimu saat ini")
            .openPopup();

          circle = L.circle([lat, lon], {
            radius: accuracy,
            color: "#176b5b",
            fillOpacity: 0.12
          }).addTo(map);

          const link = document.getElementById("mapsLink");
          link.href =
            "https://www.google.com/maps?q=" + lat + "," + lon;
          link.hidden = false;
        },
        (error) => {
          const messages = {
            1: "Izin lokasi ditolak. Periksa izin lokasi browser.",
            2: "Lokasi belum tersedia. Periksa GPS perangkat.",
            3: "Waktu permintaan habis. Coba lagi."
          };

          status.textContent =
            messages[error.code] || "GPS gagal dijalankan.";
        },
        {
          enableHighAccuracy: true,
          timeout: 20000,
          maximumAge: 0
        }
      );
    });
  </script>
</body>
</html>
