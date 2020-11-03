<script lang="ts">
import { getContext, onMount } from 'svelte';

    import { Jumper } from 'svelte-loading-spinners'
    import Education from "./Education.svelte";
    import WorkHistroy from "./WorkHistory.svelte";
    const { fetchObject } = getContext("repository");
    
    let cv:any = {};
    let workHistoryArr = new Array<any>();
    
    onMount(async () => {
      cv = await fetchObject("https://pyszkowski.com/cv");
      workHistoryArr=cv["http://rdfs.org/resume-rdf/cv.rdfs#hasWorkHistory"];
      console.log(cv);
    });

    console.log(fetchObject);

</script>

<style>
  .container .centeredRow {
    margin: 0 auto;
    width: 10%;
}
</style>

<header>
  <h1>Sebastian Pyszkowski</h1>
  <p>Software Designer and Developer</p>
</header>
<div class="container">
  <!-- Introduction-->
  <section class="center">
    <h1>Welcome!</h1>
    <p>
      I am IT Consultant with over 15 years of experience building complex
      distributed systems using cutting-edge technologies. I have been working
      for major companies, helping them to design and implement large scale,
      highly available system.
      <!--This also might be a good place to link to your resume, email, or portfolio as well.-->
    </p>
  </section>
  <hr>
      
  <!--
  <!-- Gallery Block- ->

  <section class="gallery">
    <h1 class="center">Gallery</h1>
    <div class="row">
      <a href="#"><img src="img/re.png" class="grid-2"></a>
      <a href="#"><img src="img/re.png" class="grid-2"></a>
      <a href="#"><img src="img/re.png" class="grid-2"></a>
      <a href="#"><img src="img/re.png" class="grid-2"></a>
      <a href="#"><img src="img/re.png" class="grid-2"></a>
      <a href="#"><img src="img/re.png" class="grid-2"></a>
    </div>
  </section>


  <hr>
  
  <!- - Testimonials- ->	
  <section class="testimonial">
    <div class="row">
      <div class="grid-2">
        <img src="img/re.png">
        <h2>Their Name</h2>
        <p>You can use this as a place to put recommendations, accolades and testimonials</p>
      </div>
      
      <div class="grid-2">
        <img src="img/re.png">
        <h2>Their Name</h2>
        <p>You can use this as a place to put recommendations, accolades and testimonials</p>
      </div>
  
      <div class="grid-2">
        <img src="img/re.png">
        <h2>Their Name</h2>
        <p>You can use this as a place to put recommendations, accolades and testimonials</p>
      </div>
    </div>
  </section>
-->
</div> <!-- End container-->

<!-- Spotlight -->
<!--
<section class="spotlight">
  <div class="container">
    <div class="row">
      <div class="grid-3">
        <img src="img/spotlight.jpg">
      </div>
      <div class="grid-3">
        <h1>Project Spotlight</h1>
        <p>Give a brief description of what this project is and what role you played. It can also be helpful to include a link</p>
        <a href="#">View Project</a>
      </div>
    </div>
  </div>
</section>
-->

<!-- New container-->



{#if !cv._id }
<div class="container">
  <section class="center">
    <div class="centeredRow">
      <Jumper size="60" color="#FF3E00" unit="px"></Jumper>
  </div>
    <p>Waiting for Heroku container to come up ...</p>
  </section>
</div>
{:else}

<div class="container">
  <Education></Education>

  <hr>
    
  <!--Experience Tables-->

  <section class="experience">
    <h1>Work History</h1>
      {#each workHistoryArr as workHistoryObj}
        <WorkHistroy workHistoryId={workHistoryObj._id}></WorkHistroy>
      {/each}
  </section>

  <!--  
  <hr>
  
  <section>
    <!- -List of skills- ->
    <h1>Skills</h1>
    <div class="row">
      <div class="grid-2">

        <ul>
          <li>HTML</li>
          <li>CSS</li>
          <li>Javascript</li>
          <li>Angular.js</li>
          <li>Photoshop</li>
          <li>Git</li>
        </ul>
      </div>
      <div class="grid-2">
        <ul>
          <li>HTML</li>
          <li>CSS</li>
          <li>Javascript</li>
          <li>Angular.js</li>
          <li>Photoshop</li>
          <li>Git</li>
        </ul>
      </div>
      <div class="grid-2">
        <ul>
          <li>HTML</li>
          <li>CSS</li>
          <li>Javascript</li>
          <li>Angular.js</li>
          <li>Photoshop</li>
          <li>Git</li>
        </ul>
      </div>
      <!--
      <div class="grid-4">
        <h1>In Conclusion</h1>
        <p>
          This is a good spot to highlight experiance and skills. Make sure to include a call to action like a link to your website, or your email address.
          If you find bugs, want features, or have questions, feel free to submit an <a href="https://github.com/philipcdavis/responsive-resume/issues">issue</a>. You are free to use without attribution.
          If you're ok sharing, I would love seeing what you make! Shoot me an
          <a href="mailto:Phillysoul11@gmail.com?Subject=Responsive%20Resume" target="_top">email</a> or
          <a href="http://www.twitter.com/philipcdavis">tweet</a> at me.
        </p>
      </div>
      - ->
    </div>
  </section>
  
  -->
  
  <!--Footer-->
  <footer class="center">
    <hr>
    <span>&copy; Sebastian Pyszkowski 2020</span>
  </footer>

</div>

{/if}
