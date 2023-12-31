---
date: '2013-12-07'
title: 'Matrix transformations'
description: 'Matrix transformations in FabricJS'
thumbnail: 'matrix-transformation.png'
tags: ['matrix', 'transforms']
---

TransformMatrix was the only way to represent some SVG transformations. Since 2.0 the transformation can be decomponed and used with the normal object properties.

This is the code behind the merge button. There are no plans to update controls to respect transformMatrix, but probably deprecate it.

```javascript
var obj = canvas.item(0)
// get the current transform matrix, from object properties.
var currentT = obj.calcTransformMatrix()
// get the transformMatrix array
var transformMatrix = obj.transformMatrix
// multiply the matrices together to get the combined transform
var mT = fabric.util.multiplyTransformMatrices(currentT, transformMatrix)
// Unfold the matrix in a combination of scaleX, scaleY, skewX, skewY...
var options = fabric.util.qrDecompose(mT)
var newCenter = {
  x: options.translateX,
  y: options.translateY,
}
// reset transformMatrix to identity and resets flips since negative scale
// resulting from decompose, will automatically set them.
obj.transformMatrix = [1, 0, 0, 1, 0, 0]
obj.flipX = false
obj.flipY = false
obj.set(options)
// position the object in the center given from translateX and translateY
obj.setPositionByOrigin(newCenter, 'center', 'center')
```

This demo shows the result of applying `transformMatrix` to a Fabric object. For more on `transformMatrix`, see [this excellent tutorial](http://www.senocular.com/flash/tutorials/transformmatrix/)

<div
  class="codepen-later"
  data-editable="true"
  data-height="500"
  data-default-tab="result"
  data-prefill='{
    "scripts": ["https://unpkg.com/fabric@4.0.0-beta.12/dist/fabric.js"]
  }'
>
<pre data-lang="css" data-options-autoprefixer="true">
td { color: green; font-size: 24px; }
td:first-child:before {
  content: '[';
  color: red;
  margin-right: 5px;
  font-weight: bold;
  font-size: 24px;
}
td:first-child:after {
  content: ',';
  color: red;
  font-weight: bold;
  font-size: 24px;
  margin-left: 5px;
}
td:last-child:after {
  content: ']';
  color: red;
  margin-right: 5px;
  font-weight: bold;
  font-size: 24px;
  margin-left: 5px;
}
.controls {
  display: inline-block;
  vertical-align: top;
}
.controls code {
  display: block;
  margin-top: 10px;
  margin-left: 5px;
}
td span {
  display: inline-block;
  width: 30px;
}
input {
  width: 100px;
  font-size: 24px;
  font-family: monospace;
  color: #888;
}
</pre>
<pre data-lang="html">
<canvas id="canvas" width="600" height="600" style="border:1px solid #aaa"></canvas>

<div class="controls">
  <form id="numbers" >
  <table>
    <tr>
      <td><span>a:</span><input type="number" value="1" step="0.02" id="a"></td>
      <td><span>b:</span><input type="number" value="0" step="0.02" id="b"></td>
    </tr>
    <tr>
      <td><span>c:</span><input type="number" value="0" step="0.02" id="c"></td>
      <td><span>d:</span><input type="number" value="1" step="0.02" id="d"></td>
    </tr>
    <tr>
      <td><span>tx:</span><input type="number" value="0" step="1" id="tx"></td>
      <td><span>ty:</span><input type="number" value="0" step="1" id="ty"></td>
    </tr>
  </table>
  </form>
  <button id="merge" >Merge transform</button>
  <code>
    transformMatrix == [
    <span id="a-val">1</span>,
    <span id="b-val">0</span>,
    <span id="c-val">0</span>,
    <span id="d-val">1</span>,
    <span id="tx-val">0</span>,
    <span id="ty-val">0</span>
    ]<br />
    mergedProperties:<br />
    ScaleX: <span id="scaleX">1</span>,<br />
    ScaleY: <span id="scaleY">1</span>,<br />
    SkewX: <span id="skewX">0</span>,<br />
    SkewY: <span id="skewY">0</span>,<br />
    top: <span id="top">0</span>,<br />
    left: <span id="left">0</span>,<br />
    flipX: <span id="flipX">0</span>,<br />
    flipY: <span id="flipY">0</span>,<br />
    angle: <span id="angle">0</span>
  </code>
</div>
</pre>
<pre data-lang="js">
var demoImg = 'http://www.fabricjs.com/assets/printio.png';

(function() {
var canvas = this.\_\_canvas = new fabric.Canvas('canvas');
fabric.Image.fromURL(demoImg, function(image) {
canvas.add(image);
image.transformMatrix = [1, 0, 0, 1, 0, 0];
});

    function update() {
    	canvas.requestRenderAll();
    	var matrix = canvas.item(0).transformMatrix;
    	document.getElementById('a-val').innerHTML = matrix[0];
    	document.getElementById('b-val').innerHTML = matrix[1];
    	document.getElementById('c-val').innerHTML = matrix[2];
    	document.getElementById('d-val').innerHTML = matrix[3];
    	document.getElementById('tx-val').innerHTML = matrix[4];
    	document.getElementById('ty-val').innerHTML = matrix[5];
    	document.getElementById('scaleX').innerHTML = canvas.item(0).scaleX;
    	document.getElementById('scaleY').innerHTML = canvas.item(0).scaleY;
    	document.getElementById('skewX').innerHTML = canvas.item(0).skewX;
    	document.getElementById('skewY').innerHTML = canvas.item(0).skewY;
    	document.getElementById('top').innerHTML = canvas.item(0).top;
    	document.getElementById('left').innerHTML = canvas.item(0).left;
    	document.getElementById('flipX').innerHTML = canvas.item(0).flipX;
    	document.getElementById('flipY').innerHTML = canvas.item(0).flipY;
    	document.getElementById('angle').innerHTML = canvas.item(0).angle;
    }

    document.getElementById('a').oninput = function() {
    	canvas.item(0).transformMatrix[0] = parseFloat(this.value, 10);
    	update();
    };
    document.getElementById('b').oninput = function() {
    	canvas.item(0).transformMatrix[1] = parseFloat(this.value, 10);
    	update();
    };
    document.getElementById('c').oninput = function() {
    	canvas.item(0).transformMatrix[2] = parseFloat(this.value, 10);
    	update();
    };
    document.getElementById('d').oninput = function() {
    	canvas.item(0).transformMatrix[3] = parseFloat(this.value, 10);
    	update();
    };
    document.getElementById('tx').oninput = function() {
    	canvas.item(0).transformMatrix[4] = parseFloat(this.value, 10);
    	update();
    };
    document.getElementById('ty').oninput = function() {
    	canvas.item(0).transformMatrix[5] = parseFloat(this.value, 10);
    	update();
    };
    document.getElementById('merge').onclick = function() {
    	var obj = canvas.item(0);
    	var currentT = obj.calcTransformMatrix();
    	var transformMatrix = obj.transformMatrix;
    	var mT = fabric.util.multiplyTransformMatrices(currentT, transformMatrix);
    	var options = fabric.util.qrDecompose(mT);
    	var newCenter = {
    		x: options.translateX,
    		y: options.translateY
    	};
    	obj.transformMatrix = [1, 0, 0, 1, 0, 0];
    	obj.flipX = false;
    	obj.flipY = false;
    	obj.set(options);
    	obj.setPositionByOrigin(newCenter, 'center', 'center');
    	document.getElementById('numbers').reset();
    	update();
    };

})();

</pre>
</div>
