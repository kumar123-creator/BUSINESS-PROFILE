<script>
	import { onMount } from 'svelte';
	import { Input, Label, Button } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
  
	let id = '';
	let name = '';
	let address = {
	  addressLine: '',
	  cityName: '',
	  regionName: '',
	  postCode: '',
	  countryCode: '',
	  latitude: '',
	  longitude: ''
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
		  longitude: data.address.longitude
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
	});
  
	async function saveFormData() {
	  const updateData = {
		id,
		name,
		address: {
		  addressLine: address.addressLine,
		  cityName: address.cityName,
		  regionName: address.regionName,
		  postCode: address.postCode,
		  countryCode: address.countryCode,
		  latitude: address.latitude,
		  longitude: address.longitude
		},
		phone,
		website,
		currencyCode: {
		  code: currencyCode
		},
		preferredDateFormat,
		timeZone,
		preferredCountries,
		preferredCountryCode
	  };
  
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
  
  </script>
  
  <style>
		h1 {
    text-align: center;
    font-size: 1.5em;
    font-weight: bold;
	color: darkblue;
  }
  </style>
  
  <div class="form-container">
	<h1 style="text-align: center;">Business Profile</h1>
	{#if dataFetched}
	  <form on:submit|preventDefault={saveFormData}>
  <div>
    <Label for="company_name" class="mb-2">Company name</Label>
    <Input type="text" id="company_name" bind:value={name} required />
  </div>

  <div>
    <Label for="addressLine" class="mb-2">Address Line</Label>
    <Input type="text" id="addressLine" bind:value={address.addressLine} required />
  </div>

  <div>
    <Label for="cityName" class="mb-2">City Name</Label>
    <Input type="text" id="cityName" bind:value={address.cityName} required />
  </div>

  <div>
    <Label for="regionName" class="mb-2">Region Name</Label>
    <Input type="text" id="regionName" bind:value={address.regionName} required />
  </div>

  <div>
    <Label for="postCode" class="mb-2">Post Code</Label>
    <Input type="text" id="postCode" bind:value={address.postCode} required />
  </div>

  <div>
    <Label for="countryCode" class="mb-2">Country Code</Label>
    <Input type="text" id="countryCode" bind:value={address.countryCode} required />
  </div>

  <div>
    <Label for="latitude" class="mb-2">Latitude</Label>
    <Input type="text" id="latitude" bind:value={address.latitude} required />
  </div>

  <div>
    <Label for="longitude" class="mb-2">Longitude</Label>
    <Input type="text" id="longitude" bind:value={address.longitude} required />
  </div>

  <div>
    <Label for="phone" class="mb-2">Phone number</Label>
    <Input type="tel" id="phone" bind:value={phone} placeholder="12345-67890" title="Please use the format XXXXX-XXXXX or XXXXXXXXXX" required />
  </div>

  <div>
    <Label for="website" class="mb-2">Website URL</Label>
    <Input type="url" id="website" bind:value={website} placeholder="flowbite.com" required />
  </div>

  <div>
    <Label for="currencyCode" class="mb-2">Currency Code</Label>
    <select id="currencyCode" bind:value={currencyCode} required>
      {#each currencies as currency}
        <option value={currency.code}>{currency.name} ({currency.code})</option>
      {/each}
    </select>
  </div>

  <div>
    <Label for="preferredDateFormat" class="mb-2">Preferred Date Format</Label>
    <Input type="text" id="preferredDateFormat" bind:value={preferredDateFormat} required />
  </div>

  <div>
	<Label for="timeZone" class="mb-2">Time Zone</Label>
	<select id="timeZone" bind:value={timeZone} required>
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
  