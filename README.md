<!DOCTYPE html>
<html>
<head>
  <title>InfoWay</title>
  <style>
    body {
      background-color: #000;
      color: #fff;
      font-family: 'Roboto', sans-serif;
      padding: 20px;
    }

    h1 {
      font-size: 40px;
      text-align: center;
      margin-bottom: 20px;
    }

    form {
      margin-bottom: 30px;
    }

    label {
      font-size: 18px;
      display: block;
      margin-bottom: 10px;
    }

    input[type="text"] {
      background-color: #333;
      border: none;
      color: #fff;
      padding: 10px;
      width: 100%;
      display: block;
      margin-bottom: 20px;
    }

    button[type="submit"] {
      background-color: #0078d4;
      border: none;
      color: #fff;
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
    }

    #map {
      margin-bottom: 20px;
    }

    #result {
      font-size: 16px;
    }
  </style>
</head>
<body>
  <h1>InfoWay</h1>

  <form id="routeForm" action="" method="GET">
    <label for="origin">Origen:</label>
    <input type="text" id="origin" name="origin" required>

    <label for="destination">Destino:</label>
    <input type="text" id="destination" name="destination" required>

    <button type="submit">Calcular ruta</button>
  </form>

  <div id="map"></div>
  <div id="result"></div>

  <script>
    var form = document.getElementById('routeForm');
    var resultDiv = document.getElementById('result');
    var mapDiv = document.getElementById('map');

    form.addEventListener('submit', function(event) {
      event.preventDefault();

      var originInput = document.getElementById('origin');
      var destinationInput = document.getElementById('destination');

      var origin = originInput.value;
      var destination = destinationInput.value;

      var url = "https://www.mapquestapi.com/directions/v2/route?key=jcshmI25Xc5WbpXyMnuJ4yBCkBIPJLwZ&from=" + encodeURIComponent(origin) + "&to=" + encodeURIComponent(destination) + "&locale=es_ES";

      fetch(url)
        .then(function(response) {
          return response.json();
        })
        .then(function(data) {
          var status_code = data.info.statuscode;

          if (status_code === 0) {
            var trip_duration = data.route.formattedTime;
            var distance = data.route.distance * 1.61;

            var result = `<h3>Información del viaje desde ${origin} hasta ${destination}</h3>`;
            result += `<p>Duración del viaje: ${trip_duration}</p>`;
            result += `<p>Distancia: ${distance.toFixed(2)} km</p>`;
            result += "<h3>Indicaciones del viaje</h3>";

            var maneuvers = data.route.legs[0].maneuvers;

            for ( i = 0; i < maneuvers.length; i++) {
              var each = maneuvers[i];
              var distance_remaining = distance - each.distance * 1.;
              result += `<p>${each.narrative} (${distance_remaining.toFixed(2)} Km faltantes)</p>`;
              distance = distance_remaining;
            }

            resultDiv.innerHTML = result;

            var mapUrl = `https://www.mapquestapi.com/staticmap/v5/map?key=jcshmI25Xc5WbpXyMnuJ4yBCkBIPJLwZ&start=${encodeURIComponent(origin)}&end=${encodeURIComponent(destination)}&size=600,400@2x&locale=es_ES`;

            var mapImage = document.createElement('img');
            mapImage.src = mapUrl;

            mapDiv.innerHTML = '';
            mapDiv.appendChild(mapImage);
          }
        })
        .catch(function(error) {
          console.log(error);
        });
    });
  </script>
</body>
</html>
