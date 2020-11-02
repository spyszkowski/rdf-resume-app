<script lang="ts">
    import { getContext, onMount } from "svelte";
    let education = {};
    let educationOrg = {};

    const { fetchObject } = getContext("repository");

    onMount(async () => {
        education = await fetchObject("https://pyszkowski.com/Education");
        let eduOrdId =
            education["http://rdfs.org/resume-rdf/cv.rdfs#cv:studiedIn"]._id;
        educationOrg = await fetchObject(eduOrdId);
    });
</script>

<section class="education">
    <h1>Education</h1>
    <h2>
        {education['http://rdfs.org/resume-rdf/cv.rdfs#degreeType']}<br />
        {education['http://rdfs.org/resume-rdf/cv.rdfs#eduMajor']}<br />
        {education['http://rdfs.org/resume-rdf/cv.rdfs#eduMinor']}
    </h2>
    <h2>
        <a
            href={educationOrg['http://www.w3.org/1999/02/22-rdf-syntax-ns#URL']}>{educationOrg['http://www.w3.org/1999/02/22-rdf-syntax-ns#Name']}</a><br />
        {educationOrg['http://www.w3.org/1999/02/22-rdf-syntax-ns#Locality']}<br />
        {educationOrg['http://www.w3.org/1999/02/22-rdf-syntax-ns#Country']}
    </h2>
</section>
