<script lang="ts">
  import { getContext, onMount } from "svelte";
  export let skills = [];
  export let categoryId: any = {};

  function getCategoryLabel() {
    if (skills.length > 0) {
      let category = skills[0]["https://pyszkowski.com/cv.rdfs#skillCategory"];
      if (category) {
        let label =
          category["http://www.w3.org/1999/02/22-rdf-syntax-ns#label"];
        if (label) {
          return label;
        } else {
          return categoryId;
        }
      }
    }
  }
</script>

<div class="grid-2">
  <h2>{getCategoryLabel()}:</h2>
  <ul>
    {#each skills as skill}
      <li>{skill['http://rdfs.org/resume-rdf/cv.rdfs#skillName']}</li>
    {/each}
  </ul>
</div>
