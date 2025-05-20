# Thesis
**Image-based Microgreen Height-Yield Estimation**

As part of my degree, I was required to complete a year-long research project.

I took the initiative in finding an industry-based project through 'InMarket Systems', commissioned by 'Natural Yield', and ultimately got a perfect grade.

This project proved and developed my stakeholder management skills, as I had four key stakeholders with different expertise and goals for the project to manage.

| stakeholder | Role | Expertise | Goals |
|----------|----------|----------|----------|
| Dr. Mashuda Glencross | Academic thesis advisor  	| Computer graphics 	| Assess student ability |
| InMarket Systems CEO  | Industry thesis advisor	| Engineering 			| Gain technology for business use |
| Natural Yield CEO  	| Industry client 			| Agriculture and IT  	| Gain technology for agriculture use |
| Me  					| Thesis student 			| Engineering student	| Achieve thesis goals and get a good grade |

## Summary of Thesis

The use of computer vision based yield estimation in the agricultural industry is extensive. 
However, the problem that we were trying to solve is that the technology is not accessible or practical for small-scale enterprises.
Microgreens are an early stage of plant growth, where all or most of the nutrients have come from the seed, and there are up to two leaves. They are considered to be extremely healthy.
Small commercial microgreen growers need to grow up to an additional 30% of their crops to protect against variability in growth.
Further, process optimisation is not financially practical for small businesses, because data collection is extremely costly due to time and complexity of controlling variables and taking measurements of plants.
This project aims towards enabling the use of consumer-grade equipment to cheaply and automatically collect microgreen health and growth metrics, and to ultimately enable supervisory and feedback control for small-scale farming operations.

The project consisted of research into the current state of the art, and multiple rounds of experiments of my design to establish base-line data and test capabilities.
This required testing multiple processes and fiducial markers to find the most effective methods, controlling for many variables, and a huge amount of manual data collection.
I then wrote software using Pythons OpenCV to correct for visual and lens effects using fiducial markers to get accurate measurements using images from mobile phone cameras.
These measurements were then combined with the collected data to estimate the current yield, and, with the growth pattern modelled, estimate future yield.

Throughout this process, I furthered our knowledge of the requirements of our goals, and during my presentation, my system was able to correctly estimate the yield to within 15 grams.
With my methods, one of the main drawbacks was reliability, and I made suggestions for how the system reliability could be improved to be viable in practice.

## Image data

Here is a small selection of the (roughly 14GB of) image data collected.
<div style="display: flex; gap: 10px; justify-content: center;">
	<img src="projects/thesis/_media/top.jpg" alt="top" height="150">
	<img src="projects/thesis/_media/front.jpg" alt="front"  height="150">
	<img src="projects/thesis/_media/side.jpg" alt="side" height="150">
</div>
<div style="display: flex; gap: 10px; justify-content: center;">
	<img src="projects/thesis/_media/seeds.jpg" alt="seeds" height="150">
	<img src="projects/thesis/_media/scan.png" alt="scan"  height="150">
</div>

## Full document
If you would like to view the full thesis document, it is available below:

<iframe src="projects\thesis\_media\thesis_microgreen_yield.pdf" width="100%" height="600px">
	Your browser does not support iframes.
</iframe>