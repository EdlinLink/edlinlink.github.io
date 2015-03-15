---
layout: post
title:  "[CSIT 5400] Computer Graphics"
date:   2015-03-04 12:00:00
tags:	tech
myTag:	course

---

# [CSIT 5400] Computer Graphics

-----------------------------

### RGB Model

1. "True colour"(真彩色) is the common name for 24 bits (=3 byte) representation of a colour.

### RGBA

1. "A" refers to the *alpha*, means "transparency". 

2. Max alpha (solid), zero alpha (invisible).

### HSV

1. Hue: colour [0,360); Saturation: purity [0,1]; Value: brightness [0,1]

2. H=0 (Red). S=1 (Pure). V=1 (Brightness).

3. RGB to HSV

		min = MIN(r, g, b);
		max = MAX(r, g, b);
		v = max;
		delta = max - min;
		if(max != 0){
			s = delta/max;
		}
		else{
			//white
			s = 0;
			h = -1;
		}

		if(r == max)
			h = (g-b)/delta;
		else if(g == max)
			h = 2 + (b-r)/delta;
		else
			h = 4 + (r-g)/delta;
		h *= 60;

		if(h < 0)
			h += 360;

4. HSV to RGB

		if(s == 0){
			r = g = b = v
			return ;
		}
		h /= 60;
		i = floor(h);
		f = h - i;
		p = v * (1 - s);
		q = v * (1 - s*f);
		t = v * (1 - s*(1-f));

		switch(i){
			case 0:
				r = v;	g = t;	b = p;	break;
			case 1:
				r = q;	b = v;	b = p;	break;
			case 2:
				r = p;	b = v;	b = t;	break;
			case 3:
				r = p;	b = q;	b = v;	break;
			case 4:
				r = t;	b = p;	b = v;	break;
			default
				r = v;	b = p;	b = q;	break;
		}

### Observations

1. ***What is the hue of Black?***

	Black is the absence of colour. so the *H = -1* indicate an invalid state.
	
2. ***Zero saturation***

	When Saturation is 0, it means there is no color. Therefore, there is no hue, so when *saturation = 0*, *hue = -1*.

3. ***How to convert an image into grayscale?***
	+ R=G=B = average(R,G,B).
	+ R=G=B = V*255 = MAX(R,G,B). [V in this case range[0,1], but usually [0,255]]

-------------------------

### Color to Grayscale

1. Convert from color to grayscale

	+ Averaging method: R=G=B = (R+G+B)/3
	+ Max method: R=G=B = Max(R,G,B)

*The result of the two approaches is very similar but different.*

### Brightness

1. Adjusting brightness in RGB: adding **same** value to R, G, B value.

2. Adjusting brightness in HSV: increase V value range[0-1].

### Posterization (分色)

1. Deleting the lower bits from the total 8 bits, make it has the big area of same color.

2. Range from [min, max] to [min, half].

3. Total number of color = total possible of Red * total possible of Green * total possible of Blue

4. **n bit posterization** means deleting "n" bits from the lower bits.

5. Building mask

	bitmask=255; 
	for (int i = 7; i >= 0; i--){
		if ((i+1)<=number_posterization_bits)
			bitmask -= (int)(0x00000001 << i);
	}

-------------------------------

### Convolution

1. Code for applying a Kernel

		for(int y=0; y<height; y++) {
			for(int x=0; x<width; x++) {
				float sum = 0;
				for(int k=-n2; k<=n2; k++) {
					for(int j=-m2; j<=m2; j++) {
						sum = sum + kernel[m2+j][n2+k] * red(get(x+j,y+k); 
					}
				}
			output[x][y] = int(sum/ divisor); 
			}
		}

*The same process is applied to Green and Blue values.*

### Sharpen

1. Take the edge information and add it back to the origin image.

### Offset

1. Example code adjustment

	output[x][y] = int(sum/divisor) + offset;

2. It is the offset of the RGB value not the position.

--------------------------------------------------------

### Kuwahara Filter

1. It blurs an image, but preserves edges in the image. (basic step of "painting" or "cartoon" effect)

2. Dividing the 25 closest pixels into four areas, choose the area which is "smoothest" (has the least change), and then use the mean R, G and B values of that area center pixel result.

	+ The mean variance is calculated for each of the four regions
	+ Then the area with the least variance is selected
	+ The mean R, G and B values of that selected area are then calculated and used for the center pixel RGB result

3. Comic Effect: Do the Kuwahara processing and replace edges above a threshold with black.

----------------------

### Motion Tracking

1. Get the Mean Absolute Error (MAE).

### Shaking Camera Adjustment

1. Using the middle area as a reference, and searching for where it has moved

2. Use the pixels from the previous output frame in the missing pixels.

### Motion Blur

1. Average pixels across time

2.  The result is that parts of the video which contain movement are blurred, but parts of the image which do not move (constant image) are not blurred

---------------------------

### SVG

1. SVG Text

		< svg xmlns="http://www.w3.org/2000/svg"
			 xmlns:xlink="http://www.w3.org/1999/xlink">
			< text x="10" y="300" style="font-size:60px;fill:red" >
				This is SVG text </text>
		< /svg>

2. Parameter order

		< text x="10" y="300" style="font-size:60px; fill:red" >	}
																	|-> has the same meaning
		< text style="font-size:60px; fill:red" x="10" y="300" >	}

