<script>
  let contador = 0;

  $: total = contador;

  const handleClick = () => {
    contador++;
  };
</script>

<div class="Boton">
  <button on:click={handleClick}>Click me</button>
  <div>Has dado {total} clicks</div>
</div>

<style></style>
