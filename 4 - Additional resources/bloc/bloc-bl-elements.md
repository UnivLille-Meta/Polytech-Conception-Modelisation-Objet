# Configuring BlElements

### Geometry of BlElement

In Bloc, the visual form and boundaries of your UI elements are determined by their geometry. 
Each element can only possess a single geometry, essentially acting as a blueprint for its shape and size.
You can visualize an element as a specific geometry encapsulated within an invisible rectangular container, representing its overall *bounds*.

Bloc provides a diverse range of pre-defined geometry shapes accessible through `BlElementGeometry allSubclasses`. 
This comprehensive library empowers you to construct elements of varying complexities, from basic rectangles and circles to more intricate forms.

Bloc excels in facilitating the creation of custom components with advanced layout possibilities. 
Imagine building complex layouts by strategically arranging various elements, each defined by its unique geometry, to form a cohesive whole.

While the Alexandrie canvas provides a foundational set of building drawing primitives, Bloc offers a richer library of pre-defined shapes and the flexibility to construct even more intricate geometries.

![Base geometries.](figures/allGeometry.png)

* **Annulus**: `BlAnnulusSectorGeometry new startAngle: 225; endAngle: 360;   innerRadius: 0.3; outerRadius: 0.9);`
* **Bezier**: `BlBezierCurveGeometry controlPoints: { 5@0. 25@80. 75@30. 95@100 }`
* **Circle**: `BlCircleGeometry new matchExtent: 100 @ 50`
* **Ellipse**: `BlEllipseGeometry new matchExtent: 100 @ 50)`
* **Line**: `BlLineGeometry from: 10@10 to: 90@90`
* **Polygon** : `BlPolygonGeometry vertices: {(10 @ 10). (10 @ 90). (50 @ 50). (90 @ 90). (90 @ 10)}`
* **Polyline**: `BlPolylineGeometry vertices: {(10 @ 10). (10 @ 90). (50 @ 50).(90 @ 90). (90 @ 10) }`
* **Rectangle** : `BlRectangleGeometry  new`
* **Rounded rectangle**: `BlRoundedRectangleGeometry cornerRadius: 20`
* **Square**: `BlSquareGeometry new matchExtent: 70 @ 70`
* **Triangle**: `BlTriangleGeometry new matchExtent: 50 @ 100; beLeft`

### Element border

The geometry is like an invisible line on which your border is painted. The
painting is a subclass of `BlPaint`, and one of the three:

- solid color
- linear gradient color
- radial gradient color

![Border color type.](figures/bordercolortype.png)

Your border opacity can be specified as well: `opacity: 0.5;`

By default, your border will be a full line, but it can also be dashed, with
**dash array** and **dash offset**. Dash arrays define the number of element, and
dash offset, the space between elements.

You also have a pre-defined option, available in a single call:

- **dashed**
- **dashed small**

![Border dash.](figures/multipletriangledash.png)

If the path is not closed, The style extent of your border can be defined with

- **cap square**
- **cap round**
- **cap butt**

Last, when the lines of your border cross each other, you can define the style of
the join as shown in Figure *@fig:jointype@*:

- **round join**
- **bevel join**
- **mitter join**

![Border join type.](figures/borderjointype.png)

You have two options to define your border:

* short call: `element border: (BlBorder paint: Color orange width: 5)`
* with a builder:`element border: (BlBorder builder dashed; paint: Color red; width: 3; build)`

The first one is very helpful for solid line definition. The builder lets use
customize all the details of your border.

### Element bounds and outskirts

Let's look at the different possible bounds of your element.

**Layout bounds** can be defined explicitly using `size:` method or dynamically
Layout bounds are considered by layout algorithms to define mutual locations
for all considered elements. You'll know more about layout later.

**Geometry bounds** area is defined by minimum and maximum values of polygon
vertices. This does not take in account the border width

**Visual bounds** is an exact area occupied by an element. it takes strokes
and rendering into account.

The geometry is like an invisible line on which your border is represented.
The border drawing can happen outside (adding its border size to the size of
your element), centered, or inside the geometry of the element. The final size
(geometry + border width) will define the **bounds** of your element.

In Figure *@fig:outskirts@*, the same exact star is drawn 3 times. The only difference is
the outskirts definition between those 3.

![Outskirts.](figures/multipletriangleoutskirts.png)

If we specify outskirts inside, visual bound and geometry bounds will be the
same. But if the outskirts is outside, then visual bounds are larger than
geometry bounds to take border width into its calculation.

### Element background

quick set-up: `background: (Color red alpha: 0.8);`

using rgb color

```smalltalk
background: (Color r: 63 g: 81 b: 181 range: 255);
```

using linear gradient

