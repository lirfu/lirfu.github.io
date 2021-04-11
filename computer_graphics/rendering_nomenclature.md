# **Rendering cheatsheet**
The what-is-what document for various elements in rendering. Useful to stop mixing names of different, but similarly called methods. Also good for young researchers to gain some nomenclature and an overview of the field.



## Radiometry units (light representation)
-----

### Radiant flux (power) [*W*]
* Amount of energy in a second (like power).
* Basically an property of the light source.
* Ambiguous: lot of energy through a small surface or little energy through a large surface.
![formula](https://render.githubusercontent.com/render/math?math=e^{i \pi} = -1)

### Irradiance [*W/m^2*]
* Received flux by unit area.
* When emitting energy, is called *radiosity*.
* Basically, the average amount of energy that a unit area would get.
* Ambiguous: lots of energy from high angle (from normal) or little energy from small angle.

### Radiance [*W/(sr×m^2)*]
* Flux by unit area by unit steradian.
* Basically limits the irradiance to a subset of light rays that can arrive.



## Rendering methods (scene traversal)
-----

### Forward renderer
* For real-time rendering
* Does everything on one pass
* Expensive when lots of occluded triangles (calculating lighting for every tris)
1. Prune objects outside of view frustum
2. Draw to screen the remaining objects, one by one from furthest to nearest (use z-buffer to ensure the foremost surface is drawn)
3. Sort transparent objects and draw them in a second pass over the current pixels.

### Deferred renderer
* For real-time rendering, separates geometry lighting stage
* Uses forward rendering for transparency
* Faster than forward renderer when scene has lots of occlusions (uses Z-buffer, no need to calculate illumination occluded) and multiple lights (multiple light contributions precalculated and used once for lighting)
* Uses a lot of memory (G-buffer)
* Cannot use hardware anti-aliasing due to separation of geometry and lighting stage
1. Collect scene data into separate buffers
2. Do the expensive lightning calculations with collected data (cost is only pixel bound)

### Ray tracing
* On hit produces 3 types of rays: reflection, refraction and shadow
* No repeated sampling of hit-points (1spp)
* Uses Whitted lighting model with the reflection+refraction recursion

### Path tracing
* Similar to raytracing, but uses Monte Carlo integration on bounces (integrate hemisphere around hit-point)
* Physically based model and interaction description
* Photo-realistic looking images

### Bidirectional path tracing
1. Trace some paths from camera
2. Trace some paths from light sources
3. Connect the them up

### Radiosity
* Scene is divided into patches, each patch emits light to other patches
* Viewpoint independent
* [TODO](https://en.wikipedia.org/wiki/Computer_graphics_lighting)

### Photon mapping
* [TODO](https://en.wikipedia.org/wiki/Computer_graphics_lighting)

### *Next event estimation*
* Addition to ray based renderers
* At each bounce point connect to light sources and sum up contributions
* If bounced ray hits source, ignore its contribution (if was already added at each previous bounce)
* Solves the problem of hitting point light sources (random hit probability would be 0)



## Shading (interpolation)
-----

### Flat shading
* Ok, only for very blocky objects or simplistic artist effect
* Very cheap
1. Calculate light value ad single point on triangle
2. Use that value for entire triangle

### Gouraud shading
* Ok, only for very high poly meshes
* Objects look "spiky" or "meshy"
* Very cheap
1. Calculate light value at vertices
2. Interpolate values of vertices to fill the triangle

### Phong shading
* For smooth surfaces
* Interpolation expensive, can use SLERP to reduce cost
1. Compute the normal at each vertex (average of touching faces)
2. Interpolate normals of vertices for each point in triangle
3. Calculate illumination from interpolates normals



## BRDF models (Bidirectional reflectance distribution function)
-----

### Lambertian
* Only diffuse, ideal matte

### Phong model
* Diffuse + Specular + Ambient
* Models light attenuation using surface normal and viewer vector
* Looks a bit plasticky

###  Blinn-Phong model
* Very similar to Phong model, but uses halfway vector with surface normal for attenuation
* Halfway vector is "half-way" between viewer and source vectors
* Improved specular realism and cheaper to calculate

### Cook–Torrance model
* Specular-microfacet model
* Accounts for color shifting due to light wavelengths
* Accounts for rough microstructure of surface
* Accounts for metallic surfaces
* Accounts for refraciton

### Ward model
* Specular-microfacet model with an elliptical-Gaussian dist. function that depends on surface tangent

### Oren–Nayar model
* Perfect diffuse microfacets (directed microfacet)

### Ashikhmin-Shirley
* Anisotropic reflectance
