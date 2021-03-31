<script>
  let query = "";
</script>

<div class="Search">
  <div class="Search-container">
    <input type="option" placeholder="Buscar" bind:value={query} on:input />
  </div>
</div>

<style>
  input {
    width: 500px;
  }
</style>