```smalltalk
background: ((BlLinearGradientPaint direction: 1 @ 1) from: Color red to: Color blue).
```

using radial gradient

```smalltalk
background: (BlRadialGradientPaint new
stops: { 0 -> Color blue. 1 -> Color red };
center: largeExtent // 2;
radius: largeExtent min;
yourself);
```

Using dedicated `BlPaintBackground` object.

```smalltalk
BlElement new 
	background: ((BlPaintBackground paint: Color red  asBlPaint) opacity: 0.75; yourself);
	openInSpace
```

![Background color.](figures/backgroundcolortype.png)

### Element effect

You can get the list of all the effects available by executing: `BlElementEffect allSubclasses`

#### Simple shadow. 

```origin=BlocExamples>>shadow
BlElement new
	size: 200 @ 100;
	geometry: (BlRoundedRectangleGeometry cornerRadius: 2);
	background: (Color red alpha: 0.2);
	border: (BlBorder paint: Color yellow width: 1);
	outskirts: BlOutskirts centered;
	effect:
	    (BlSimpleShadowEffect color: Color orange offset: -10 @ -20)
```

![Simple shadow.](figures/simpleshadow.png)

Try the following variation.

```
effect: (BlSimpleShadowEffect
	color: (Color orange alpha: shadowAlpha)
	offset: shadowOffset);
```

#### Gaussian shadow.
The following produces the result shown in Figure *@fig:gaussian@*.

```smalltalk
BlElement new
	size: 300 @ 150;
	geometry: (BlRoundedRectangleGeometry cornerRadius: 2);
	background: (Color blue alpha: 0.5);
	border: (BlBorder paint: Color red width: 10);
	effect: (BlGaussianShadowEffect color: Color yellow offset: 10@20 width: 5)
```

![Gaussian shadow.](figures/gaussianshadow.png)

### Element opacity

The element opacity is a value between 0 and 1, 0 meaning completely transparent.
You can apply opacity to a background, a border, or to your whole element.

![Element opacity.](figures/elementwitopacity.png)

### Element transformation

You can apply transformations to a `BlElement`:

- rotation
- translation
- scaling
- reflection
- etc...

Transformation are affine transformation. For more detail, you can search on the internet, there are countless references to it. To simplify it, I'll say we apply  a transformation matrix (*BlMatrix2D*) to all point of our figure path, each point represented as *BlVector*. 

You have 3 type of tranformation available in Bloc:
- **BlElementLocalTransformation**: This transformation combine an affine transformation (*BlAffineTransformation* subclasses), with a point of origin (*BlAffineTransformationOrigin* subclasses). By default, origin is the center of your element, BlAffineTransformationCenterOrigin.
- **BlElementAbsoluteTransformation**: This transformation apply a transformation matrix to your shape, without point of origin. Its  result is similar to *BlElementLocalTransformation*, with origin set to *BlAffineTransformationTopLeftOrigin*
- **BlElementCompositeTransformation** which are combination of *BlElementLocalTransformation* and/or *BlElementAbsoluteTransformation*

Most of the time, you won't have to deal with matrix definition. You'll use the 
helper method `transformDo`, and define your transformation using *BlTransformationBuilder*.

When you're defining a transformation using `transformDo:` , you'll, by default, 
use *BlAffineCompositeTransformation*. All transformation move added subsequently will be composed together.
The origin will be set to *BlAffineTransformationCenterOrigin*.

Those two transformation below are strictly equivalent, and rotate your element by 45 degree. 
One use the underlying object, while the other use the helper methods:

```smalltalk
elt transformation: (BlElementLocalTransformation 
	newWith: ((BlRotationTransformation new angle: 45) 
	origin: (BlAffineTransformationCenterOrigin defaultInstance ) )).
```

```smalltalk
elt transformDo: [ :t | t rotateBy: 45 ].
```

A transformation is applied in the scope of the message `transformDo:` as shown below.
```
element transformDo: [ :b | b scaleBy: 0.2; translateBy: -25 @ -15 ];
```
The following script produces *@fig:transform@*.

```smalltalk
aContainer := BlElement new
		layout: BlFrameLayout new;
		constraintsDo: [ :c |
			c horizontal fitContent.
			c vertical fitContent ];
		padding: (BlInsets all: 20);
		background: (Color gray alpha: 0.2).

node := BlElement new
	geometry: (BlRoundedRectangleGeometry cornerRadius: 4);
	border: (BlBorder paint: Color black width: 2);
	background: Color white;
	constraintsDo: [ :c |
		c frame horizontal alignCenter.
		c frame vertical alignBottom ];
	size: 20 @ 20.

aContainer transformDo: [ :t |
	t
		scaleBy: 2.0;
		rotateBy: 69;
		translateBy: 50 @ 50 ].
		
aContainer addChild: node.
aContainer forceLayout.
```

