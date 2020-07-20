<template>
  <!-- a container that is used by yFiles -->
  <div id="graph-component"></div>
</template>

<script>
    import {
        Arrow, ArrowType,
        BevelNodeStyle, Class,
        Color, Fill, GraphBuilder,
        GraphComponent,
        GraphEditorInputMode, IEdge, LayoutExecutor, License, MarkupLabelStyle, OrganicLayout,
        PolylineEdgeStyle,
        Stroke
    } from 'yfiles'
  import licenseData from '../../license.json'
  License.value = licenseData
  export default {
    name: 'GraphComponent',
    mounted() {
      Class.ensure(LayoutExecutor)
      this.graphComponent = new GraphComponent('#graph-component')
      var myInputMode = new GraphEditorInputMode()
      myInputMode.allowCreateNode = false;
      myInputMode.allowEditLabel = true;
      myInputMode.allowAddLabel = false;
      myInputMode.allowCreateEdge = true;
      myInputMode.allowCreateBend = false;
      myInputMode.allowClipboardOperations = false;
      myInputMode.addLabelTextChangedListener((sender, evt) => {
        if (evt.owner instanceof IEdge) {
          fetch("http://localhost:8080/maxFlow/changeweight", {
            mode: "cors",
            method: "post",
            body: JSON.stringify({
              from: evt.item.owner.tag.from,
              to: evt.item.owner.tag.to,
              weight: evt.item.text
            })
          }).then((response) => {
            response.text().then(() => {
              console.log("Weight changed")
              this.$store.dispatch("getAnswer")
            })
          })
        }
      })
      this.graphComponent.inputMode = myInputMode
      // create some graph elements
      this.createSampleGraph(this.graphComponent.graph)
    },
    methods: {
        buildGraph(graph, nodesSource, edgesSource) {
            let graphBuilder = new GraphBuilder(graph)
            graphBuilder.createNodesSource({
                data: nodesSource,
                id: 'id',
                labels: 'id'
            })
            graphBuilder.createEdgesSource({
                data: edgesSource,
                sourceId: 'from',
                targetId: 'to',
                labels: 'weight'
            })
            graphBuilder.buildGraph()
            this.graphComponent.morphLayout(new OrganicLayout({
                considerNodeLabels: true,
                minimumNodeDistance: 127,
                nodeEdgeOverlapAvoided: true,
                nodeOverlapsAllowed: false,
                preferredEdgeLength: 127,
                smartComponentLayout: true
            }))
            // center the newly created graph
            this.graphComponent.fitGraphBounds()
        },
        createSampleGraph(graph) {
        graph.edgeDefaults.labels.style = new MarkupLabelStyle({
          backgroundFill: Fill.LIGHT_GRAY,
          autoFlip: true
        })
        graph.edgeDefaults.labels.autoAdjustPreferredSize = true
        graph.nodeDefaults.style = new BevelNodeStyle({
          color: Color.LIGHT_GREEN
        })
        graph.edgeDefaults.style = new PolylineEdgeStyle({
          stroke: Stroke.DARK_GREEN,
          targetArrow: new Arrow({
            stroke: Stroke.DARK_GREEN,
            type: ArrowType.TRIANGLE,
            scale: 1.5
          })
        })
        let nodesSource = null
        fetch('http://localhost:8080/maxFlow/getvertices', {mode: "cors"})
                .then((response) => {
                  response.json()
                          .then((json) => {
                            nodesSource = json
                          })
                })
        let edgesSource = null
        fetch('http://localhost:8080/maxFlow/getrelations', {mode: "cors"})
        .then((response) => {
          response.json()
          .then((json) => {
            edgesSource = json
            this.buildGraph(graph, nodesSource, edgesSource)
        })
      })
    }
  }
  }
</script>

<style scoped>
  /* yfiles.css is an integral part of the library and must be loaded wherever a yFiles GraphComponent is created */
  @import '~yfiles/yfiles.css';
  #graph-component {
    width: 100%;
    height: 93%;
  }
</style>
