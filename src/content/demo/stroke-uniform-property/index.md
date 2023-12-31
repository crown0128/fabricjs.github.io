---
date: '2019-02-17'
title: 'Stroke uniform property'
description: 'Demonstration of stroke'
thumbnail: 'stroke-uniform-property.png'
tags: ['property', 'stroke']
---

<div
  class="codepen-later"
  data-editable="true"
  data-height="500"
  data-default-tab="result"
  data-prefill='{
    "scripts": ["https://unpkg.com/fabric@4.0.0-beta.12/dist/fabric.js", "https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.15/lodash.js"]
  }'
>
<pre data-lang="css" data-options-autoprefixer="true">
.controls {
  display: inline-block;
}
</pre>
<pre data-lang="html">
  <canvas id="c" width="500" height="500" style="border:1px solid #ccc"></canvas>
  <div class="controls">
    <p>
      <button id="uniform" onclick="toggleUniform()">Toggle stroke uniform</button>
    </p>
  </div>
</pre>
<pre data-lang="js">
fabric.Object.prototype.noScaleCache = false;
/*
	strokeUniform works better without scalingCache
	Objects in group are not scaled directly, so stroke uniform will not have effect.
*/
function toggleUniform() {
	var aObject = canvas.getActiveObject();
	if (aObject.type === 'activeSelection') {
		aObject.getObjects().forEach(function(obj) {
			obj.set('strokeUniform', !obj.strokeUniform);
		});
	} else {
		aObject.set('strokeUniform', !aObject.strokeUniform);
	}
	canvas.requestRenderAll();
}

var canvas = this.\_\_canvas = new fabric.Canvas('c');
// create a rectangle object
var rect = new fabric.Rect({
left: 100,
top: 50,
fill: '#D81B60',
width: 50,
height: 50,
strokeWidth: 2,
stroke: "#880E4F",
rx: 10,
ry: 10,
angle: 45,
scaleX: 3,
scaleY: 3,
hasControls: true
});

canvas.add(rect);

var circle1 = new fabric.Circle({
radius: 65,
fill: '#039BE5',
left: 0,
stroke: 'red',
strokeWidth: 3
});

var circle2 = new fabric.Circle({
radius: 65,
fill: '#4FC3F7',
left: 110,
opacity: 0.7,
stroke: 'blue',
strokeWidth: 3,
strokeUniform: true
});

canvas.add(circle1);
canvas.add(circle2);

</pre>
</div>
