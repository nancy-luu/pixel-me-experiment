Why I built this ---

I'm always interested in how ideas translate into code. In this experiment, I explored what it means to compress an image into pixels -
which brought me down a rabbit hole of lossy compression that preserves perceptual identity but that's perhaps for another blog post.

TLDR ---

We are essentially throwing away most of the information in the image on purpose — collapsing it down to a grid of cells. 
Each cell discards color, texture, and detail, keeping only one number: brightness.
The interesting part is that it still reads as you. Your face is recognizable from just ~3k luminance values because the human brain is extremely good at reconstructing identity from minimal data. 
Edges, proportions, light/shadow patterns are enough.

How it works ---

Each frame of your webcam is downsampled into a grid, converted to luminance, then redrawn as monochromatic pixel blocks on a black background.

1. Downsampling to a grid

If your webcam outputs a full 640×480 frame - that's approx 307,200 pixels. So the frame gets shrunk down to a smaller grid. 
Each cell represents a section of the original image, and all the fine detail within that section gets collapsed into a single value.

2. Converting to luminance

Each cell's RGB color gets reduced to a single brightness number between 0 and 1:

lum = 0.2126×R + 0.7152×G + 0.0722×B

This essentially discards hue entirely — the canvas no longer knows if something was red or blue, only how bright it was.

3. Contrast and brightness adjustment

The raw luminance values tend to be flat and washed out. Two adjustments push them apart:

  lum = (lum - 0.5) * contrast + 0.5; // stretches the range
  
  lum  = Math.max(0, Math.min(1, lum * brightness));  // lifts the overall level

Brightness just scales everything up or down uniformly. Together they make the image easier to read as a face on a black background.


Controls:

Pixel Size — how chunky the pixels are (4px = very fine, 30px = very blocky)

Brightness — lift the overall exposure

Contrast — push the darks darker and lights lighter

