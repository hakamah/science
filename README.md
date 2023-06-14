<!DOCTYPE html>
<html>
<head>
  <title>Calculateur de Temps de Voyage</title>
  <style>
    #vitesse-input {
      width: calc(400px + (400px / 3)); /* Agrandissement de 1/3 de la largeur d'origine */
    }
  </style>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script>
    function calculerTemps() {
      const c = 299792458; // Vitesse de la lumière en m/s

      // Récupérer les valeurs des entrées utilisateur
      const distanceInput = document.getElementById("distance-input");
      const vitesseInput = document.getElementById("vitesse-input");
      const masseInput = document.getElementById("masse-input");

      const distance = parseFloat(distanceInput.value);
      const vitesse = parseFloat(vitesseInput.value);
      const masse = parseFloat(masseInput.value);

      // Calculer le facteur de Lorentz (gamma)
      const vitesseEnMetresParSeconde = vitesse / 100 * c;
      const gamma = 1 / Math.sqrt(1 - Math.pow((vitesseEnMetresParSeconde / c), 2));

      // Calculer le temps personnel en années
      const tempsPersonnel = distance / (vitesseEnMetresParSeconde / c) / gamma;

      // Convertir le temps personnel en jours, heures, minutes et secondes
      const tempsPersonnelJours = tempsPersonnel * 365.25; // Temps personnel en jours
      const tempsPersonnelHeures = tempsPersonnelJours * 24; // Temps personnel en heures
      const tempsPersonnelMinutes = tempsPersonnelHeures * 60; // Temps personnel en minutes
      const tempsPersonnelSecondes = tempsPersonnelMinutes * 60; // Temps personnel en secondes

      // Calculer l'énergie cinétique et l'équivalent en bombes Tsar atomiques
      const energieCinetique = (gamma - 1) * (masse * c * c); // Énergie cinétique en joules
      const bombesTsar = energieCinetique / (4.184e+17); // Équivalent en bombes Tsar atomiques

      // Estimer le temps d'accélération et de freinage
      const tempsAcceleration = vitesseEnMetresParSeconde / (9.81 * 2); // Temps estimé pour l'accélération en secondes
      const tempsFreinage = tempsAcceleration; // Temps estimé pour le freinage en secondes

      // Convertir le temps de freinage en jours, heures, minutes et secondes
      let joursFreinage = Math.floor(tempsFreinage / (60 * 60 * 24)); // Jours de freinage
      let heuresFreinage = Math.floor((tempsFreinage % (60 * 60 * 24)) / (60 * 60)); // Heures de freinage
      let minutesFreinage = Math.floor((tempsFreinage % (60 * 60)) / 60); // Minutes de freinage
      let secondesFreinage = Math.floor(tempsFreinage % 60); // Secondes de freinage

      // Conversion des temps en heures, minutes et secondes si nécessaire
      if (joursFreinage > 0 && heuresFreinage >= 24) {
        heuresFreinage %= 24;
        joursFreinage++;
      }

      if (heuresFreinage > 0 && minutesFreinage >= 60) {
        minutesFreinage %= 60;
        heuresFreinage++;
      }

      if (minutesFreinage > 0 && secondesFreinage >= 60) {
        secondesFreinage %= 60;
        minutesFreinage++;
      }

      // Afficher les résultats dans le document HTML
      document.getElementById("temps-personnel-annees").textContent = tempsPersonnel.toFixed(2) + " années";
      document.getElementById("temps-personnel-jours").textContent = tempsPersonnelJours.toFixed(2) + " jours";
      document.getElementById("temps-personnel-heures").textContent = Math.floor(tempsPersonnelHeures) + " heures";
      document.getElementById("temps-personnel-minutes").textContent = Math.floor(tempsPersonnelMinutes) + " minutes";
      document.getElementById("temps-personnel-secondes").textContent = Math.floor(tempsPersonnelSecondes) + " secondes";
      document.getElementById("energie-cinetique").textContent = energieCinetique.toFixed(2) + " joules";
      document.getElementById("bombes-tsar").textContent = bombesTsar.toFixed(2);
      document.getElementById("temps-acceleration").textContent = joursFreinage + " jour(s) " + heuresFreinage + " heure(s) " + minutesFreinage + " minute(s) " + secondesFreinage + " seconde(s)";
      document.getElementById("temps-freinage").textContent = joursFreinage + " jour(s) " + heuresFreinage + " heure(s) " + minutesFreinage + " minute(s) " + secondesFreinage + " seconde(s)";

      creerGraphique(tempsAcceleration, tempsFreinage);
    }

    function formatTemps(jours, heures = 0, minutes = 0, secondes = 0) {
      let temps = "";

      if (jours > 0) {
        temps += jours + " jour(s) ";
      }

      if (heures > 0 || (jours > 0 && (minutes > 0 || secondes > 0))) {
        temps += heures.toString().padStart(2, "0") + " heure(s) ";
      }

      if (minutes > 0 || (heures > 0 && secondes > 0)) {
        temps += minutes.toString().padStart(2, "0") + " minute(s) ";
      }

      if (secondes > 0) {
        temps += secondes.toString().padStart(2, "0") + " seconde(s)";
      }

      return temps.trim();
    }

    function updateVitesse() {
      const vitesseInput = document.getElementById("vitesse-input");
      const vitesseDisplay = document.getElementById("vitesse-display");
      vitesseDisplay.textContent = vitesseInput.value + "%";
    }

    function creerGraphique(tempsAcceleration, tempsFreinage) {
      const ctx = document.getElementById("graphique").getContext("2d");

      new Chart(ctx, {
        type: "bar",
        data: {
          labels: ["Accélération", "Freinage"],
          datasets: [
            {
              label: "Temps (secondes)",
              data: [tempsAcceleration, tempsFreinage],
              backgroundColor: ["rgba(75, 192, 192, 0.2)", "rgba(255, 99, 132, 0.2)"],
              borderColor: ["rgba(75, 192, 192, 1)", "rgba(255, 99, 132, 1)"],
              borderWidth: 1
            }
          ]
        },
        options: {
          responsive: true,
          scales: {
            y: {
              beginAtZero: true,
              title: {
                display: true,
                text: "Temps (secondes)"
              }
            },
            x: {
              title: {
                display: true,
                text: "Étapes"
              }
            }
          },
          plugins: {
            title: {
              display: true,
              text: "Temps d'accélération et de freinage"
            }
          }
        }
      });
    }
  </script>
