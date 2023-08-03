<script>
	import { onMount } from 'svelte';
	import { Input, Label, Checkbox, Button } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
  
	let companyName = '';
	let companyLogo = '';
	let address = '';
	let phone = '';
	let website = '';
	let dateFormat = '';
	let timezone = '';
	let currencies = [];
    let currencyCode = '';
	let timezones = [];
	let logoImage = new Image();

	
 // Function to fetch default values from API
 const fetchDefaultValues = async () => {
    try {
      const response = await fetch("https://api.recruitly.io/api/business/profile?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
      const data = await response.json();
      console.log('Default Values API response:', data);

      // Populate the input fields with fetched data
      companyName = data.name;
      address = data.fullAddressLine;
      phone = data.phone;
      website = data.website;
      currencyCode = data.currencyCode.code;
      dateFormat = data.preferredDateFormat;
      timezone = data.timeZone;
    } catch (error) {
      console.error("Failed to fetch default values:", error);
    }
  };

	onMount(async () => {
		await fetchDefaultValues();
    try {
      const response = await fetch("https://api.recruitly.io/api/lookup/currency?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA");
      const data = await response.json();
	  console.log('API response:', data);
      currencies = data.data;
	  console.log('Currencies type:', typeof currencies);
    } catch (error) {
      console.error("Failed to fetch currencies:", error);
    }

	    // Fetch timezones from the API
		const response = await fetch('https://api.recruitly.io/api/lookup/timezone?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA');
    if (response.ok) {
      const data = await response.json();
      timezones = data.data; // Assuming the API returns an array of timezones in the format: [{ code: '...', name: '...' }, ...]
    } else {
      console.error('Failed to fetch timezones from the API');
    }
	logoImage.src = 'https://s3-eu-west-1.amazonaws.com/recruitly-public/334fbbc8-6ab1-49d1-bede-5ead2a0b1a56/43aa5940-e9b2-406a-a826-99a405a7a343.jpeg';
  });
  
  const handleSubmit = async () => {
  try {
    const response = await fetch("https://api.recruitly.io/api/business/profile/save?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        systemRecordLabel: companyName,
        fullAddressLine: address,
        phone,
        website,
        currencyCode,
        preferredDateFormat: dateFormat,
        timeZone: timezone,
        // Add other properties as needed
      }),
    });

	const responseData = await response.json(); // Try to get response data even if the request is not successful
  console.log("Response Status:", response.status);
  console.log("Response Data:", responseData);

    if (response.ok) {
      console.log("Profile updated successfully");
    } else {
      console.error("Failed to update profile");
    }
  } catch (error) {
    console.error("Error updating profile:", error);
  }
};

  </script>
  
  <style>
  
	.form-container {
	  width: 400px; /* Adjust the width as needed */
	  background-color: white;
	  border-radius: 8px;
	  padding: 20px;
	  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	  justify-self: center;
      align-self: center;
	  margin-left: 500px;
	}

	h1 {
    text-align: center;
    font-size: 1.5em;
    font-weight: bold;
	color: darkblue;
  }
  </style>
  
  <div class="form-container">
	<h1 style="text-align: center;">Business Profile</h1>
  <form on:submit|preventDefault={handleSubmit}>
	<div class="grid gap-6 mb-6 md:grid-cols-1">
		<div>
			<Label for="company_logo" class="mb-2">Company logo</Label>
			<img bind:this={logoImage} alt="Company Logo" style="max-width: 100px; max-height: 100px; margin-top: 10px;" />
		</div>

	    <div>
		    <Label for="company_name" class="mb-2">Company name</Label>
		    <Input type="text" id="company_name" bind:value={companyName} required />
	    </div>

	    <div>
		<Label for="address" class="mb-2">Address</Label>
		<Input type="text" id="address" bind:value={address} required />
	  </div>

	  <div>
		<Label for="phone" class="mb-2">Phone number</Label>
		<Input type="tel" id="phone" bind:value={phone} placeholder="12345-67890"  title="Please use the format XXXXX-XXXXX or XXXXXXXXXX" required />
	  </div>

	  <div>
		<Label for="website" class="mb-2">Website URL</Label>
		<Input type="url" id="website" bind:value={website} placeholder="flowbite.com" required />
	  </div>

	  <div>
		<Label for="currency" class="mb-2">Currency</Label>
		<select id="currency" bind:value={currencyCode} required>
			<option value="">Select Currency</option>
		  {#each currencies as currency}
		  <option value={currency.code}>{currency.name}</option>
		  {/each}
		</select>
	  </div>
	  
	  <div>
		<Label for="date_format" class="mb-2">Preferred date format</Label>
		<select id="date_format" bind:value={dateFormat} required>
			<option value="">Select Date Format</option>
			<option value="default">Default</option>
			<option value="pretty">Pretty</option>
			<option value="date">Date Only</option>
			<option value="datetime">Date and Time Only</option>
		</select>
	</div>
	  
	  <div>
		<Label for="timezone" class="mb-2">Timezone</Label>
		<select id="timezone" bind:value={timezone} required>
		  <option value="">Select Timezone</option>
		  {#each timezones as timezone}
			<option value={timezone.code}>{timezone.name}</option>
		  {/each}
		</select>
	  </div>	
	    
	</div>
	<Button type="submit" style="background-color: #007bff;">Update Profile</Button>
  </form>
</div>