3. Coding Sytle

		< text x="10" y="300" style="font-size:60px; fill:red" >	}
																	|-> has the same meaning
		< text x="10" y="300" font-size="60px" fill="red" >			}

4. SVG Line

		< line x1="0" y1="0" x2="300" y2="200" stroke="black"/>

5. SVG Rectangle

		< rect width="700" height="100" x="0" y="200" fill="blue"/>

6. SVG Circle

		< circle cx="150" cy="150" r="100" fill="green"/>

7. SVG Ellipse

		< ellipse cx="200" cy="220" rx="80" ry="20" fill="red"/>

8. SVG Polygons

		< polygon points="120,20 50,230 190,150" style="fill:tomato; stroke:black"/>

9. SVG PolyLine

		< polyline points="120,20 50,230, 190,150" style="fill:tomato; stroke:black"/>

10. Bitmap Images

		< image xlink:href="hong_kong.jpg" x="10" y="10" width="300" height="300"/>

11. Grouping Thing Together

		< g id="my_group_name">
			<circle cx="100" cy="120" r="30" style="fill:red"/>
			<circle cx="200" cy="120" r="30" style="fill:red"/>
			<circle cx="150" cy="150" r="100" style="fill:none;stroke:blue;stroke-width:3"/>
		</g>

12. Creating Paths

		M = move to
		L = draw a straight line to
		H = draw a horizontal line to
		V = draw a vertical line to
		C = draw a curve to (uses a cubic Bezier curve)
		S = draw a smooth curve to
		Q = quadratic Bezier curve
		T = draw a smooth quadratic Bezier curve to
		A = draw an arc to
		Z = finish/ go back to the beginning

	*If the letter is lower case, it is a relative movement; If the letter is upper case, it is an absolute position*

		< path d="M 100,25 L 200,25 L 250,125 L 50,125 z" style="fill:pink;stroke:black"/>

		< path d="M 50,150 h 200 l -50,100 h -100 z" style="fill:yellow;stroke:black"/>

13. SVG Curved Path

		< path d="M 50,200 Q 135.5,210.5 125,125 T 200,50 z" style="fill:khaki;stroke:brown"/>

		< path d="M 225,225 h -50 a50,50 0 1,0 50,-50 z" style="fill:lightgreen;stroke:darkgreen"/>

14. Using Style

		< svg>
			< style type="text/css">
				rect {
					fill: yellow;
					fill-opacity: 0.5;
					stroke: orange;
					stroke-width: 5;
				}
				text {
					fill: red;
					font-family: Arial;
					font-size: 60px;
					text-anchor: middle;
				}
			</style>
			< rect x="50" y="50" width="200" height="100" rx="10" ry="10"/>
			< text x="150" y="120">SVG</text>
		</svg>

15. SVG Stroke

		< svg>
			< style type="text/css">
				text {
					fill: darkslateblue;
				}
				.style1 {
					fill: none;
					stroke: skyblue;
					stroke-width: 5;
					stroke-dasharray: 5;
				}
				.style2 {
					fill: none;
					stroke: slateblue;
					stroke-width: 5;
					stroke-dasharray: 10,5;
				}
				.style3 {
					fill: none;
					stroke: steelblue;
					stroke-width: 5;
					stroke-dasharray: 10,10,5,10;
				}
			</style>
			<text x="50" y="45">stroke-dasharray: 5</text>
			<line class="style1" x1="50" y1="55" x2="250" y2="55"/>
			<text x="50" y="95">stroke-dasharray: 10,5</text>
			<line class="style2" x1="50" y1="105" x2="250" y2="105"/>
			<text x="50" y="145">stroke-dasharray: 10,10,5,10</text>
			<line class="style3" x1="50" y1="155" x2="250" y2="155"/>
		</svg>

