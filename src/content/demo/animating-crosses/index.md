---
date: '2011-9-1'
title: 'Animating crosses'
description: 'Animating crosses using FabricJS'
thumbnail: 'animating-crosses.png'
tags: ['animation']
---

<div
  class="codepen-later"
  data-editable="true"
  data-height="500"
  data-default-tab="result"
  data-prefill='{
    "scripts": ["https://unpkg.com/fabric@4.0.0-rc.1/dist/fabric.js"]
  }'
>
<pre data-lang="html">
  <canvas id="c" width="600" height="500"></canvas>
</pre>
<pre data-lang="js">
(function() {
	var canvas = this.__canvas = new fabric.Canvas('c');
	fabric.Object.prototype.transparentCorners = false;

    var Cross = fabric.util.createClass(fabric.Object, {
    	objectCaching: false,
    	initialize: function(options) {
    		this.callSuper('initialize', options);
    		this.animDirection = 'up';

    		this.width = 100;
    		this.height = 100;

    		this.w1 = this.h2 = 100;
    		this.h1 = this.w2 = 30;
    	},

    	animateWidthHeight: function() {
    		var interval = 2;

    		if (this.h2 >= 30 && this.h2 <= 100) {
    			var actualInterval = (this.animDirection === 'up' ? interval : -interval);
    			this.h2 += actualInterval;
    			this.w1 += actualInterval;
    		}

    		if (this.h2 >= 100) {
    			this.animDirection = 'down';
    			this.h2 -= interval;
    			this.w1 -= interval;
    		}
    		if (this.h2 <= 30) {
    			this.animDirection = 'up';
    			this.h2 += interval;
    			this.w1 += interval;
    		}
    	},

    	_render: function(ctx) {
    		ctx.fillRect(-this.w1 / 2, -this.h1 / 2, this.w1, this.h1);
    		ctx.fillRect(-this.w2 / 2, -this.h2 / 2, this.w2, this.h2);
    	}
    });

    canvas.add(
    	new Cross({
    		top: 100,
    		left: 100
    	}),
    	new Cross({
    		top: 140,
    		left: 230
    	}),
    	new Cross({
    		top: 300,
    		left: 210
    	}),
    	new Cross({
    		top: 40,
    		left: 400
    	}),
    	new Cross({
    		top: 450,
    		left: 400
    	})
    );

    setTimeout(function animate() {
    	canvas.forEachObject(function(obj) {
    		obj.animateWidthHeight();
    		obj.dirty = true;
    	});
    	canvas.renderAll();
    	setTimeout(animate, 10);
    }, 10);

})();

</pre>
</div>
