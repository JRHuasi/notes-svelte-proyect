<script>
  import NotePlaceHolder from "./NotePlaceHolder.svelte";
  export let notes;
  import { each } from "svelte/internal";
  import Note from "./Note.svelte";
</script>

<div class="DashBoard">
  <div class="notes-container">
    <NotePlaceHolder on:click />
    {#each notes as { id, title, color, text }}
      <Note {id} bind:title {color} bind:text on:update on:color on:remove />
    {/each}
  </div>
</div>

<style>
  .DashBoard {
    width: 100%;
    padding: 20px;
    box-sizing: border-box;
  }
  .notes-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    width: 100%;
    gap: 10px;
  }
</style>
