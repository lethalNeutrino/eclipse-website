+++
title = "Final Deliverables"
template = "page.html"
date = 2024-04-30T12:00:00Z
[extra]
summary = "Total Eclipse of the Sun"
mathjax = "tex-mml"
+++

# Eclipse Rendering
## Aditya Baireddy, Andy Chen, Andrew Liu, Eddie Park

### Abstract



### Technical Approach 

The key component of our approach was Perlin noise. We mostly used HLSL shaders in our code to render both the star and the corona. To add more control over the noise, we implemented fractal noise, where we sampled several octaves of noise with varying frequencies to give different appearances of the noise.

The basic algorithm we used is as given in the snippet below:

```
float fractal_noise(float4 position, int octaves, float frequency, float persistence) {
	// Sum of all octaves
	float total = 0.0;
	
	// Maximum possible value, used for normalization
	float maxAmplitude = 0.0;
	
	// Current noise amplitude
	float amplitude = 1.0;

	// Sample Perlin noise OCTAVES number of times
	for(int i = 0; i < octaves; i++) {
		total += snoise(position * frequency) * amplitude;
		
		// Double the frequency
		frequency *= 2;
		maxAmplitude += amplitude;
		
		// Reduce the amplitude
		amplitude *= persistence;
	}

	return total / maxAmplitude;
}
```

To smoothly vary our noise textures across not just the surface of the star, but also through time, we used a 4-dimensional position vector, where the 4th coordinate denoted time. Below is a comparison of the noise shader applied to a plane with varying parameters:

<style>
img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 90%;
}
</style>

<table>
	<tr>
		<th style="width:10%"> </th>
		<th> 1 Octave </th>
		<th> 2 Octaves </th>
		<th> 4 Octaves </th>
		<th> 20 Octaves </th>
	</tr>
	<tr>
		<td> <p style="text-align:center"> Frequency = 0.1 <br> Persistence = 0.7 </p> </td>
		<td> <img src="./fractal_noise_1o_0.1f_0.7p.png"> </td>
		<td> <img src="./fractal_noise_2o_0.1f_0.7p.png"> </td>
		<td> <img src="./fractal_noise_4o_0.1f_0.7p.png"> </td>
		<td> <img src="./fractal_noise_20o_0.1f_0.7p.png"> </td>
	</tr>
	<tr>
		<td> <p style="text-align:center"> Frequency = 0.5 <br> Persistence = 0.7 </p> </td>
		<td> <img src="./fractal_noise_1o_0.5f_0.7p.png"> </td>
		<td> <img src="./fractal_noise_2o_0.5f_0.7p.png"> </td>
		<td> <img src="./fractal_noise_4o_0.5f_0.7p.png"> </td>
		<td> <img src="./fractal_noise_20o_0.5f_0.7p.png"> </td>
	</tr>
	<tr>
		<td> <p style="text-align:center"> Frequency = 0.1 <br> Persistence = 0.1 </p> </td>
		<td> <img src="./fractal_noise_1o_0.1f_0.1p.png"> </td>
		<td> <img src="./fractal_noise_2o_0.1f_0.1p.png"> </td>
		<td> <img src="./fractal_noise_4o_0.1f_0.1p.png"> </td>
		<td> <img src="./fractal_noise_20o_0.1f_0.1p.png"> </td>
	</tr>
	<tr>
		<td> <p style="text-align:center"> Frequency = 0.1 <br> Persistence = 0.9 </p> </td>
		<td> <img src="./fractal_noise_1o_0.1f_0.9p.png"> </td>
		<td> <img src="./fractal_noise_2o_0.1f_0.9p.png"> </td>
		<td> <img src="./fractal_noise_4o_0.1f_0.9p.png"> </td>
		<td> <img src="./fractal_noise_20o_0.1f_0.9p.png"> </td>
	</tr>
</table>

We then tuned the noise to get our base for the star rendering:



We also used Perlin noise for the star's corona. The main approach was to sample the noise function based on time and the unit vector pointing from the sun to the pixel we wanted to color, and use the distance from the star to judge how bright the corona should be. (It's necessary to sample on time to make the noise actually change.) However, doing this alone doesn't create a convincing corona; it's too circular, it has lines pointing directly away from the star which looks extremely stiff and unnatural, and it flickers in and out of brightness with no rhyme or reason. In order to fix this, there were two main tricks we used to create something more similar to a real star's corona:
- We sampled from the noise function (three times, once each for x,y,z) based on the position and time to introduce a jitter to the position of the vertex. This jittered position was then normalized and used to sample again (again with time) to end up with a position jittered roughly with respect to its angle emanating outward from the sun. Finally, we used this position to calculate distance from the sun, and scaled brightness inversely to this distance to make parts of the corona closer to the sun brighter. This handles two of our problems: one, it makes the corona less circular, and two, it gets rid of the straight lines and replaces them with more smooth curves and spikes reminiscent of a real corona.
- When sampling from the noise function, instead of sampling directly based on time, we sampled based on the time minus the distance from the pixel to the star (scaled by a constant). This creates the appearance of the particles of the corona moving outward from the star (as they do in reality), as the last noise sample is only based on the angle (roughly) and time minus distance. 



#### Challenges
Though two of our group members had previous Unity experience, it was the other two members' first time using Unity, which was a challenging experience. In particular, writing scripts and shaders was particularly challenging, not only because they were in relatively unfamiliar languages (C# and HLSL), but also because we had to interface with Unity built-ins, of which the documentation was not very helpful at explaining how to use it. Searching for resources online often ended up with looking at long, mostly unhelpful forum threads. Many tasks that were presumed easy ended up being far more time consuming than initially planned.

1. 

One of these challenges was

Another one of these challenges was implementing the corona shader as a billboard. 

2. Moon displacement map


1. Coming up with the noise functions
2. Billboard
3. We struggled with 
4. HDR

#### Learnings
We had a fair amount of learning moments while completing this project.
1. It was far easier for us to do partner programming on one computer than to try to work separately on our own computers. We initially tried to work separately, but found that we encountered lots of unintelligible merge conflicts, and ended up having to do a bunch of work again transferring the results from one computer to another.
2. Choosing the right parameters (frequency, strength, radius, temperature, size, thickness, etc.) is really important for rendering images that look good. Choosing the wrong values for these parameters can lead to results that look really weird.
3. While we ended up sticking with Unity for this project, we were ultimately unhappy with the framework and felt that it would've been easier to operate in C++ by perhaps modifying one of the homework skeleton codes.


### Results



### References

1. Procedural Star Rendering. Benjamin Arnold, Seed of Andromeda. https://web.archive.org/web/20150910041136/https://www.seedofandromeda.com/blogs/51-procedural-star-rendering.
2. 2D / 3D / 4D optimised Perlin Noise Cg/HLSL library (cginc). https://forum.unity.com/threads/2d-3d-4d-optimised-perlin-noise-cg-hlsl-library-cginc.218372/.
3. What color is a blackbody? Mitchell Charity. http://www.vendian.org/mncharity/dir3/blackbody/.
4. CGI Moon Kit. Ernie Wright, Nasa's Scientific Visualization Studio. https://svs.gsfc.nasa.gov/cgi-bin/details.cgi?aid=4720.
5. Billboards. OpenGL-Tutorial. https://www.opengl-tutorial.org/intermediate-tutorials/billboards-particles/billboards/


### Contributions from each team member

