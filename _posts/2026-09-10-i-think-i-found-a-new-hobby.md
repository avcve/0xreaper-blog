---
title: "I Think I Found a New Hobby: Geolocating a Photo From Scratch"
tags: [osint, recon, writeup]
thumb: recon
thumb_img: /assets/img/thumbs/stalker.gif
---

I've been deep in OSINT lately, and I think I found a new hobby.

Here's the setup. Imagine someone on CT posts a photo standing in front of a supercar. No location tag, no caption giving anything away. Just the photo.

If I worked for the EFCC IRL, this would basically be the job. So I got to work, just to see how far I could actually get with nothing but the image.

All I needed to get started was a quick prompt to Grok, asking for coordinates based on what was visible in the photo, on Google Maps.

I double checked that against Google Images, cross-referencing visible landmarks and background details, and it checked out.

Then I went to work on Google Maps properly. There was an offset between what Grok gave me and the actual pin, so I had to use the forward and backward arrows on Street View to walk the location back manually until it lined up with what was actually in the photo.

![Street View lined up against the original photo]({{ '/assets/img/posts/street-view-walkthrough.jpg' | relative_url }})
*Walking the Street View arrows until the background matched.*

It was genuinely fun. Reconstructing where someone was standing, from a single photo, using nothing but public tools. And it's not just a party trick. This is real tradecraft, the same core method groups like Bellingcat use to verify footage from conflict zones: landmark matching, shadow angles, signage, terrain, cross-referenced against satellite and Street View until one location is the only one that fits.

## Quick question though

In a country like Nigeria, where there isn't a lot of CCTV coverage compared to places like the UK or US, is there any real utility to open source geolocation work? And how can "I", or we as a community, actually contribute to developing OSINT further in Africa and Nigeria specifically?

I don't have a full answer yet. I'll keep exploring it. But my early thinking is that camera density isn't actually the bottleneck people assume it is. Satellite imagery is global and free regardless of local camera infrastructure. Corporate and domain registries, which I've been using for the recon side of my work, are often weaker and less transparent in African markets than in the US or EU, which paradoxically makes OSINT more valuable here, not less, because there's less public-record friction to dig through to find something real. And a lot of the existing geolocation tooling is trained on European and American reference points: road markings, building styles, signage conventions. A proper reference guide built around Nigerian or broader African visual context (building types, road infrastructure, regional vegetation) is something that basically doesn't exist yet and probably should.

Thanks for reading.

On the next one, I'll go deeper into using historical satellite imagery for geolocation, specifically for cases where you don't even have a landmark in the photo to start from, just terrain and shadow. It's a different problem than the Street View walkthrough above, and honestly a harder one.
