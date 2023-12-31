---
date: '2013-02-08'
title: 'Solar system'
description: 'Solar system using FabricJS'
thumbnail: 'solar-system.png'
tags: ['planets', 'solar-system']
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
<pre data-lang="css" data-options-autoprefixer="true">
.canvas-container { background: url('http://www.fabricjs.com/assets/sky.png'); }
#bd-wrapper { min-width: 1600px; }
</pre>
<pre data-lang="html">
  <p>Adaptation of <a href="http://habrahabr.ru/post/163893">LibCanvas demo</a></p>
  <canvas id="c" width="840" height="840" style="border:1px solid #aaa"></canvas>
</pre>
<pre data-lang="js">
(function() {
	fabric.Object.prototype.originX = fabric.Object.prototype.originY = 'center';

    var canvas = this.__canvas = new fabric.Canvas('c', {
    	hoverCursor: 'pointer',
    	selection: false,
    	perPixelTargetFind: true,
    	targetFindTolerance: 5
    });

    // load sun and center it
    fabric.Image.fromURL('http://www.fabricjs.com/assets/sun.png', function(sunImg) {
    	canvas.add(sunImg);
    	sunImg.center();
    });

    var planetSize = 26,
    	totalPlanets = 12,
    	rotationSpeed = 20000,
    	orbits = [],
    	planets = [],
    	planetNames = ['Selene', 'Mimas', 'Ares', 'Enceladus', 'Tethys', 'Dione',
    		'Zeus', 'Rhea', 'Titan', 'Janus', 'Hyperion', 'Iapetus'
    	];

    var hoverCircle = new fabric.Circle({
    	radius: 13,
    	fill: '#000',
    	stroke: 'rgb(0,192,255)',
    	strokeWidth: 3,
    	left: -100,
    	top: -100
    });

    var planetLabel = new fabric.Text('', {
    	fill: '#fff',
    	fontSize: 16,
    	fontFamily: 'Open Sans',
    	textBackgroundColor: '#002244'
    });

    // load sprite with planets
    fabric.Image.fromURL('http://fabricjs.github.io/assets/planets.png', function(planetsImg) {

    	// temp canvas to generate planet images
    	var tempCanvas = new fabric.StaticCanvas();

    	// only to fit one planet onto temp canvas
    	tempCanvas.setDimensions({
    		width: planetSize,
    		height: planetSize
    	});

    	// make sure image is drawn from left/top corner
    	planetsImg.originX = 'left';
    	planetsImg.originY = 'top';

    	// add it onto temp canvas
    	tempCanvas.add(planetsImg);

    	for (var i = 0; i < totalPlanets; i++) {
    		createOrbit(i);
    	}
    	canvas.add(hoverCircle);

    	for (var i = 0; i < totalPlanets; i++) {
    		createPlanet(i, planetsImg, tempCanvas);
    	}

    	canvas.add(planetLabel);
    }, { crossOrigin: 'anonymous' });

    function createOrbit(i) {
    	var orbit = new fabric.Circle({
    		radius: 26 * i + 90,
    		left: canvas.getWidth() / 2,
    		top: canvas.getHeight() / 2,
    		fill: '',
    		stroke: 'rgba(0,192,255,0.5)',
    		hasBorders: false,
    		hasControls: false,
    		lockMovementX: true,
    		lockMovementY: true,
    		index: i
    	});
    	canvas.add(orbit);
    	orbits.push(orbit);
    }

    function createPlanet(i, planetsImg, tempCanvas) {

    	// offset planets sprite to fit each of the planets onto it
    	planetsImg.left = -planetSize * i;
    	planetsImg.setCoords();
    	tempCanvas.renderAll();

    	// get data url for that planet
    	var img = new Image();
    	img.onload = function() {
    		// create image of a planet from data url
    		var oImg = new fabric.Image(img, {

    			name: planetNames[i],
    			index: i,
    			scaleX: 1 / canvas.getRetinaScaling(),
    			scaleY: 1 / canvas.getRetinaScaling(),
    			// position planet 90px from canvas center and 26px from previous planet
    			left: (canvas.getWidth() / 2) - 90 - (planetSize * i),
    			top: canvas.getHeight() / 2,

    			// remove borders and corners but leaving object available for events
    			hasBorders: false,
    			hasControls: false
    		});
    		canvas.add(oImg);
    		planets.push(oImg);
    		animatePlanet(oImg, i);
    	}
    	img.src = tempCanvas.toDataURL();
    }

    function animatePlanet(oImg, planetIndex) {

    	var radius = planetIndex * 26 + 90,

    		// rotate around canvas center
    		cx = canvas.getWidth() / 2,
    		cy = canvas.getHeight() / 2,

    		// speed of rotation slows down for further planets
    		duration = (planetIndex + 1) * rotationSpeed,

    		// randomize starting angle to avoid planets starting on one line
    		startAngle = fabric.util.getRandomInt(-180, 0),
    		endAngle = startAngle + 359;

    	(function animate() {

    		fabric.util.animate({
    			startValue: startAngle,
    			endValue: endAngle,
    			duration: duration,

    			// linear movement
    			easing: function(t, b, c, d) {
    				return c * t / d + b;
    			},

    			onChange: function(angle) {
    				angle = fabric.util.degreesToRadians(angle);

    				var x = cx + radius * Math.cos(angle);
    				var y = cy + radius * Math.sin(angle);

    				oImg.set({
    					left: x,
    					top: y
    				}).setCoords();

    				// only render once
    				if (planetIndex === totalPlanets - 1) {
    					canvas.renderAll();
    				}
    			},
    			onComplete: animate
    		});
    	})();
    }

    var hoverTarget, prevHoverTarget;

    canvas.on('mouse:over', function(options) {
    	hoverTarget = options.target;
    });

    canvas.on('mouse:out', function(options) {
    	hoverTarget = null;
    	prevHoverTarget = options.target;
    });

    canvas.on('after:render', function() {
    	orbits.forEach(function(orbit) {
    		orbit.strokeWidth = 1;
    		orbit.stroke = 'rgba(0,192,255,0.5)';
    	});
    	if (hoverTarget && !hoverTarget.text) {
    		var hoveredPlanet = planets[hoverTarget.index];
    		var hoveredOrbit = orbits[hoveredPlanet.index];

    		hoveredOrbit.set({
    			strokeWidth: 3,
    			stroke: 'rgb(0,192,255)'
    		});

    		hoverCircle.set({
    			left: hoveredPlanet.left,
    			top: hoveredPlanet.top
    		});

    		planetLabel.set({
    			left: hoveredPlanet.left + 50,
    			top: hoveredPlanet.top + 20,
    			text: hoveredPlanet.name
    		});
    	} else {
    		hoverCircle.set({
    			left: -100,
    			top: -100
    		});
    		planetLabel.set({
    			left: -100,
    			top: -100
    		});
    	}
    });

})();

</pre>
</div>
