<script lang="ts">
    import { getContext, onMount } from "svelte";
    export let workHistoryId = {};
    let workHistory = {
        "http://rdfs.org/resume-rdf/cv.rdfs#jobDescription": [],
    };
    let company = {};
    const { fetchObject } = getContext("repository");

    onMount(async () => {
        workHistory = await fetchObject(workHistoryId);
        console.log("workHistory", workHistory);
        let companyId =
            workHistory["http://rdfs.org/resume-rdf/cv.rdfs#employedIn"]._id;
        company = await fetchObject(companyId);
    });
</script>

<div class="row">
    <div class="grid-2">
        <h2>{company['http://rdfs.org/resume-rdf/cv.rdfs#Name']}</h2>
        <p>
            {company['http://rdfs.org/resume-rdf/cv.rdfs#Locality']},
            {company['http://rdfs.org/resume-rdf/cv.rdfs#Country']}<br />
            {workHistory['http://rdfs.org/resume-rdf/cv.rdfs#startDate']}
            -
            {workHistory['http://rdfs.org/resume-rdf/cv.rdfs#endDate']}<br />
            {workHistory['http://rdfs.org/resume-rdf/cv.rdfs#jobType']}
        </p>
    </div>
    <div class="grid-4">
        <ul>
            {#each workHistory['http://rdfs.org/resume-rdf/cv.rdfs#jobDescription'] as jobDescription}
                <li>{jobDescription}</li>
            {/each}
        </ul>
    </div>
</div>
