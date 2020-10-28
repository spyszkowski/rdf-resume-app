zip -r rdf-resume-app-27-10-20.zip ./rdf-resume-app -x '*node_modules*'


npx degit sveltejs/template my-svelte-project
cd my-svelte-project
npm install
npm run dev

##to create a production-ready version
npm run build

## setup ts
node scripts/setupTypeScript.js



To preserve some encapsulation you can put the global selector inside a local one — it will still affect any child components, but it won't affect the rest of the page:

<style>
  ref:things :global(.selected) {
    font-weight: bold;
  }
</style>



npm install --save @types/n3
npm install --save n3
npm install --save sparql-engine

--for missing nodejs packages
npm install --save-dev rollup-plugin-node-polyfills


GITHUB_PAGES_ACCESS_TOKEN  -> GHP_ACCESS_TOKEN
