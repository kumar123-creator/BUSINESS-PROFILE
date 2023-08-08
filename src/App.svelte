<script>
    import BusinessProfile from './BusinessProfile.svelte'
 </script>

<main class="container">
<BusinessProfile
      name:{name}
      address:{address}
      phone:{phone}
      website:{website}
      currencyCode:{currencyCode}
      preferredDateformat:{preferredDateformat}
      timeZone:{timeZone}
      preferredCountryCode:{preferredCountryCode}
      preferredCountries:{preferredCountries}
      imageUrl:{imageUrl}
      selected:{selected}
    />
</main>