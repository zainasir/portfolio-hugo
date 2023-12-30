---
title: "Cosine-Beta Schedule for Denoising Diffusion Models"
date: 2023-12-29T18:52:04-05:00
description: ""
tags: ["diffusion models", "variance schedules", "cosine-beta schedule", "pixel-binning"]
---
Denoising Diffusion Probabilistic Models (DDPMs) are quickly taking over the world, especially since [Dall-E 2](https://openai.com/dall-e-2) and [Midjounery](https://www.midjourney.com/home?callbackUrl=%2Fexplore) came out. It is quite interesting how powerful they are considering they are based on [Markov Chains](https://en.wikipedia.org/wiki/Markov_chain) which were introduced all the way back in 1906.

Diffusion models are based on a forward process and a backward process. In the forward process, noise is added to the dataset in incremental steps through a Gaussian distribution. In the backward process, a trained neural network is used to predict the mean and variance of the noise which allows us to reverse the added noise.

{{< figure src="assets/forward.png" attr="Overview of the forward diffusion process. Source: [Ho et al., (2020)](https://arxiv.org/abs/2006.11239)" align=center link="https://arxiv.org/abs/2006.11239" target="_blank" >}}

In the original diffusion model that was presented by [Ho et al., (2020)](https://arxiv.org/abs/2006.11239) the authors decided to use only the mean as the learnable parameter and keep the variance fixed through a variance schedule.

## Variance Schedule Overview

### What is a variance schedule?

A variance schedule is simply a function that defines the variance at each given time step during the Markov chain, from $t =0$ to $t = T$, which is the maximum timestep. At each step, Gaussian noise $\epsilon$ is added to the previous image, as defined by the formula below:

$$
x_{t+1}=\sqrt{1-\beta}x_t+\sqrt{\beta_t}\epsilon
$$

It is obvious that the amount of noise added is dependent on variance chosen through the schedule. Thus, a well behaved variance scheduled is important to ensure the quality of the trained model.

A well behaved variance schedule ensures that at timestep $t=T$, we end up with an [isotropic Gaussian distribution](https://math.stackexchange.com/questions/1991961/gaussian-distribution-is-isotropic).

### Well Behaved Variance Schedules

A variance schedule doesn’t have to be complex in order to train a diffusion model. The original authors of DDPM ([Ho et al., (2020)](https://arxiv.org/abs/2006.11239)) used a linear variance schedule with $T=1000$ and $10^{-4} \leq \beta_i \leq0.02$ where $i=[0,T]$. They were able to achieve considerable results with this schedule.

{{< figure src="assets/linear.png" attr="Linear variance schedule. Source: [Nichol and Dhariwal (2021)](https://arxiv.org/abs/2102.09672)" align=center link="https://arxiv.org/abs/2102.09672" target="_blank" >}}

However, according to [Nichol and Dhariwal (2021)](https://arxiv.org/abs/2102.09672), a linear schedule is not well suited for low resolution images. It can be seen in the above image that the samples get too noisy early on in the forward noising process, which leads to a lower sample quality and sub-optimal training as the timesteps increase. This is where a cosine-beta schedule comes in because it avoids such problems, ensuring that the sample quality is consistent throughout, as shown in the image below.

{{< figure src="assets/cosine-beta.png" attr="Cosine-beta variance schedule. Source: [Nichol and Dhariwal (2021)](https://arxiv.org/abs/2102.09672)" align=center link="https://arxiv.org/abs/2102.09672" target="_blank" >}}

## Cosine-Beta Schedule

### Formula

A cosine beta schedule is based on the following formula:

$$
\bar{\alpha}_t=\frac{f(t)}{f(0)} \space where \space f(t)=cos(\frac{t/T+s}{1+s}*\frac{\pi}{2})^2
$$

Variance values can then be computed using $\bar{\alpha_t}$ as follows:

$$
\beta_t=1-\frac{\bar{\alpha_t}}{\bar{\alpha_t} - 1}
$$

The result of the cosine-beta schedule is that it avoids adding too little noise in the beginning and too much noise at the end, while still allowing a considerable increase in the noise in the middle. This ensures that samples at any timestep are equally valuable to the training process.

### Improving Accuracy through Offset

The cosine-beta schedule also uses a neat little trick using a fixed offset to improve noise prediction in the early timesteps. Having tiny amounts of noise at the beginning makes it hard for the model to predict noise accurately. To avoid this, [Nichol and Dhariwal (2021)](https://arxiv.org/abs/2102.09672) added an offset $s=0.008$.

Unsurprisingly, the choice for the offset value is not completely random. It stems from the idea that the offset should be such that the standard deviation, or $\sqrt{\beta_t}$ , stays relatively smaller than the bin size. To understand how this helps model training, we first need to understand the concept of [Pixel Binning](https://en.wikipedia.org/wiki/Pixel_binning).

### Pixel Binning

The general idea is that during image processing, binning is used to combine neighboring pixels into a single pixel. For example, reducing a 144-pixel with a bin size of 2x2 (4 pixels) results in a 36-pixel image.

{{< figure src="assets/binning.png" attr="Binning a 144-pixel image by 2x and 4x. Source: [Tucsen](https://www.tucsen.com/learning/binning-what-is-binning%EF%BC%9F-4/)" align=center link="https://www.tucsen.com/learning/binning-what-is-binning%EF%BC%9F-4/" target="_blank" >}}

In the context of diffusion models, if the standard deviation is much larger than the bin size, it could result in overlapping or excessively large intervals which can lead to information loss. A smaller standard deviation ensures a controlled addition of noise, preventing abrupt changes in the data representation.

The improved denoising diffusion model in [Nichol and Dhariwal (2021)](https://arxiv.org/abs/2102.09672) uses a bin size of $1/127.5$, which gives us the value of $s=0.008$.

## Further Reading

So the question that comes to mind is what if we can learn the variance too, rather than fixing it through a schedule. The answer is yes, we can! This is another improvement which is discussed in [Nichol and Dhariwal (2021)](https://arxiv.org/abs/2102.09672). I highly recommend reading the paper as it does a pretty good job of explaining the cosine-beta schedule and the motivation behind it.
