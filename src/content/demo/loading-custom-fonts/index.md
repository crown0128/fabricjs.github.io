---
date: '2017-10-24'
title: 'Loading custom font'
description: 'Loading custom fonts in FabricJS'
thumbnail: 'load-custom-fonts.png'
tags: ['fonts', 'typography', 'custom']
---

<div
  class="codepen-later"
  data-editable="true"
  data-height="500"
  data-default-tab="result"
  data-prefill='{
    "scripts": ["https://unpkg.com/fabric@4.0.0-beta.12/dist/fabric.js", "http://rawgit.com/bramstein/fontfaceobserver/master/fontfaceobserver.js"]
  }'
>
<pre data-lang="css" data-options-autoprefixer="true">
.controls {
	display: inline-block;
}
canvas {
  border: 1px solid #999;
}
</pre>
<pre data-lang="html">
  <canvas id="c" width="500" height="500" style="border:1px solid #ccc"></canvas>
  <div class="controls">
    <p>
      Font-family: <select id="font-family"></select>
    </p>
  </div>
</pre>
<pre data-lang="js">
/*
When working with custom fonts on a fabric canvas, it is recommended to
use a font preloader. Not doing so is likely to cause texts that are
imported onto the canvas to be rendered with a wrong (default) font. It
can also cause you to see a FOUT (Flash of Unstyled Text) right after
changing the font of a text. The reason behind this is that the browser
will only load a font after it is used in the DOM. Preloading fonts
prevents this from happening. In this example we are using Font Face
Observer (https://github.com/bramstein/fontfaceobserver) to preload
Google Fonts, using the following steps:
- Add the custom fonts using @import in your CSS
- Make an array containing the names of all the custom fonts
- Load all the custom fonts using a promise or load them by request
- When the promise is fulfilled, initialize the fabric canvas
*/

var canvas = new fabric.Canvas('c');
// Define an array with all fonts
var fonts = ["Pacifico", "VT323", "Quicksand", "Inconsolata"];

var textbox = new fabric.Textbox('Lorum ipsum dolor sit amet', {
left: 50,
top: 50,
width: 150,
fontSize: 20
});
canvas.add(textbox).setActiveObject(textbox);
fonts.unshift('Times New Roman');
// Populate the fontFamily select
var select = document.getElementById("font-family");
fonts.forEach(function(font) {
var option = document.createElement('option');
option.innerHTML = font;
option.value = font;
select.appendChild(option);
});

// Apply selected font on change
document.getElementById('font-family').onchange = function() {
if (this.value !== 'Times New Roman') {
loadAndUse(this.value);
} else {
canvas.getActiveObject().set("fontFamily", this.value);
canvas.requestRenderAll();
}
};

function loadAndUse(font) {
var myfont = new FontFaceObserver(font)
myfont.load()
.then(function() {
// when font is loaded, use it.
canvas.getActiveObject().set("fontFamily", font);
canvas.requestRenderAll();
}).catch(function(e) {
console.log(e)
alert('font loading failed ' + font);
});
}

// Load all fonts using Font Face Observer

</pre>
</div>