</head>
<body>
  <h1>Calculateur de Temps de Voyage</h1>
  <label for="distance-input">Distance (années-lumière) :</label>
  <input type="number" id="distance-input" step="0.01" required><br>
  <label for="vitesse-input">Vitesse (en % de la vitesse de la lumière) :</label>
  <input type="range" id="vitesse-input" min="0" max="100" value="0" oninput="updateVitesse()"><br>
  <div id="vitesse-display">0%</div><br>
  <label for="masse-input">Masse de l'objet (en kilogrammes) :</label>
  <input type="number" id="masse-input" step="0.01" required><br>
  <button onclick="calculerTemps()">Calculer</button>

  <h2>Résultats :</h2>
  <p>Temps personnel (années) : <span id="temps-personnel-annees"></span></p>
  <p>Temps personnel (jours) : <span id="temps-personnel-jours"></span></p>
  <p>Temps personnel (heures) : <span id="temps-personnel-heures"></span></p>
  <p>Temps personnel (minutes) : <span id="temps-personnel-minutes"></span></p>
  <p>Temps personnel (secondes) : <span id="temps-personnel-secondes"></span></p>
  <p>Énergie cinétique (joules) : <span id="energie-cinetique"></span></p>
  <p>Équivalent en bombes Tsar atomiques : <span id="bombes-tsar"></span></p>
  <p>Temps d'accélération : <span id="temps-acceleration"></span></p>
  <p>Temps de freinage : <span id="temps-freinage"></span></p>

  <canvas id="graphique" width="400" height="200"></canvas>
</body>
</html>