16. Definition

		<svg>
			<defs>
				<g id="Cloud">
					<circle cx="24" cy="36" r="15"/>
					<circle cx="41" cy="26" r="17"/>
					<circle cx="90" cy="40" r="13"/>
					<circle cx="105" cy="31" r="13"/>
					<ellipse cx="75" cy="20" rx="27" ry="20"/>
					<ellipse cx="56" cy="50" rx="25" ry="18"/>
				</g>
			</defs>
			<circle id="Sun" cx="125" cy="140" r="56" style="fill:orange"/>
			<use id="SunCloud1" xlink:href="#Cloud" x="20" y="20" />
			<use id="SunCloud2" xlink:href="#Cloud" x="0" y="130" />
			<use id="SunCloud3" xlink:href="#Cloud" x="150" y="210" />
		</svg>


17. Clip Path

		<defs>
			<clipPath id="some_text" >
			<text x="0" y="130" font-size="120px"> Clipping </text>
			<text x="10" y="220" font-size="120px"> Window </text>
			</clipPath>
		</defs>
		<image xlink:href="image.jpg" style="clip-path:url(#some_text)" width="800" height="400"/>

18. Pattern

		<defs>
			<pattern id="dotspattern" x="0" y="0" patternUnits="userSpaceOnUse" width="495px" height="495px">
				<image xlink:href="dots.png" x="0" y="0" width="495px" height="495px"/>
			</pattern>
		</defs>
		<rect style="fill:url(#dotspattern)" width="950" height="700" x="50" y="50" />

19. Gradient

		<defs>
			<linearGradient id="disc_gradient">
				<stop offset="0" style= "stop-color:white"/>
				<stop offset="1" style= "stop-color:purple"/>
		</linearGradient>
		</defs>
		<ellipse style="fill:url(#disc_gradient)" cx="400" cy="400" rx="50" ry="250" />


20. Filter

		<svg>
			<defs>
				<filter id="cool_effect">
					Definition of filter goes here
				</filter>
			</defs>
			<text style="filter:url(#cool_effect)">
				In this example, the defined filter
				is applied to these words
			</text>
		</svg>
	
### Layout Operatation

1. RGB Range Issues

+ Addition: 0 to 510
+ Subtract: -255 to 255
+ Multiply: 0 to 65025
+ Division: 0 to 255 

*Finally all value will convert to [0,255]* 

-----------------------------------------------------------------------------

### SVG Filter

1. feImage

	Take in a bitmap image.

		<defs>
			<filter id="feImage_demo">
				<feImage xlink:href="smile.jpg" />
			</filter>
		</defs>

		<rect y="0" x="0" height="500" width="500"
			style="filter:url(#feImage_demo)" />
		</svg>

2. feMerge

	Combining Stages

		<defs>
			<filter id="layers"
				color-interpolation-filters="sRGB">
				<feImage xlink:href="liquid_image.jpg" result="background"/>
				<feImage xlink:href="small_fish.png" result="layer1" width="500" x="500" y="-180"/>
				<feImage xlink:href="big_fish.png" result="layer2" width="800" x="80" y="30"/>
				<feMerge>
					<feMergeNode in="background"/>
					<feMergeNode in="layer1" />
					<feMergeNode in="layer2"/>
				</feMerge>
			</filter>
		</defs>
		<rect y="0" x="0" height="960" width="1280" style="filter:url(#layers)"/>


3. feBlend

	Combining Stages

	mode = (normal | multiply | screen | darken | lighten)

		<feImage xlink:href="rainbow.png" result="background_image"/>
		<feImage xlink:href="pony.png" result="foreground_image" width="500" x="100"/>
		<feBlend mode="multiply" in="foreground_image" in2="background_image" />

4. feTile

		<defs>
			<filter id="carpet"
				color-interpolation-filters="sRGB">
				<feImage xlink:href="carpet.jpg"
					width="294" height="374"/>
				<feTile />
			</filter>
		</defs>

5. feFlood

	Create a simple rectangle area


		<defs>
			<filter id="chessboard"
				color-interpolation-filters="sRGB">
				<feFlood x="0" y="0" width="100" height="100" flood-color="black" result="stage1"/>
				<feFlood x="0" y="100" width="100" height="100" flood-color="white" result="stage2"/>
				<feFlood x="100" y="100" width="100" height="100" flood-color="black" result="stage3"/>
				<feFlood x="100" y="0" width="100" height="100" flood-color="white" result="stage4"/>
				<feMerge>
					<feMergeNode in="stage1"/>
					<feMergeNode in="stage2"/>
					<feMergeNode in="stage3"/>
					<feMergeNode in="stage4"/>
				</feMerge>
				<feTile />
			</filter>
		</defs>
		<rect x="0" y="0" width="100%" height="100%" filter="url(#chessboard)"/>

