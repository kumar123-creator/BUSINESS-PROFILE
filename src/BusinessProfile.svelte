<script>
	import { onMount, afterUpdate } from 'svelte';
	import { Input, Label, Button } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
	import 'intl-tel-input/build/css/intlTelInput.css';
	import intlTelInput from 'intl-tel-input';
	import { Select} from 'flowbite-svelte';
	import { MultiSelect } from 'flowbite-svelte';
  
	export let id = '';
	export let name = '';
	export let address = {
	  addressLine: '',
	  cityName: "",
        regionName: "",
        postCode: "",
        countryCode: "",
        latitude: "",
        longitude: "",
       };
	
    export let phone = '';
	export let website = '';
	export let currencyCode = '';
	export let preferredDateFormat = '';
	export let timeZone = '';
	export let dataFetched = false;
	export let currencies = [];
	export let timeZones = [];
    export let logoImage = new Image();
	export let inputRef;
	export let imageUrl = '';
	export let phoneInput; 
	export let iti; 
	export let mobilePreferences = {
          preferredCountryCode: "",
          preferredCountries: "",
         };
         export let countryOptions = '';
         export let preferredCountriesOptions=[];
         export let preferredCountryCode = [];
         export let preferredCountryCode1 = [];  
         export let selectedLabels=[];
         export let countries = [];
         export let selectedCountry = null;
         export let selected = ["IN","AF"];
         export let preferredCountries = ["us", "gb"];
         export let preferredCountries1 = [];
         export let showDropdown = false;
	
	
	async function fetchData() {
	  try {
		const response = await fetch("https://api.recruitly.io/api/business/profile?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
		const data = await response.json();
		console.log(data);
  
		id = data.id;
		name = data.name;
		address = {
		  addressLine: data.address.addressLine,
		  cityName: data.address.cityName,
          regionName: data.address.regionName,
          postCode: data.address.postCode,
          countryCode: data.address.countryCode,
          latitude: data.address.latitude,
          longitude: data.address.longitude,
    };
		phone = data.phone;
		website = data.website;
		currencyCode = data.currencyCode.code;
		preferredDateFormat = data.preferredDateFormat;
		timeZone = data.timeZone;
		preferredCountryCode = data.mobilePreferences.preferredCountryCode,
        preferredCountries = data.mobilePreferences.preferredCountries.split(','); // Split the string into an array
        console.log(preferredCountries);
        preferredCountryCode1 = preferredCountryCode.toUpperCase();
        console.log(preferredCountries1 );
		dataFetched = true;
	  } catch (error) {
		console.error("Error fetching data:", error);
	  }
	}

	async function fetchCurrencies() {
    try {
      const response = await fetch("https://api.recruitly.io/api/lookup/currency?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA");
      const currencyData = await response.json();
      currencies = currencyData.data;
    } catch (error) {
      console.error("Error fetching currencies:", error);
    }
  }

  async function fetchTimeZones() {
    try {
      const response = await fetch("https://api.recruitly.io/api/lookup/timezone?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA");
      const timeZoneData = await response.json();
	  console.log(timeZoneData);
      timeZones = timeZoneData.data;
    } catch (error) {
      console.error("Error fetching time zones:", error);
    }
  }

  const fetchCountryData = async () => {
      try {
      const response = await fetch(
        "https://api.recruitly.io/api/lookup/countries?apiKey=TEST69513C4B379BD5594CD0AAC9ECA436CA2C83" 
             );
   const responseData = await response.json();
     if (Array.isArray(responseData.data)) {
          countryOptions = responseData.data.map((country) => ({
              value: country.code,
              label: country.name,
              }));
        console.log("Country API response:", responseData);
             } else {
        console.error("Invalid country data format:", responseData);
            }
              } catch (error) {
                 console.error("Error fetching country options:", error);

 

                }
          };

          async function fetchCountries() {
   try {
    const response = await fetch(
       "https://api.recruitly.io/api/lookup/countries?apiKey=TEST69513C4B379BD5594CD0AAC9ECA436CA2C83" 
       );
   if (!response.ok) {
      throw new Error("Network response was not ok");
      }
        const responseData = await response.json();

 

     if (Array.isArray(responseData.data)) {
       countries = responseData.data.map((country) => ({
           value: country.code,
           name: country.name
         }));
      console.log("Fetched countries:", countries);
      } 
      else {
       console.error("Invalid API response format:", responseData);
      }
   } catch (error) {
  console.error("Error fetching countries:", error);
}
}

  
	onMount(() => {
	  fetchData();
	  fetchCurrencies();
	  fetchTimeZones();
	  fetchImage(); 
	  fetchCountryData();
      fetchCountries();
	  
	});

	afterUpdate(() => {
		const phoneInput = document.querySelector("#phone");
		if (phoneInput && !iti) {
      iti = window.intlTelInput(phoneInput, {
        utilsScript: "https://cdn.jsdelivr.net/npm/intl-tel-input@18.1.1/build/js/utils.js",
      });

      // Listen for changes and update the phone variable
      phoneInput.addEventListener("change", () => {
        phone = iti.getNumber();
      });
	}
	});

	async function saveFormData() {
	  const updateData = {
		id,
		name,
		address: {
		  addressLine: address.addressLine,
		  cityName: address.cityName,
      regionName:address.regionName,
      postCode:address.postCode,
      countryCode:address.countryCode,
      latitude:address.latitude,
      longitude:address.longitude,
    },
		phone,
		website,
		currencyCode: {
		  code: currencyCode
		},
		preferredDateFormat,
		timeZone,
		preferredCountries,
        preferredCountryCode: preferredCountryCode1.toLowerCase(),
		
	  };
	  console.log("Update data:", JSON.stringify(updateData));
  
	  try {
		const response = await fetch('https://api.recruitly.io/api/business/profile/save?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77', {
		  method: "POST",
		  headers: {
			"Content-Type": "application/json",
		  },
		  body: JSON.stringify(updateData),
		});
  
		const updatedData = await response.json();
		console.log("Updated data from API:", updatedData);
		alert("Business Profile Updated successfully");
	  } catch (error) {
		console.error("Error updating data:", error);
	  }
	}

	
	async function fetchImage() {
    try {
      const response = await fetch("https://api.recruitly.io/api/business/profile?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
      const data = await response.json();
      imageUrl = data.logo.url; // Assuming the image URL is in the 'logo' property
	  //fetchImage();
      console.log("Image URL:", imageUrl);
    } catch (error) {
      console.error("Error fetching image:", error);
    }
  }

  function handleLogoClick(){
	const inputElement = document.createElement("input");
	inputElement.type = "file";
	inputElement.accept = "image/*";
	inputElement.addEventListener("change", (event) => handleLogoUpload(event, inputElement));
	inputElement.click();
  }
  

	async function handleLogoUpload(event, inputElement) {
		inputElement.click();
    const file = event.target.files[0];

    if (file) {
      const formData = new FormData();
      formData.append('file', file);
      formData.append('profilePic', 'true');
      formData.append('type', 'TENANT');

      try {
        const response = await fetch('https://api.recruitly.io/api/image/upload?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77', {
          method: 'POST',
		  headers: {
			Cookie: "SESSION=NDU1ZDRjNmUtNDg1ZC00NjVhLWJhNmItN2NlZmE4NzYxMWRm",
            "Cache-Control": "no-cache, no-store, must-revalidate", // Add the Cache-Control header
		    },
          body: formData,
        });

        if (response.ok) {
          const responseData = await response.json();
          console.log('Image uploaded:', responseData);
		  imageUrl = responseData.url; 
		  const imgElement = document.getElementById('uploadedImage');
          imgElement.src = URL.createObjectURL(file);
		   // Call the function again to ensure image is uploaded
        await fetchData();
          alert('Company logo updated successfully');
        
	}
		else {
          console.error('Image upload failed:', response);
          alert('Failed to update company logo');
        }
      } catch (error) {
        console.error('Error uploading image:', error);
        alert('An error occurred while updating the company logo');
      }

    }
  }

  

  function loadData() {
    console.log('Fetching initial data...');
    // Simulate an asynchronous operation (e.g., API call) using setTimeout
    setTimeout(() => {
      console.log('Initial data fetched.');
    }, 2000); // Adjust the delay as needed
  }

  </script>
  
  <style>
		h1 {
    text-align: center;
    font-size: 1.5em;
    font-weight: bold;
	color: darkblue;
  }
  .form-container {
    max-width: 500px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid black;
    border-radius: 8px;
    background-color: #f5f5f5;
  }

  form {
    display: flex;
    flex-direction: column;
  }

  .logo-image {
        cursor: pointer;
        max-width: 100px; /* Set a maximum width for the image */
        height: auto; /* Maintain the aspect ratio */
    }

  </style>
  
  <div class="form-container">
	<h1 style="text-align: center;">BUSINESS PROFILE</h1>
	{#if dataFetched}
	  <form on:submit|preventDefault={saveFormData}>
		<div class="mb-6">
			<Label for="company_logo" class="mb-2">Company Logo</Label>
			<img id="uploadedImage" src="{imageUrl}" alt="Logo" on:click={handleLogoClick} class="logo-image" />
		</div>

  <div>
    <Label for="company_name" class="mb-2">Company name</Label>
    <Input type="text" id="company_name" bind:value={name} required />
  </div>

  <div>
    <Label for="address" class="mb-2">Address </Label>
    <Input type="text" id="address" bind:value={address.cityName} required />
  </div>

  <div>
	<Label for="phone" class="mb-2">Phone number</Label>
	  <Input type="tel" id="phone" bind:this={phoneInput} bind:value={phone}  title="Please use the format XXXXX-XXXXX or XXXXXXXXXX" required />
  </div>
  
  <div>
    <Label for="website" class="mb-2">Website URL</Label>
    <Input type="url" id="website" bind:value={website} placeholder="flowbite.com" required />
  </div>

  <div class="mb-6"> 
	<label for="currencyCode" class="block text-sm font-medium text-gray-700 dark:text-white">Currency:</label>
	 <div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700"> 
		<select id="currencyCode" bind:value={currencyCode} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600"> 
			{#each currencies as currency} 
			<option value={currency.code}>{currency.name}</option>
			{/each} 
		</select> 
	</div>

	<div>
	<label for="preferredDateFormat" class="block text-sm font-medium text-gray-700 dark:text-white">Preferred Date Format</label>
	<div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
	  <select id="preferredDateFormat" bind:value={preferredDateFormat} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
		<option value="default" >Pretty (e.g., 1 Day Ago/2 Week Ago, etc.)</option>
		<option >Date only (e.g., 12/31/2020)</option>
		<option >Date and Time (e.g., 12/31/2020 15:00:00)</option>
		<option >Date and Time (e.g., 12/31/2020 03:00PM)</option>
	  </select>
	</div>
	</div>
	
	<div class="mb-6">
        <label for="timezone" class="block text-sm font-medium text-gray-700 dark:text-white">Timezone</label>
        <div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
          <select id="timezone" bind:value={timeZone} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
			{#each timeZones as timeZoneOption}
			<option value={timeZoneOption.code}>{timeZoneOption.name}</option>
            {/each}
          </select>
          </div>

		  <div class="mb-3">
			<Label for="country" class="mb-2">Default Country To Display</Label>
			<Select class="block w-full rounded-lg bg-white border border-gray-300 text-gray-700 focus:outline-none focus:border-indigo-500" bind:value={preferredCountryCode1} on:change={(event) => {
					   preferredCountryCode1 = event.target.value.toUpperCase();}}>
			
							 {#each countryOptions as country1}
			<option value={country1.value}>{country1.label}</option> 
						   {/each}
			</Select>
			</div>
			
			
			 
			
				 <div class="mb-3">
			<Label class="mb-2">Preferred Countries To Display on Top</Label>
			<div>
			<input
						type="text"
						id="address"
						bind:value={preferredCountries}
						required
						on:click={() => (showDropdown = !showDropdown)}
					/>
					{#if showDropdown}
			<div class="dropdown">
			<Select bind:value={preferredCountries} required multiple>
								{#each countryOptions as country}
			<option value={country.value}>{country.label}</option>
								{/each}
			</Select>
			</div>
					{/if}
			</div>
			</div>

			  
  <Button type="submit" style="background-color: #007bff;">Update Profile</Button>
</form>

	{:else}
	  <p>Loading data...</p>
	  {/if}
	</div>