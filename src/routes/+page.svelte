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
        const countryCode = props.country_code; 

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


<div class="min-h-screen flex justify-center items-center px-4 sm:px-6 md:px-12 bg-gradient-to-b from-[#000814] to-[#0c0a3b]">
  <div class="w-full max-w-md p-6">
    
    <h1 class="text-2xl text-white mb-4 text-center">GEOLOCATION</h1>
    
    <div class="flex justify-center mb-4">
      <button 
        on:click={getLocation} 
        class="p-3 hover:text-blue-400 text-white font-medium rounded"
      >
        STANDORT ABRUFEN
      </button>
    </div>

    {#if errorMessage}
      <p class="text-red-600 text-sm mb-4 text-center">{errorMessage}</p>
    {/if}

    <ul class="text-white">
      <li class="my-2 ">BREITENGRAD: {latitude}</li>
      <li class="my-2 ">LÄNGENGRAD: {longitude}</li>
      <li class="my-2 ">GENAUIGKEIT (m): {accuracy}</li>
      {#if address}
        <li class="my-2 ">ADRESSE: {address}</li>
        <li class="my-2 ">{flag}</li>
      {/if}
    </ul>
  </div>
</div>
