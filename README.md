# bachelor

This is a project I did for fun using Mike Bostock's D3 force layout.

  
<iframe src="http://bl.ocks.org/mbostock/raw/4061502/0a200ddf998aa75dfdb1ff32e16b680a15e5cb01/" width="600" height="400" marginwidth="0" marginheight="0" scrolling="no"></iframe>  

<iframe src="http://bl.ocks.org/mbostock/raw/4061502/0a200ddf998aa75dfdb1ff32e16b680a15e5cb01/" marginwidth="0" marginheight="0" scrolling="no"></iframe>

<!DOCTYPE html>
<meta charset="utf-8">
<style>
.node {
  stroke: #fff;
  stroke-width: 1.5px;
}
.link {
  stroke: #999;
  stroke-opacity: .6;
}
</style>
<body>
<h2>Bachelor Co-Occurrences</h2>
<script src="//d3js.org/d3.v3.min.js"></script>
<script>
var width = 990,
	height = 900;
var color = d3.scale.category20();
var force = d3.layout.force()
	.charge(-120)
	.linkDistance(30)
	.size([width, height]);
var svg = d3.select("body").append("svg")
	.attr("width", width)
	.attr("height", height);
d3.json("links_reduced.json", function(error, graph) {
    var edges = [];
    graph.links.forEach(function(e) { 
    var sourceNode = graph.nodes.filter(function(n) { return n.Name === e.source; })[0],
    targetNode = graph.nodes.filter(function(n) { return n.Name === e.target; })[0];
    	
    edges.push({source: sourceNode, target: targetNode, value: e.Value});
    });
    
   force
      .nodes(graph.nodes)
      .links(edges)
      .start();
	var link = svg.selectAll(".link")
		.data(edges)
	  .enter().append('line')
		.attr('class', 'link')
		.style('stroke-width', function(d) {return Math.sqrt(d.value); });
	var node = svg.selectAll('.node')
		.data(graph.nodes)
	 .enter().append('circle')
		.attr('class', 'node')
		.attr('r', 3)
		.style('fill', function(d) { return color(d.value); })
		.call(force.drag);
	node.append('title')
		.text(function(d) {return d.Name;});
	force.on('tick', function() {
		link.attr('x1', function(d) {return d.source.x; })
			.attr('y1', function(d) {return d.source.y; })
			.attr('x2', function(d) {return d.target.x; })
			.attr('y2', function(d) {return d.target.y; });
		
		node.attr('cx', function(d) {return d.x; })
			.attr('cy', function(d) {return d.y; });
	});
});
</script>
