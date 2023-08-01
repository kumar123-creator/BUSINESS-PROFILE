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
	let logoImage;
	let currencies = [];
    let selectedCurrency = '';
	let timezones = [];

	
	onMount(async () => {
    try {
      // Fetch timezones from the API
      const response = await fetch('https://api.recruitly.io/api/lookup/timezone?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA');
      if (response.ok) {
        const data = await response.json();
		console.log('API response:', data);
        timezones =  data.data; // Assuming the API returns an array of timezones in the format: [{ code: '...', name: '...' }, ...]
      } else {
        console.error('Failed to fetch timezones from the API');
      }
    } catch (error) {
      console.error('Error fetching timezones:', error);
    }

    // Set the default logo image URL
    logoImage.src = 'https://s3-eu-west-1.amazonaws.com/recruitly-public/334fbbc8-6ab1-49d1-bede-5ead2a0b1a56/43aa5940-e9b2-406a-a826-99a405a7a343.jpeg';
  });

  
	const handleSubmit = () => {
	  // Here, you can handle the form submission and update the profile using the entered values.
	  // For simplicity, we are just logging the values to the console.
	  console.log('Company Name:', companyName);
	  console.log('Company Logo:', companyLogo);
	  console.log('Address:', address);
	  console.log('Phone:', phone);
	  console.log('Website:', website);
	  console.log('Currency:', selectedCurrency);
	  console.log('Preferred Date Format:', dateFormat);
	  console.log('Timezone:', timezone);
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
  
  </style>

  <div class="form-container">
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
		<Input type="tel" id="phone" bind:value={phone} placeholder="123-45-678" pattern="[0-9]{3}-[0-9]{2}-[0-9]{3}" required />
	  </div>
	  <div>
		<Label for="website" class="mb-2">Website URL</Label>
		<Input type="url" id="website" bind:value={website} placeholder="flowbite.com" required />
	  </div>
	  <div>
		<Label for="currency" class="mb-2">Currency</Label>
		<select id="currency" bind:value={selectedCurrency} required>
			<option value="">Select Currency</option>
		  {#each currencies as currency}
		  <option value={currency.code}>{currency.name}</option>
		  {/each}
		</select>
	  </div>
	  
	  <div>
		<Label for="date_format" class="mb-2">Preferred date format</Label>
		<Input type="text" id="date_format" bind:value={dateFormat} required />
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
  