![Transform example.](figures/transformexample.png)

transform is something extra that is applied on top of position. For example if
you want to have a short of animation, you would use transform as it is not 
taken into account by layouts

#### Transform catches

The message `transformDo:` can be applied at any moment during the life of an object.
You can use any static or pre-computed properties with `transformDo:` as in the following snippet.
Here ` -25 asPoint` does not depend on the child or parent size.

```
| child parent |
child := BlElement new 
	background: Color lightBlue; 
	geometry: BlCircleGeometry new;
	yourself.
 
child position: 100@100.
child  transformDo: [ :t | t translateBy: -25 asPoint ].

parent := BlElement new 
	size: 200 asPoint; 
	addChild: child;
	background: Color lightRed.

parent openInSpace.
```

**Important.**
if you want to use dynamic layout properties (such as `size`) with `transformDo:`, you need to wait for layout phase to be completed using `whenLayoutedDoOnce:`.
Compare the two examples below:

```
| child parent |
child := BlElement new 
	background: Color lightBlue; 
	geometry: BlCircleGeometry new;
	yourself.
 
child position: 100@100.
child transformDo: [ :t | t translateBy: child size negated / 2 ];

parent := BlElement new 
	size: 200 asPoint; 
	addChild: child;
	background: Color lightRed.

parent openInSpace.
```



```
| child parent |
child := BlElement new 
	background: Color lightBlue; 
	geometry: BlCircleGeometry new;
	yourself.
 
child position: 100@100.

parent := BlElement new 
	size: 200 asPoint; 
	addChild: child;
	background: Color lightRed.

parent whenLayoutedDoOnce: [ 
	child  transformDo: [ :t | t translateBy: (child size negated / 2) ]  ].

parent openInSpace.
```` 


### Element custom painting (more here)

Bloc favors `BlElement` composition to create your graphical interface. 
Most of the time, you will not have to create a custom painting of your element widget. 
You can already do a lot with existing geometry.

Ultimately, you can define drawing methods on a canvas, but once drawn, a canvas cannot be easily inspected
for its elements. 
However, Bloc element composition creates a tree of elements, that can be inspected, and shaped dynamically.

Creating and drawing your element
- subclass `BlElement`
- custom drawing is done with `aeFullDrawOn:` method. Note that 'ae' stands for the Alexandrie canvas.

You can see the `aeFullDrawOn:`
```
BlElement >> aeFullDrawOn: aCanvas
	"Main entry point to draw myself and my children on an Alexandrie canvas."

	self aeDrawInSameLayerOn: aCanvas.
	self aeCompositionLayersSortedByElevationDo: [ :each | each paintOn: aCanvas ].
```

Element geometry is taken care by the method `aeDrawGeometryOn: aeCanvas`.
Painting is done on an Alexandrie canvas, then rendered on the host
by the method `BARenderer (BlHostRenderer) >> render: aHostSpace` which displays it on a `AeCairoImageSurface`.

Drawing is done through method 'xxx', which receives an Alexandrie
(vector) canvas as an argument.

1. `aeDrawChildrenOn:`
2. `aeDrawOn:`
3. `aeDrawGeometryOn:`

Drawing example -  draw hour tick around a circle 
```
aeDrawOn: aeCanvas
	"draw clock tick on frame"

	super aeDrawOn: aeCanvas.

	aeCanvas setOutskirtsCentered.

	0 to: 11 do: [ :items |
		| target |
		target := (items * Float pi / 6) cos @ (items * Float pi / 6) sin.

		items % 3 == 0
			ifTrue: [
				aeCanvas pathFactory: [ :cairoContext |
					cairoContext
						moveTo: center;
						relativeMoveTo: target * 115;
						relativeLineTo: target * 35;
						closePath ].

				aeCanvas setBorderBlock: [
					aeCanvas
						setSourceColor: Color black;
						setBorderWidth: 8 ] ]
			ifFalse: [
				aeCanvas pathFactory: [ :cairoContext |
					cairoContext
						moveTo: center;
						relativeMoveTo: target * 125;
						relativeLineTo: target * 25;
						closePath ].

				aeCanvas setBorderBlock: [
					aeCanvas
						setSourceColor: Color black;
						setBorderWidth: 6 ] ].
		aeCanvas drawFigure ]
```

### Conclusion

`BlElement` is defining a large spectrum of element functionalities. 
The following chapters will cover layout, event handling, animations and more. 

## Exercise 2: lights wall

Transform your color grid to a wall of lights:
- compose elements to add circles to the squares
- build and add glowing effects to the circles

Do not hesitate to explore the various effects and their configuration!

![Creating a grid of elements.](figures/elementsGridLights.png)