<script>
  let latitude = null;
  let longitude = null;
  let accuracy = null;
  let address = null;
  let errorMessage = null;
  let flag = null;

  const apiKey = '84ea7caff22e4de9bea2d25c44ab7a8e';

  function getLocation() {
    navigator.geolocation.getCurrentPosition(showPosition, showError);
  }

  function showPosition(position) {
    latitude = position.coords.latitude;
    longitude = position.coords.longitude;
    accuracy = position.coords.accuracy;
    errorMessage = null;

    getAddress(latitude, longitude);
  }

  function showError(error) {
    errorMessage = "Es gibt einen Fehler beim Abrufen des Standorts.";
    address = null;
    flag = null;
  }

  async function getAddress(lat, lon) {
    try {
      const response = await fetch(
        `https://api.geoapify.com/v1/geocode/reverse?lat=${lat}&lon=${lon}&apiKey=${apiKey}`
      );
      const data = await response.json();

      if (data.features && data.features.length > 0) {
        const props = data.features[0].properties;
        address = props.formatted;

        const countryName = props.country;

        if (countryName) {
          const flagRes = await fetch(`https://restcountries.com/v3.1/name/${encodeURIComponent(countryName)}?fullText=true`);
          const flagData = await flagRes.json();

          if (flagData && flagData[0] && flagData[0].flag) {
            flag = flagData[0].flag;
          } else {
            flag = null;
          }
        } else {
          flag = null;
        }
      } else {
        address = "Keine Adresse gefunden.";
        flag = null;
      }
    } catch (error) {
      address = "Fehler beim Abrufen der Adresse.";
      flag = null;
    }
  }
</script>

<svelte:head>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap" rel="stylesheet" />
</svelte:head>

<div class="min-h-screen flex justify-center items-center px-4 sm:px-6 md:px-12 bg-gradient-to-b from-[#000814] to-[#0c0a3b] font-orbitron">
  <div class="w-full max-w-md p-8 bg-[rgba(0,0,0,0.4)] backdrop-blur-md rounded-xl border border-blue-600 shadow-neon">
    
    <h1 class="text-4xl text-blue-400 font-bold mb-6 text-center glow-text">GEOLOCATION</h1>
    
    <div class="flex justify-center mb-6">
      <button 
        on:click={getLocation} 
        class="px-6 py-3 text-white font-semibold rounded-md bg-blue-700 hover:bg-blue-500 transition glow-button"
      >
        STANDORT ABRUFEN
      </button>
    </div>

    {#if errorMessage}
      <p class="text-red-500 text-sm mb-6 text-center glow-text-red">{errorMessage}</p>
    {/if}

    <ul class="text-blue-300 text-lg space-y-3">
      <li>BREITENGRAD: {latitude}</li>
      <li>LÄNGENGRAD: {longitude}</li>
      <li>GENAUIGKEIT (m): {accuracy}</li>
      {#if address}
        <li>ADRESSE: {address}</li>
        <li class="text-3xl">{flag}</li>
      {/if}
    </ul>
  </div>
</div>

<style>
  .font-orbitron {
    font-family: 'Orbitron', sans-serif;
  }

  .glow-text {
    text-shadow:
      0 0 5px #0ff,
      0 0 10px #0ff,

  }

  .glow-button {
    box-shadow:
      0 0 5px #00ccff,
      0 0 10px #00ccff,

  }

  .glow-button:hover {
    box-shadow:
      0 0 10px #00ffff,
      0 0 20px #00ffff,

  }

  .glow-text-red {
    text-shadow:
      0 0 5px #ff4444,
      0 0 10px #ff4444,
  }

  .shadow-neon {
    box-shadow:
      0 0 10px #0ff,
      0 0 20px #0ff,
      inset 0 0 10px #0ff;
  }
</style>
