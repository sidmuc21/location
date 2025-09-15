<script>
	let latitude = null;
	let longitude = null;
	let accuracy = null;
	let errorMessage = null;
 
	function getLocation() {
		navigator.geolocation.getCurrentPosition(showPosition, showError);
	}
 
	function showPosition(position) {
		latitude = position.coords.latitude;
		longitude = position.coords.longitude;
		accuracy = position.coords.accuracy;
		errorMessage = null;
	}
 
	function showError(error) {
		errorMessage = "Es gibt ein Fehler.";
	}
</script>
 
<div class="min-h-screen flex justify-center items-center px-4 sm:px-6 md:px-12 bg-gradient-to-b from-[#000814] to-[#0c0a3b]">
  <div class="w-full max-w-md p-6">
    
    <h1 class="text-2xl font-semibold font-display text-white mb-4 text-center">GEOLOCATION</h1>
    
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

    <ul class="text-blue-400">
      <li class="my-2 font-bold">BREITENGRAD: {latitude}</li>
      <li class="my-2 font-bold">LANGENGRAD: {longitude}</li>
      <li class="my-2 font-bold">GENAUIGKEIT (m): {accuracy}</li>
    </ul>
  </div>
</div>