6. feComponentTransfer

	Change any of the RGBA values

	+ Identity – the output is the same as the input
	+ Linear – a slope
	+ Discrete – a step function
	+ Table – linear interp. of a set of values
	+ Gamma – an exponential curve

			<defs>
				<filter id="brighter"
					color-interpolation-filters="sRGB">
					<feComponentTransfer>
						<feFuncR type="linear" slope="1.5" intercept="0" />
						<feFuncG type="linear" slope="1.5" intercept="0" />
						<feFuncB type="linear" slope="1.5" intercept="0" />
					</feComponentTransfer>
				</filter>
			</defs>

	Similar Posterization

		<feComponentTransfer>
			<feFuncR type="discrete" tableValues="0 1" />
			<feFuncG type="discrete" tableValues="0 1" />
			<feFuncB type="discrete" tableValues="0 1" />
		</feComponentTransfer>

	+ *In the posterization method we looked at before the result was lower than the original*
	+ *With the feComponentTransfer ‘discrete’ option the result is rounded up/down*

7. SourceGraphic

	Sometimes you need to take the target SVG as an input into the filter. To do that, you simply use ‘SourceGraphic’ as an input.

	<feGaussianBlur in="SourceGraphic" stdDeviation="11" />

8. feGaussianBlur

		<filter id="both_axes_blur"
			color-interpolation-filters="sRGB">
			<feGaussianBlur in="SourceGraphic" stdDeviation="1.5 1.5" />
		</filter>

	stdDeviation="10 0.0001" 表示矩阵

		+--+--+--+--+---+--+--+--+--+--+--+
		|0 |0 |0 |0 | 0 |0 |0 |0 |0 |0 |0 |
		+--+--+--+--+---+--+--+--+--+--+--+
		|..|44|60|78|94|100|94|78|60|44|..|
		+--+--+--+--+---+--+--+--+--+--+--+
		|0 |0 |0 |0 | 0 |0 |0 |0 |0 |0 |0 |
		+--+--+--+--+---+--+--+--+--+--+--+

9. Tspan

		<text x="10" y="100" font-size="60pt">
			<tspan>Dave</tspan>
			<tspan x="10" dy="80">Gibson</tspan>
			<tspan x="10" dy="80">Oz</tspan>
		</text>

10. feOffset

		<feOffset in="SourceGraphic" dx="-10" dy="0" result="left"/>
		<feOffset in="SourceGraphic" dx="10" dy="0" result="right"/>
		<feOffset in="SourceGraphic" dx="0" dy="-10" result="up"/>
		<feOffset in="SourceGraphic" dx="0" dy="10" result="down"/>

11. SVG Transform

		<text y="200" x="100" font-size="145" font-family="verdana"
			style="fill:url(#vertical_gradient); filter:url(#reflection_blur)"
			transform="translate(0,405) scale(1,-1)" >
				Reflection
		</text> 

12. feMorphology

	+ dilation
	+ erosion

		<feMorphology operator="erode" in="SourceGraphic" radius="6" />
		<feMorphology operator="dilate" in="SourceGraphic" radius="4" />

13. feColorMatrix

		<defs>
			<filter id="grayscale" color-interpolation-filters="sRGB">
				<feColorMatrix type="matrix" in="SourceGraphic"
				values=".33 .33 .33 0	0
						.33 .33 .33 0	0
						.33 .33 .33 0	0
						0	0	0	1	0" />
			</filter>
		</defs>

	+ hueRotate
		
		<filter id="add_90" color-interpolation-filters="sRGB">
			<feColorMatrix type="hueRotate" in="SourceGraphic" values="90" />
		</filter>

	+ saturate

		<filter id="increase_s" color-interpolation-filters="sRGB">
			<feColorMatrix type="saturate" in="SourceGraphic" values="1.4"/>
		</filter>


14. feConvolveMatrix

		<feConvolveMatrix in="SourceGraphic"
			kernelMatrix="
				0 0 0 0 0
				0 0 0 0 0
				1 2 4 2 1
				0 0 0 0 0
				0 0 0 0 0 "
			order="5 5"
			divisor="10"
			bias="0"
			preserveAlpha="true"
		/>
	
	+ edgeMode="duplicate"/"wrap" 

15. feTurbulence

	Generating Noise

		<feTurbulence
			type="fractalNoise"
			baseFrequency="0.005"
			numOctaves="4"
			seed="0"
			result="noise" />

	Other 

		<feTurbulence
			type="turbulence" … />

16. feDisplacement

	Moving pixels

		<feDisplacementMap
			in="SourceGraphic"
			in2="displacement_information"
			yChannelSelector="R"
			scale="15" >
		</feDisplacementMap>
	
17. Animated Waves

		<rect width="800"
			height="400"
			fill="url(#gradient1)"
			id="gradient_area" />
	
	Change to:

		<rect width="800"
			height="400"
			fill="url(#gradient1)"
			id="gradient_area"
			x="0" >
			<animate
				attributeName="x"
				begin="0s" dur="3s"
				values="-200; 0"
				repeatCount= "indefinite" />


