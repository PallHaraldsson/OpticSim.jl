# Emitters (NEW)

Emitters create rays in a certain pattern, usually controlled by some parameters. Emitters are defined by Pixels and Spatial Layouts, and have a spectrum and an optical power distribution over the hemisphere. These are intrinsic physical properties of the emitter.

The **basic emitter** ([`Source`](@ref sources)) is constructed as a combination of 4 basic elements and a 3D [`Transform`](@ref). The basic elements include:
- [`Spectrum`](@ref spectrum)
- [`Angular Power Distribution`](@ref angular_power_distribution)
- [`Rays Origins Distribution`](@ref rays_origins_distribution)
- [`Rays Directions Distribution`](@ref rays_directions_distribution)

The [`OpticSim`](index.html) package comes with various implementations of each of these basic elements:
- Spectrum - the **generate** interface returns a tuple (power, wavelength)
  * [`Emitters.Spectrum.Uniform`](@ref) - A flat spectrum bounded (default: from 450nm to 680nm). the range is sampled uniformly.
  * [`Emitters.Spectrum.DeltaFunction`](@ref) - Constant wave length.
  * [`Emitters.Spectrum.Measured`](@ref) - measured spectrum to compute emitter power and wavelength (created by reading CSV files – more details will follow).
- Angular Power Distribution - the interface **apply** returns an OpticalRay with modified power
  * [`Emitters.AngularPower.Lambertian`](@ref)
  * [`Emitters.AngularPower.Cosine`](@ref)
  * [`Emitters.AngularPower.Gaussian`](@ref)
- Rays Origins Distribution - the interface **length** returns the number of samples, and **generate** returns the n'th sample.
  * [`Emitters.Origins.Point`](@ref) - a single point
  * [`Emitters.Origins.RectUniform`](@ref) - a uniformly sampled rectangle with user defined number of samples
  * [`Emitters.Origins.RectGrid`](@ref) - a rectangle sampled in a grid fashion
  * [`Emitters.Origins.Hexapolar`](@ref) - a circle (or an ellipse) sampled in an hexapolar fasion (rings)
- Rays Directions Distribution - the interface **length** returns the number of samples, and **generate** returns the n'th sample.
  * [`Emitters.Directions.Constant`](@ref)
  * [`Emitters.Directions.RectGrid`](@ref)
  * [`Emitters.Directions.UniformCone`](@ref)
  * [`Emitters.Directions.HexapolarCone`](@ref)

## [Examples of Basic Emitters](@id basic_emitters)

**Note**: All the examples on this page assumes the followin statement was executed:

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters
```

### Point origin with various Direction distributions
```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(origins=Origins.Point(), directions=Directions.RectGrid(π/4, π/4, 15, 15))
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_rect_grid.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_rect_grid.png">
    <img width="250" src="assets/emitters_example_rect_grid.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>RectGrid</b> Direction Distribution
</td>
</tr></table>
```

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(origins=Origins.Point(), directions=Directions.UniformCone(π/6, 1000))
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_uniform_cone.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_uniform_cone.png">
    <img width="250" src="assets/emitters_example_uniform_cone.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>UniformCone</b> Direction Distribution with 1000 sampled directions
</td>
</tr></table>
```

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(origins=Origins.Point(), directions=Directions.HexapolarCone(π/6, 10))
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_hexapolar_cone.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_hexapolar_cone.png">
    <img width="250" src="assets/emitters_example_hexapolar_cone.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>HexapolarCone</b> Direction Distribution
</td>
</tr></table>
```

### Various origins distributions

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(origins=Origins.RectGrid(1.0, 1.0, 10, 10), directions=Directions.Constant())
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_origin_rectgrid.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_origin_rectgrid.png">
    <img width="250" src="assets/emitters_example_origin_rectgrid.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>RectGrid</b> origin distribution
</td>
</tr></table>
```

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(origins=Origins.Hexapolar(5, 1.0, 2.0), directions=Directions.Constant())
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_origin_hexapolar.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_origin_hexapolar.png">
    <img width="250" src="assets/emitters_example_origin_hexapolar.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>Hexapolar</b> origin distribution
