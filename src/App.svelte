<script>
  import { v4 } from "uuid";
  import Header from "./components/Header.svelte";
  import DashBoard from "./components/Dashboard.svelte";
  import { onMount } from "svelte";
  import { darkmode } from "./store/store";

  let notes = [];
  let copyNotes = [...notes];

  $: count = notes.length;

  onMount(async () => {
    const response = await fetch("http://localhost:3000");
    const data = await response.json();
    notes = [...data.notes];
    copyNotes = [...data.notes];

    darkmode.set(data.settings.darkmode);
  });

  const handleNew = () => {
    const color = generateColor();
    const id = v4();
    const note = {
      id: id,
      title: "",
      color: color,
      text: "",
    };

    fetch("http://localhost:3000/add", {
      method: "POST",
      body: JSON.stringify(note),
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
    })
      .then((response) => response.text())
      .then((res) => console.log(res));

    notes = [note, ...notes];
    console.log("🚀 ~ file: App.svelte ~ line 43 ~ handleNew ~ note", note);
    copyNotes = [...notes];
  };

  const generateColor = () => {
    const colors = [
      "#DDFFC2",
      "#FFC2C2",
      "#FFEAC2",
      "#C2FFD3",
      "#C2FFEC",
      "#C2FAFF",
      "#C2E2FF",
      "#CBC2FF",
      "#EBC2FF",
      "#FFC2F7",
      "#FFC2D8",
    ];
    const index = Math.floor(Math.random() * colors.length);
    return colors[index];
  };

  const handleUpdate = (e) => {
    const note = e.detail;
    const index = notes.findIndex((n) => n.id === note.id);

    fetch("http://localhost:3000/update", {
      method: "POST",
      body: JSON.stringify(e.detail),
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
    })
      .then((response) => response.text())
      .then((res) => console.log(res));

    notes[index] = note;
    copyNotes = [...notes];
  };

  const handleColor = (e) => {
    const index = notes.findIndex((note) => note.id === e.detail.id);
    console.log("color: " + JSON.stringify(index));
    notes[index].color = generateColor();

    fetch("http://localhost:3000/update", {
      method: "POST",
      body: JSON.stringify(notes[index]),
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
    })
      .then((response) => response.text())
      .then((res) => console.log(res));

    copyNotes[index] = notes[index];
  };

  const handleRemove = (e) => {
    const response = notes.filter((n) => n.id !== e.detail.id);

    fetch("http://localhost:3000/remove", {
      method: "POST",
      body: JSON.stringify({ id: e.detail.id }),
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
    })
      .then((response) => response.text())
      .then((res) => console.log(res));

    notes = [...response];
    copyNotes = [...notes];
  };

  const handleSearch = (e) => {
    const q = e.target.value.toLowerCase();
    if (q === "") {
      copyNotes = [...notes];
      return false;
    }

    const results = notes.filter((note) => {
      const title = note.title.toLowerCase();
      const text = note.text.toLowerCase();

      return title.indexOf(q) > -1 || text.indexOf(q) > -1;
    });

    copyNotes = [...results];
  };
</script>

<main class={$darkmode ? "darkmode" : ""}>
  <Header on:input={handleSearch} />
  <div class="count-notes">{count} notas</div>
  <DashBoard
    bind:notes={copyNotes}
    on:click={handleNew}
    on:update={handleUpdate}
    on:color={handleColor}
    on:remove={handleRemove}
  />
</main>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
  }
  main {
    height: 100%;
  }
  :global(main.darkmode, main.darkmode textarea, main.darkmode input) {
    background-color: rgba(0, 0, 0, 0.719);
    color: white;
  }
  .count-notes {
    padding: 20px 20px 0 20px;
    text-align: right;
  }
</style>
