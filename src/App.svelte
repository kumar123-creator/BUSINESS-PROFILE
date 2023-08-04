<script>
	import { onMount } from 'svelte';
	import { Input, Label, Button } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
  
	let id = '';
	let name = '';
	let address = {
	  addressLine: '',
	  cityName: "",
        regionName: "",
        postCode: "",
        countryCode: "",
        latitude: "",
        longitude: "",
       };
	
	let phone = '';
	let website = '';
	
	let currencyCode = '';
	let preferredDateFormat = '';
	let timeZone = '';
	let preferredCountryCode = '';
	let preferredCountries = [];
	let dataFetched = false;
	let currencies = [];
	let timeZones = [];
	let logoImage = new Image();
	let inputRef;
	let imageUrl = '';
  
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
		preferredCountryCode = data.preferredCountryCode;
		preferredCountries = [data.preferredCountries];
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
  
	onMount(() => {
	  fetchData();
	  fetchCurrencies();
	  fetchTimeZones();
	  fetchImage(); 
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
		preferredCountries: preferredCountries,
		preferredCountryCode
	  };
	  console.log("Update data:", JSON.stringify(Update));
  
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
	  fetchImage();
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
  }fetchImage();

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
	<h1 style="text-align: center;">Business Profile</h1>
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
    <Input type="tel" id="phone" bind:value={phone} placeholder="12345-67890" title="Please use the format XXXXX-XXXXX or XXXXXXXXXX" required />
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

  <div>
    <Label for="preferredCountryCode" class="mb-2">Preferred Country Code</Label>
    <Input type="text" id="preferredCountryCode" bind:value={preferredCountryCode} required />
  </div>

  <div>
    <Label for="preferredCountries" class="mb-2">Preferred Countries</Label>
    <Input type="text" id="preferredCountries" bind:value={preferredCountries} required />
  </div>

  <Button type="submit" style="background-color: #007bff;">Update Profile</Button>
</form>

	{:else}
	  <p>Loading data...</p>
	{/if}
  </div>
  