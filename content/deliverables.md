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

### References

[Seed of Andromeda Blog Post (Archive)](https://web.archive.org/web/20150910041136/https://www.seedofandromeda.com/blogs/51-procedural-star-rendering)
### Preliminary results
Here is a video of us detailing our progress so far:
<iframe src="https://drive.google.com/file/d/1qG3P6DkMyDPRBvkbKj1j6rZBin0__Sis/preview" width="640" height="480" allow="autoplay"></iframe>

Here are our project milestone slides (berkeley dot edu account required):
[link](https://docs.google.com/presentation/d/1D1iDlvEi04ZeOGGfWA8Nlf4K3nUhdPnpQOIle3osOLY/edit?usp=sharing)

