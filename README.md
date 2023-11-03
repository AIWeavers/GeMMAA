# GeMMAA
A Generative Multi Modal Augmented Reality Art app for the ODSC West Multi Modal Hackathon (sponsored and facilitated by Weaviate)

## The main concept

* Artists can generate 3D objects, we are using [Meshy](https://app.meshy.ai/discover) text to 3D generative AI technology, the alpha version API got released at the end of October 2023 (a week before the hackathon).
* Artists can also generate music or audio using [Meta's MusicGen](https://about.fb.com/news/2023/08/audiocraft-generative-ai-for-music-and-audio/) which is part of [AudioCraft](https://ai.meta.com/blog/audiocraft-musicgen-audiogen-encodec-generative-ai-audio/) technology family offering, which became available just lately (2023 August).
* Users can search for artworks in a multi-modal manner (searching for salsa should return both artworks with salsa music or artwork depicting a bowl salsa sauce, searching for metal should both match on metal genre music or art installations made of iron and metal components), powered by Weaviate, more specifically Weaviate's multi-modal ImageBind embedding capabilities.
* Users can examine a choosen artwork in Augmented Reality, powered by [8thwall](https://www.8thwall.com/).
* Users may choose to mint an artwork as an NFT to become part of the related NFT collection, powered by [NFTPort](https://www.nftport.xyz/).

## Team members (in order of joining):

Everyone had many key competencies benefiting the hackathon, I'll list the main role for the particular project

|Name|GitHub|Ref|Hackathon role|
|---|---|---|---|
|Csaba Toth|@MrCsabaToth|https://csaba.page|back-end|
|Avi Rao||https://www.linkedin.com/in/avi-nav-rao-39a567205/|front-end|
|Yvonne Fang|https://yvonnef.net/|@y1vonnef|front-end|
|Andrew Savala|@redswimmer|https://www.linkedin.com/in/redswimmer/|back-end|
|Quinton Mills|@quintonmills|https://www.linkedin.com/in/quinton-mills/|front-end|

# Design decision and implementation details
Due to the extermely tight time constraints of the hackathon we were primarily looking for readily availablse solutions and easily accessible APIs.

## Generative Search
Weaviate provided a multi modal generative search engine for us. Since it's a cutting edge new development it was not available on the managed WCS (Weaviate Cloud Services). The ImageBind component requires 6GB memory (configuring 8GB memory for safety) First Csaba tried to spin up an EC2 instance, but he realized that he only had CPU quota policy increase for SageMaker 

## 3D Generative AI
Text to 3D and image to 3D are extremely emerging and developing areas within generative AI. One of the biggest challenges is [the last mile](https://huggingface.co/blog/ml-for-games-3), when a mesh should be generated. Inherently stable diffusion models could geenrate a point cloud or NeRF, however that's not usable for traditional 3D systems, we'd need a mesh model. This step is - just like many problems such as tessellation - is NP hard. I've tried [Shap-E](https://github.com/openai/shap-e) (available on [HuggingFace](https://huggingface.co/openai/shap-e) and [Replicate](https://replicate.com/cjwbw/shap-e) as well) however seemingly these models generated the mesh from voxels, and my rubbery ducky example resulted in a 13 MB obj file without a texture.
We haven't found [DreamFusion](https://dreamfusion3d.github.io/) accessible on HuggingFace or Replicate. In the end the quality of the geenrated model and the assets are important for a real world usage. After testing many endpoints we stumbled across the [Meshy](https://app.meshy.ai/discover), which was able to generate sub megabyte size glb models which even contain the texture! According to our tests it takes less than 5 seconds to receive a model.
In the future we may give another try to [DreamGaussian/Craft](https://replicate.com/alaradirik/dreamgaussian) which are newer techniques than Shap-E.

## Music Generation
Google introduced [MusicML to the wide public in May](https://blog.google/technology/ai/musiclm-google-ai-test-kitchen/), and [tests via GUI](https://aitestkitchen.withgoogle.com/experiments/music-lm) and [samples](https://google-research.github.io/seanet/musiclm/examples/) showed satsifying results, however there's no API yet. An [unofficial "API" uses web scraping](https://github.com/armintum/MusicLM) to drive the web GUI, that's too much of an ugly solution for a back-end.
After going through some more models and looking for readily available solutions we settled on [Meta's MusicGen](https://about.fb.com/news/2023/08/audiocraft-generative-ai-for-music-and-audio/) as part of their [AudioCraft](https://ai.meta.com/blog/audiocraft-musicgen-audiogen-encodec-generative-ai-audio/) technology family offerings. It is also available on Replicate in [several](https://replicate.com/meta/musicgen) [multiple](https://replicate.com/aussielabs/musicgen/versions/11bbae94fb523ecaab2b87ec074fd5b668f20d2b84030c093ed5d5ba8f6f4df1?prediction=tt3had3babhiyuuqleguifrehq) [variations](https://replicate.com/pphu/musicgen-small). THe official meta model only offers the large and the melody model flavors. According to our tests creating our own replicate and serving it from a beefy box didn't yiled a faster generation. It seems that a 10-20 second long music sample could take about 15-20 seconds to generate. The melody flavor respected a chillout + ambient + no beat request better, whereas the large model still generated beats into it. For speedier generation we may still resort to the small model which could be faster.

## Augmented Reality
Csaba has experience with ARCore and SceneView (see [BWSS ARMap] or [AR Physics Experiments]), however we wanted a multi platform solution. PWA (Progressive Web Apps) could offer seamless experience both from developer (no need to build for the native platform and struggle with store submissions and updates) and from user perspective (no need to search for the app in an app store and downloading, installing it). Our concept doesn't need any native sensors or features which are not provided by a web browser, and we can possibly even have a better integration towards the NFT minting feature later (let's just think about web3 which is synergy with crypto currencies and blockchains).
Avi advised [8thwall](https://www.8thwall.com/) as a great platform wich fulfills all of our needs. It's main focus is WebAR, the tech stack is React which is a dominant web front-end technology these days, and all of our front-end team members had this knowledge (along with AR) in their competency skills. As a bonus 8thwall applications can also connect to user's wallets and make any crypto or blockchain features easier to implement.

## NFT minting
NFTPort is a platform made for developers to add NFT related features into applications. They are centered around API, and shield a lot of crypto technical details which are hard to tackle otherwise.
