<script>
  import { onMount } from "svelte";
  import { onDestroy } from "svelte";
  import * as d3 from "d3";

  let simulation;

  onMount(async () => {
    let color = d3.scaleOrdinal(d3.schemeCategory10);
    //let width = 800;
    //let height = 350;

    var width = window.innerWidth,///2,
            height = window.innerHeight;///2;

    let svg = d3
      .select("#chart")
      .append("svg")
      .attr("class", "chart")
      .attr("viewBox", [-width , -height, width*2, height*2]);
    //   let svg = d3.select("svg");
    //  svg.attr("viewBox", [-width / 2, -height / 2, width, height]);

    const baseG = svg.append("g");

    const simulation = d3
      .forceSimulation()
      .force(
        "charge",
        d3.forceManyBody().strength((d) => {
          /*
      if(d.termType==='Literal'){
                  return -20;
                }else{
                  return -1000;
                }
       }*/
          return -1000;
        })
      )
      .force(
        "link",
        d3
          .forceLink()
          .id((d) => d.id)
          .distance((d) => {
            // if(d.target.termType==='Literal'){
            if (isOpened(d.target.id)) {
              return 200;
            } else {
              return 90;
            }
          })
      )
      .force("x", d3.forceX())
      .force("y", d3.forceY())
      .on("tick", ticked);

    var path = baseG
      .append("g")
      .attr("fill", "none")
      .attr("stroke", "#000")
      .attr("stroke-opacity", 0.2)
      .selectAll("path");

    var linktext = baseG.append("g").selectAll("g.linklabelholder");

    let node = baseG
      .append("g")
      // .attr("stroke", "#fff")
      .attr("stroke-width", 1)
      .selectAll(".chart_group");

    //label for node created as part of link
    var nodeLabel2 = baseG
      .append("g")
      .attr("stroke", "#000")
      .attr("stroke-opacity", 0.2)
      .selectAll("text");

    function ticked() {
      // node.attr("cx", d => d.x)
      //     .attr("cy", d => d.y)

      node.attr("transform", function (d) {
        return "translate(" + d.x + "," + d.y + ")";
      });

      nodeLabel2.attr("transform", function (d) {
        let relx, rely;
        let r = 18; //node radious
        let xdistance = 5;
        rely = this.getBBox().height / 2; //vertical text aligment
        if (d.source.x > d.target.x) {
          //lablel on the left side of the node
          relx = -( (this.getBBox().width /2 ) + r + xdistance);
        } else {
          //lablel on the right side of the node
          relx = this.getBBox().width /2 + r + xdistance;
        }
        // relx=0;
          rely=0;
        return (
          "translate(" + (d.target.x + relx) + "," + (d.target.y - rely) + ")"
        );
      });

      // link
      //   .attr("x1", (d) => d.source.x)
      //   .attr("y1", (d) => d.source.y)
      //   .attr("x2", (d) => d.target.x)
      //   .attr("y2", (d) => d.target.y);

      path.attr("d", function (d) {
        var dx = d.target.x - d.source.x,
          dy = d.target.y - d.source.y,
          // dr = 75 / d.linknum; //linknum is defined above
          dr = 1;
        // var dValue="M" +
        // d.source.x +
        // "," +
        // d.source.y +
        // "A" +
        // dr +
        // "," +
        // dr +
        // " 0 0,1 " +
        // d.target.x +
        // "," +
        // d.target.y;

        var dValue;

        //change path directon to make the text always from left to right
        if (d.source.x < d.target.x) {
          dValue =
            "M" +
            d.source.x +
            "," +
            d.source.y +
            "L" +
            d.target.x +
            "," +
            d.target.y;
        } else {
          dValue =
            "M" +
            d.target.x +
            "," +
            d.target.y +
            "L" +
            d.source.x +
            "," +
            d.source.y;
        }
        return dValue;
      });
    }

    // Terminate the force layout when this cell re-runs.

    // invalidation.then(() => simulation.stop());

    var nodeById = new Map();

    var chart = Object.assign(svg.node(), {
      update({ nodes, links }) {
        prepareNodeRelations(nodes, links);
        // Make a shallow copy to protect against mutation, while
        // recycling old nodes to preserve position and velocity.
        const old = new Map(node.data().map((d) => [d.id, d]));
        nodes = nodes.map((d) => Object.assign(old.get(d.id) || {}, d));
        links = links.map((d) => Object.assign({}, d));

        node = node
          .data(nodes, (d) => d.id)
          .join((group) => {
            var enter = group.append("g").attr("class", "chart_group");

            enter.call(drag(simulation));

            enter
              .append("circle")
              .attr("r", (d) => {
                // if (d.termType === "Literal") {
                //   return 12;
                // } else {
                //   return 18;
                // }
                return 18;
              })
              .attr("fill", (d) => color(d.id))
              .on("click", clicked)
              .on("mouseover", function (d, n) {
                if(isOpened(n.id)){
                  d3.select(this).style("fill", "gray");
                }else{
                  d3.select(this).style("fill", "red");
                }
              })
              .on("mouseout", function (d) {
                d3.select(this).style("fill", (d) => color(d.id));
              });

            /*
enter.append('rect').transition().duration(500).attr('width', 60)
                .attr('height', 80)
                .attr('x', -30)
                .attr('y', -40)
                .style('fill', 'white')
                .attr('stroke', 'black')
enter.append('text').text('This is some information about whatever')
                .attr('x', 50)
                .attr('y', 150)
                .attr('fill', 'black')
*/
            //append symbol
            enter
              .append("text")
              .attr("alignment-baseline", "central")
              .attr("text-anchor", "middle")
              .attr("font-family", "FontAwesome")
              .attr("font-size", function (d) {
                return "20px";
              })
              .attr("stroke", "none")
              .attr("fill", "#FFF")
              // .attr("stroke-opacity", 0.2)
              .attr("pointer-events", "none")
              .text(function (d) {
                return getNodeIconText(d);
              }); //f3c5
            // .on("click", clicked)
            // .on("mouseover", function (d) {
            //   d3.select(this).style("fill", "red");
            // })
            // .on("mouseout", function (d) {
            //   d3.select(this).style("fill", color(d.id));
            // });

            //append label -replaced by label2
            // enter
            //   .append("text")
            //   .text(function (d) {
            //     return d.label;
            //   })
            //   .style("font-weight", "bold")
            //   .attr("x", 8)
            //   .attr("y", 3);

            //append tooltip?
            enter.append("title").text(function (d) {
              return d.id;
            });
            //    enter.append("rect").attr("class","group_rect");
            // enter.append("text").attr("class","group_text");
            // enter.append("image").attr("class","group_image");
            return enter;
          });

        // link = link.data(links, (d) => [d.source, d.target]).join("line");

        nodeLabel2 = nodeLabel2
          .data(links, (d) => [d.source, d.target, d.type]) //, (d) => [d.source, d.target]
          .join((group) => {
            var enter = group.append("g").attr("class", "chart_label2_group");

            enter
              .append("text")
              .text(function (d) {
                //get node label from target node
                return nodeById.get(d.target).label;
              })
              .style("font-weight", "bold")
              // .attr("x", "50%")
              .attr("y", 0)
              .attr("text-anchor", "middle")
             // .attr("dominant-baseline", "middle");

              //node sub label

              enter
              .append("text")
              .text(function (d) {
                //comment is only shown for not opened nodes, because it visualy overlaps opened nodes
                  if(!isOpened(d.target)){
                    return nodeById.get(d.target).comment;
                  }else{
                    return undefined;
                  }

              })
              .style("font-size", "8px")
              // .attr("x", "50%")
              .attr("y", 14)
              .attr("text-anchor", "middle")
            //  .attr("dominant-baseline", "middle");


            return enter;
          });

        path = path.data(links).join((group) => {
          // var enter = group.append("g").attr("class", "path_group");
          var enter = group
            .append("path")
            .attr("class", function (d) {
              return "link " + d.type;
            })
            .attr("id", function (d, i) {
              return "linkId_" + i;
            })
            .attr("marker-end", function (d) {
              return "url(#" + d.type + ")";
            });
          return enter;
        });

        linktext = linktext
          .data(links, (d) => [d.source, d.target, d.type]) //, (d) => [d.source, d.target]
          .join((group) => {
            //var enter = group.append("g").attr("class", "path_group");

            var enter = group
              .append("g")
              .attr("class", "linklabelholder")
              .append("text")
              .attr("class", "linklabel")
              // .attr("x", "40")
              // .attr("y", "-25")
              .attr("text-anchor", "middle");
            enter
              .append("textPath")
              .attr("startOffset", "50%")
              .attr("xlink:href", function (d, i) {
                return "#linkId_" + i;
              })
              .text(function (d) {
                return d.label;
              });
            return enter;
          });
        simulation.nodes(nodes);
        simulation.force("link").links(links);
        simulation.alpha(1).restart();
      },
    });

    /**
     * Dragging
     * @param simulation
     */

    function drag(simulation) {
      function dragstarted(event) {
        if (!event.active) simulation.alphaTarget(0.3).restart();
        event.subject.fx = event.subject.x;
        event.subject.fy = event.subject.y;
      }

      function dragged(event) {
        event.subject.fx = event.x;
        event.subject.fy = event.y;
      }

      function dragended(event) {
        if (!event.active) simulation.alphaTarget(0);
        event.subject.fx = null;
        event.subject.fy = null;
      }

      return d3
        .drag()
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended);
    }

    //zoom

    svg.call(
      d3
        .zoom()
        .extent([
          [0, 0],
          [width, height],
        ])
        .scaleExtent([0.2, 8])
        .on("zoom", zoomed)
    );

    function zoomed({ transform }) {
      baseG.attr("transform", transform);
    }

    function isOpened(id) {
      return openedNodes.has(id) || openedLists.has(id);
    }

    let rootNodeId="https://pyszkowski.com/cv";

    let openedNodes = new Set();
    openedNodes.add(rootNodeId); // pys:me

    //used to speed up search for opened link nodes as the key in the openedListsMap is the parent of the opened link BlankNode
    let openedLists = new Set();

    //map where the key is a node that is conected to the openend link BlankNode via a predicate that is the value in the map
    let openedListsMap = new Map();

    async function fetchNodes() {
      let prodUrl="https://rdf-resume.herokuapp.com/graph-repository";
      let localUrl="http://localhost:3033/graph-repository";

      fetch(prodUrl, {
        headers: new Headers({ "Content-Type": "application/json" }),
        method: "POST",
        body: JSON.stringify({
          query:
            `
      PREFIX pys: <https://pyszkowski.com/>
      PREFIX foaf: <http://xmlns.com/foaf/0.1/>
      PREFIX cv: <http://rdfs.org/resume-rdf/cv.rdfs#>
      PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
      SELECT ?s ?p ?o ?t ?l ?c
      WHERE 
      {
        {
          values (?s) {` +
            Array.from(openedNodes)
              .map((id) => "(<" + id + "> )")
              .join(" ") +
            `} 
              
          ?s ?p ?o 
          OPTIONAL {?o <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> ?t .}
          OPTIONAL {?o <http://www.w3.org/1999/02/22-rdf-syntax-ns#comment> ?c .}
        }` 
        +
        Array.from(openedListsMap.entries()).map(([baseSubject, predicate]) => `
    UNION
    {
      values (?s ?p) { (<${baseSubject}> <${predicate}>) } 
     ?s ?p ?o .
      ?s <${predicate}>/<http://www.w3.org/1999/02/22-rdf-syntax-ns#rest>*/<http://www.w3.org/1999/02/22-rdf-syntax-ns#first>  ?l .
    }`
            ).join("\n") +
            `}`,

          // `
          //     PREFIX pys: <https://pyszkowski.com/>
          //     PREFIX foaf: <http://xmlns.com/foaf/0.1/>
          //     PREFIX cv: <http://rdfs.org/resume-rdf/cv.rdfs#>
          //     PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
          //     SELECT ?s ?p ?o ?t ?l
          //     WHERE
          //     {
          //         ?s ?p ?o
          //         OPTIONAL {?o <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> ?t .}

          // }`
        }),
      })
        .then((r) => r.json())
        .then((graph) => {
          chart.update(graph);
        });
    }

    function closeChildrenNodes(nodeId, depth){
      if(openedNodes.has(nodeId)){
        let node = nodeById.get(nodeId);
        if(node.sourceLinks){
          node.sourceLinks.forEach(link=> closeChildrenNodes(link.target, depth + 1));
        }
        if(nodeId !== rootNodeId){
          openedNodes.delete(nodeId);
        }
      }
      if(openedListsMap.has(nodeId)){
        openedListsMap.delete(nodeId);
      }
      if(openedLists.has(nodeId)){
        openedLists.delete(nodeId);
      }
    }

    async function clicked(event, d) {
      if (d.termType === "NamedNode") {
        //|| d.termType === "BlankNode"
        //only for nodes with id
        let nodeId = d.id;
        if (openedNodes.has(nodeId)) {
          //openedNodes.delete(nodeId);
          closeChildrenNodes(nodeId);
        } else {
          openedNodes.add(nodeId);
        }
      }
      //(<https://pyszkowski.com/workHistory/Empolis> <http://rdfs.org/resume-rdf/cv.rdfs#jobDescription>)
      if (d.termType === "BlankNode") { //todo: proper check it is a list
        if (d.targetLinks && d.targetLinks.size == 1) {
          let nodeId = d.id;
          let targetLink = d.targetLinks.values().next().value;
          let baseSubject = targetLink.source;
          let predicate = targetLink.type;
          if(openedListsMap.has(baseSubject))
          {
            openedLists.delete(nodeId);//used only to speed up search
            openedListsMap.delete(baseSubject)
          }else{
            openedLists.add(nodeId);
          openedListsMap.set(baseSubject, predicate);
          }
        }
      }
      await fetchNodes();
    }

    function prepareNodeRelations(nodes, links) {
      //prepare a map of id to nodes for quicer access
      nodes.forEach(function (node) {
        nodeById.set(node.id, node);
      });

      links.forEach(function (link) {
        let sourceNode = nodeById.get(link.source);
        let targetNode = nodeById.get(link.target);

        let sourceLinks = sourceNode.sourceLinks;
        if (!sourceLinks) {
          sourceLinks = new Set();
          sourceNode.sourceLinks = sourceLinks;
        }
        sourceLinks.add(link);

        let targetLinks = targetNode.targetLinks;
        if (!targetLinks) {
          targetLinks = new Set();
          targetNode.targetLinks = targetLinks;
        }
        targetLinks.add(link);
      });
    }

    function getNodeIconText(d) {
      let linkType;
      if (d.targetLinks && d.targetLinks.size == 1) {
        let targetLink = d.targetLinks.values().next().value;
        linkType = targetLink.type;
      } else {
        linkType = "UNKNOWN";
      }

      switch (d.type) {
        case "http://xmlns.com/foaf/0.1/Person":
          return "\uf2bb"; //"\uf406";
        case "http://rdfs.org/resume-rdf/cv.rdfs#WorkHistory":
          return "\uf2b5"; // "\uf508"; // "\uf64a";
        case "http://rdfs.org/resume-rdf/cv.rdfs#Company":
          return "\uf1ad";
        case "http://rdfs.org/resume-rdf/cv.rdfs#CV":
          return "\uf508";
      }

      switch (linkType) {
        case "http://rdfs.org/resume-rdf/cv.rdfs#startDate":
          return "\uf251";
        case "http://rdfs.org/resume-rdf/cv.rdfs#endDate":
          return "\uf253";
        case "http://rdfs.org/resume-rdf/cv.rdfs#jobTitle":
          return "\uf0e8";
        case "http://rdfs.org/resume-rdf/cv.rdfs#hasNationality":
          return "\uf0ac";
        case "http://xmlns.com/foaf/0.1/gender":
          switch (d.id) {
            case '"man"^^http://www.w3.org/2000/01/rdf-schema#Literal':
              return "\uf183";
            case '"woman"^^http://www.w3.org/2000/01/rdf-schema#Literal':
              return "\uf182";
          }
        case "http://schema.org/birthPlace":
          return "\uf3c5";
        case "http://rdfs.org/resume-rdf/cv.rdfs#jobDescription":
          return "\uf0ae";
      }

      return "\uf083";
    }
    // let graph1 = {
    //   nodes: [{ id: "a" }, { id: "b" }, { id: "c" }, { id: "d" }, { id: "e" }],
    //   links: [
    //     { source: "a", target: "b",type:"hasJob" },
    //     { source: "b", target: "c",type:"hasJob" },
    //     { source: "c", target: "d",type:"hasJob" },
    //     { source: "c", target: "e",type:"hasJob" },
    //   ],
    // };

    // chart.update(graph1);
    await fetchNodes();

    // setTimeout(() => {
    //   chart.update(graph1);
    // }, 0);

    // setTimeout(()=>{
    // chart.update(graph1)}, 6000);
  }); //end mount

  onDestroy(() => 
  {if(simulation){
    simulation.stop();
  }
  });
</script>

<!--    // background-color: steelblue;-->
<style>
  :global(.chart) {
    font: 12px sans-serif;
    text-align: right;
    padding: 3px;
    margin: 1px;
    color: black;

    border-color: lightgray;
    border-style: solid;
    border-width: 1px;
    background: #f6fafd content-box;
    padding: 3px;

  }

  :global(.links line) {
    stroke: #999;
    stroke-opacity: 0.6;
  }

  :global(.nodes circle) {
    fill: #000000;
    stroke: rgb(0, 0, 0);
    stroke-width: 1px;
  }

  :global(.text) {
    fill: #000000;
    stroke: #000000;
    stroke-width: 0.5px;
    font-size: 10px;
  }

  :global(.linklabel) {
    fill: #000000;
    stroke: #000000;
    stroke-width: 0.5px;
    font-size: 8px;
  }
</style>



<div id="chart"></div>