</td>
</tr></table>
```

### Examples of Angular Power Distribution

In these example, the arrow width is proportional to the ray power.

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(
    origins=Origins.Hexapolar(1, 8.0, 8.0),             # Hexapolar Origins
	directions=Directions.RectGrid(π/6, π/6, 15, 15),   # RectGrid Directions
	power=AngularPower.Cosine(10.0)                     # Cosine Angular Power 
)
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_angular1.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_angular1.png">
    <img width="250" src="assets/emitters_example_angular1.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>Cosine</b> angular power distribution
</td>
</tr></table>
```

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide
src = Sources.Source(
	origins=Origins.RectGrid(1.0, 1.0, 3, 3),           # RectGrid Origins  
	directions=Directions.HexapolarCone(π/6, 10),       # HexapolarCone Directions
	power=AngularPower.Gaussian(2.0, 2.0)               # Gaussian Angular Power 
)
Vis.draw(src, debug=true)
Vis.save("assets/emitters_example_angular2.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_angular2.png">
    <img width="250" src="assets/emitters_example_angular2.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
An example of <b>Gaussian</b> angular power distribution
</td>
</tr></table>
```

### Composite Sources - Display Example

```@example
using OpticSim, OpticSim.Geometry, OpticSim.Emitters # hide

# construct the emitter's basic components
S = Spectrum.Uniform()
P = AngularPower.Lambertian()
O = Origins.RectGrid(1.0, 1.0, 3, 3)
D = Directions.HexapolarCone(deg2rad(5.0), 3)	
	
# construct the srouce. in this example a "pixel" source will contain only one source as we are simulating a "b/w" display. 
# for RGB displays we can combine 3 sources to simulate "a pixel".
Tr = Transform(Vec3(0.5, 0.5, 0.0))
source1 = Sources.Source(Tr, S, O, D, P)
	
# create a list of pixels - each one is a composite source
pixels = Vector{Sources.CompositeSource{Float64}}(undef, 0)
for y in 1:5 # image_height
    for x in 1:10 # image_width
        # pixel position relative to the display's origin
        local pixel_position = Vec3((x-1) * 1.1, (y-1) * 1.5, 0.0)
        local Tr = Transform(pixel_position)

        # constructing the "pixel"
        pixel = Sources.CompositeSource(Tr, [source1])

        push!(pixels, pixel)
    end
end
	
Tr = Transform(Vec3(0.0, 0.0, 0.0))
my_display = Sources.CompositeSource(Tr, pixels)

Vis.draw(my_display)                                                # render the display - nothing bu the origins primities
rays = AbstractArray{OpticalRay{Float64, 3}}(collect(my_display))   # collect the rays generated by the display
Vis.draw!(rays)                                                     # render the rays 

Vis.save("assets/emitters_example_composite_display.png") # hide
nothing #hide
```

```@raw html
<table><tr>
<td>
<a target="_blank" href="assets/emitters_example_composite_display.png">
    <img width="500" src="assets/emitters_example_composite_display.png" style="max-width:100%;">
</a>
</td>
<td valign="middle">
A display example using composite sources.
</td>
</tr></table>
```



## [Spectrum](@id spectrum)

```@docs
Emitters.Spectrum.Uniform
Emitters.Spectrum.DeltaFunction
Emitters.Spectrum.Measured
```

## [Angular Power Distribution](@id angular_power_distribution)

```@docs
Emitters.AngularPower.Lambertian
Emitters.AngularPower.Cosine
Emitters.AngularPower.Gaussian
```

## [Rays Origins Distribution](@id rays_origins_distribution)

```@docs
Emitters.Origins.Point
Emitters.Origins.RectUniform
Emitters.Origins.RectGrid
Emitters.Origins.Hexapolar
```

## [Rays Directions Distribution](@id rays_directions_distribution)

```@docs
Emitters.Directions.Constant
Emitters.Directions.RectGrid
Emitters.Directions.UniformCone
Emitters.Directions.HexapolarCone
```

## [Sources](@id sources)

```@docs
Emitters.Sources.Source
Emitters.Sources.CompositeSource
```





