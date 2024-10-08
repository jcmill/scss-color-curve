# Scss Color Curve (SCC)

The SCSS Color Curve (SCC) is a color mapping tool that simplifies the process of generating color themes and variations from a base color. Unlike the deprecated SASS <code>lighten</code> and <code>darken</code> functions, SCC offers both linear and non-linear approaches for creating color variations. The non-linear approach generates two asymptotic curves from the base color, one approaching a luminance of 100 (white) and the other approaching a luminance of 0 (black). By emphasizing saturation, SCC produces richer and more dynamic color variations compared to simple lightening and darkening techniques. Which can lead to dull and muddy colors.

## How does Scss Color Curve work?
The SCC has two options when approaching shifts in colors. The first, the default, is a curved approach that tries to maintain saturation in each shift in color. The second option is a linear approach that strictly adds black or white to the color.

One other default behavior to take note of is if the base color is gray (has a saturation of 0%) the shift in colors will only utilize the linear approach. This prevents grays from becoming tinted with unintended colors. As example, because of the default position of #000000, shifting black with a curve will add red to the color. Likewise, #ffffff will shift into pinks. In order to avoid unintended shifting in grays, this default behavior is forced.


### Curved Approach
In it's simplest form the following with adjust a color accoding to the color curve, with the saturation of that alteration being the focus:

``` scss
  color(#d62121, 2); // -> #e63535 (a lighter version of the base color while maintaining proper saturation)
  color(#d62121, -2); // -> #c01a1a (a darker version of the base color while maintaining proper saturation)
```
### Linear Approach
For a linear approach, one that only adds black or white to the color, a condition of false can be added when calling the function.
``` scss
  color(#d62121, 2, false); // -> #e63535 (adds 10% white to the base color)
  color(#d62121, -2, false); // -> #c01a1a (adds 10% black to the base color)
```
Each step in the linear approach is multiplied by 5%. So a step of 3 would change the color by 15%.

## The Structure of the Function
``` scss 
@function color($color, $scale: 0, $linear: false, $gradientScale) {...}
```
**$color** - passes a color or the key of a color 

**$scale** - the amount you wish to adjust the base color. Typically a range of -4 to 4 (which provides 9 options of color options), but the number can shift as much as is needed. The Scaling is also impacted by the <code>gradientScale</code> (explained below)

**$linear** - The default value is set to false which initiates the color curve approach. If the linear approach is needed, passing true in initiates it.

**gradientScale** - This only affects the color curve. The default value is set to 3. When adjusted, this will determine how quickly the color curve falls off to its lightest and darkest values. A value of 1 will create a more subtle shift in color variations. Whereas a 6 might only allow the base color to shift 3 times in one direction before arriving at black or white.

## Mapping Colors: $color-settings
The function pulls from a color map you create named <code>$color-settings</code> which could look something like this:

``` scss
$color-settings: (
  'colors': (
    'a': (
      value: #d62121,
      name: 'red',
      gradientScale: 3
    )
  )
) 
```
In these maps, the actionable keys are the initial name of each key (in this example, <code>a</code>), the <code>value</code> which will be concidered the base color, and the <code>gradientScale</code> which adjusts the intensity of each shift in color, starting from the base. A gradient of 1 will produce a very subtle gradient, while a value of 6 will create harsher shifts in colors. If the gradientScale is omitted, the value will default to 3.

**The simplest passable map would be:**
``` scss
$color-settings: (
  'colors': (
    'a': (
      value: #d62121
    )
  )
) 
```

### Custom color options
If the shifting of a color has to be controlled further, custom color options for each scale can be add like the following code.
``` scss
$color-settings: (
  'colors': (
    'b': (
      value: #e81c0d,
      name: 'custom gradient',
      gradientScale: 2,
      -5: #e2136f, 
      -4: #e3125a, 
      -3: #e51044, 
      -2: #e60f2d, 
      -1: #e70e16, 
      1: #e9310c,
      2: #ea470b,
      3: #eb5e0a,
      4: #ed7508,
      5: #ee8d07
    )
  )
) 
```