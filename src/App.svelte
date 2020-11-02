<script lang="ts">
  import { setContext } from "svelte";

  import RdfForceGraph from "./RdfForceGraph.svelte";
  import ResponsiveResume from "./ResponsiveResume.svelte";
  let current = "home";

  setContext("repository", {
    fetchObject: async (id: string) => {
      let prodUrl = "https://rdf-resume.herokuapp.com/objects-respository";
      let localUrl = "http://localhost:3033/objects-respository";

      return fetch(prodUrl, {
        headers: new Headers({ "Content-Type": "application/json" }),
        method: "POST",
        body: JSON.stringify({ id: id }),
      })
        .then((r) => r.json())
        .then((obj) => {
          return obj;
        });
    },
  });
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  div {
    color: grey;
    text-transform: uppercase;
    font-size: 1.8em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }

  .flexcontainer {
    display: flex;
    flex-direction: row;
  }

  .flexcontainer div {
    margin-right: 30px;
    margin-bottom: 1px;
    border-bottom: 1px solid white;
  }

  .flexcontainer div:hover {
    /* text-decoration-line: underline;
  	text-decoration-thickness: 1px; */
    text-decoration: none;
    border-bottom: 1px solid red;
  }

  .flexcontainer div .label {
  }

  .flexcontainer div .symbol {
    font-family: "FontAwesome";
  }
  .selected {
    color: #ff3e00;
  }

  @media (max-width: 50em) {
    .flexcontainer div .label {
      display: none;
    }
  }

  @media (min-width: 50em) {
    .flexcontainer div .symbol {
      display: none;
    }
  }
</style>

<main>
  <div class="flexcontainer">
    <div
      class={current === 'home' ? 'selected' : ''}
      on:click={() => (current = 'home')}>
      <span class="symbol"><i class="fas fa-home" /> </span><span
        class="label">Home</span>
    </div>
    <div
      class={current === 'resume' ? 'selected' : ''}
      on:click={() => (current = 'resume')}>
      <span class="symbol"><i class="fas fa-project-diagram" /> </span><span
        class="label">Semantic</span>
    </div>
    <div
      class={current === 'about' ? 'selected' : ''}
      on:click={() => (current = 'about')}>
      <span class="symbol"><i class="fas fa-info" /> </span><span
        class="label">About</span>
    </div>
  </div>

  {#if current == 'home'}
    <ResponsiveResume />
  {/if}
  {#if current == 'resume'}
    <RdfForceGraph />
  {/if}
</main>
