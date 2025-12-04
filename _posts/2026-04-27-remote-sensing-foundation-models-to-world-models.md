---
layout: distill
title: "Exploring Visual Inversion Problems: From World Models to Remote Sensing Image-to-Image Translation"
description: "This blog post explores the underexplored problem of visual inversion in the context of world models, arguing that remote sensing data provides optimal native image pairs for image-to-image translation tasks, and investigates whether current vision foundation models can solve these visual inversion problems through in-context learning."
date: 2026-04-27
future: true
htmlwidgets: true


# Anonymize when submitting
authors:
  - name: Anonymous

# must be the exact same name as your blogpost
bibliography: 2026-04-27-remote-sensing-foundation-models-to-world-models.bib

toc:
  - name: "World Models as Maps: The Foundation of Inversion"
  - name: "Inversion Problems: From Language to Vision"
  - name: "Image-to-Image Translation: A Visual Inversion Problem"
  - name: "The Limitations of Man-Made Feature Maps"
  - name: "Remote Sensing: Native Image Pairs for Optimal Translation"
  - name: "Exploring Visual Inversion with Foundation Models"
---


## World Models as Maps: The Foundation of Inversion

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> World models can be understood as maps or functions in a state space. Given a mapping from input X to output Y, a true world model should be able to perform both forward prediction (X → Y) and inverse inference (Y → X) for any data pair that satisfies the mapping correlation.
</div>

Recent work, particularly a comprehensive survey <d-cite key="dingUnderstandingWorldPredicting2025"></d-cite> and related research <d-cite key="vafaWhatHasFoundation2025,vafaEvaluatingWorldModel2024,zhangWhenDoNeural2025"></d-cite>, provides a compelling definition of world models that frames them as **maps of functions** in a state space. In this framework, we have a state space where we map input X to output Y. The key insight is that for any given data pair that satisfies this mapping correlation, we should always be able to derive the result Y given the input X, or conversely, perform the inversion to derive the input X given the output Y.

This bidirectional capability—the ability to both predict forward and invert backward—is fundamental to what constitutes a true world model <d-cite key="kong3D4DWorldModeling2025"></d-cite>. It's not merely about learning a one-way transformation, but about understanding the underlying mapping structure well enough to traverse it in both directions.

## Inversion Problems: From Language to Vision

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Large language models have demonstrated remarkable capability in solving inversion problems in the semantic domain. However, this type of inversion problem remains underexplored in the vision field, presenting a significant research opportunity.
</div>

A classic example of inversion in the language domain illustrates this concept beautifully. If we know that "A is B's father," we can derive through inversion that "B is A's son." This demonstrates how language models can understand and manipulate semantic relationships bidirectionally <d-cite key="liHowDoLanguage2025,wangCanLanguageModels2024"></d-cite>.

The existing literature has extensively explored large language models' capability to solve this kind of world model problem in the semantic domain <d-cite key="mancoridisPotemkinUnderstandingLarge2025"></d-cite>. However, **this type of inversion problem is still underexplored in the vision field**. While language models excel at semantic inversion, the visual domain presents unique challenges and opportunities that have not been fully addressed.

## Image-to-Image Translation: A Visual Inversion Problem

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Image-to-image translation represents a form of visual inversion problem. Tasks like translating from sketch images to real images (and vice versa) or from age maps to real images (and vice versa) have been well-studied for years, but they represent a vision-only problem that does not involve semantic information.
</div>

When we move from the language field to the vision field, one of the most similar cases is **image-to-image translation**. This encompasses tasks such as:
- Sketch image ↔ Real image
- Age map ↔ Real image  
- Segmentation map ↔ Real image

This image-to-image translation paradigm has been well-studied for years <d-cite key="isolaImageToImageTranslation2017,zhuUnpairedImageToImage2017,zhuUnpairedImagetoimageTranslation2020"></d-cite> and represents a kind of inversion problem. Recent advances have explored bidirectional translation using diffusion models <d-cite key="liBBDMImagetoimageTranslation2023,xueBiBBDMBidirectionalImage2025,zhouDenoisingDiffusionBridge2024,chungDirectDiffusionBridge2023,liuI2SBImagetoImageSchrodinger2023"></d-cite> and other generative approaches <d-cite key="leeMaskGANTowardsDiverse2020"></d-cite>. Notably, this is a **vision-only problem** that does not involve any semantic information—it's purely about learning the visual transformation between different representations of the same underlying scene or object.

## The Limitations of Man-Made Feature Maps

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Existing image-to-image translation datasets generally use man-made intermediate representations (sketches, segmentation maps, age maps) that are algorithmically defined. These features are implicitly derived from the original image, making it hard to evaluate accuracy and control the quality of extracted features.
</div>

However, one fundamental fallback of this problem definition is that **existing image-to-image translation datasets are generally man-made**. In these datasets, we typically have a real-world image either as the source image or the target image, and the corresponding translated image is something like:
- Age maps
- Segmentation maps
- Sketch maps

These are generally **human-made or defined by some sort of algorithms**. The problem is that these features are actually implicitly derived from the original image, and the details, accuracy, and degree of these extracted features can vary significantly. It's hard to evaluate and control the quality of these intermediate representations.

Moreover, nowadays strong general models can achieve impressive image-to-image translation results, but **existing metrics are field-specific metrics** that capture the difference or visual appearance of the results. It's hard to distinguish between different models even though they have different metric performance—they may look a bit different or in different styles, but it's hard to say that one is definitively better than another in appearance.

**The underlying problem is that these man-made feature maps as the source or target results are not optimal** because we do not introduce new data. The intermediate representations are essentially lossy compressions of the original image, and we're learning to translate between these compressed representations rather than between truly complementary views of the same scene.

## Remote Sensing: Native Image Pairs for Optimal Translation

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Remote sensing data provides native image pairs from different sensor modalities (optical, SAR, multispectral, hyperspectral) that capture the same geolocation at the same time. These complementary modalities represent optimal image-to-image translation pairs that reflect real-world applications.
</div>

A novel solution to this problem lies in the **remote sensing domain**. For Earth observation, we have different sorts of sensors that capture different kinds of modalities of satellite images. Common sources include:
- **Landsat 7/8** (optical imagery)
- **Sentinel-1/2** (SAR and multispectral)
- High-resolution commercial satellite imagery

These satellite images have a great number of different spectra—far more than the traditional RGB channels in general computer vision domains. For a certain geolocation at a certain time, the captured images could have multiple instances:
- **RGB optical images**
- **SAR (Synthetic Aperture Radar) images**
- **Multispectral images**
- **Hyperspectral images**

This natively gives different kinds of attributes that reflect the same object. **This is what makes optimal image-to-image translation pairs**—they reflect real-world applications where different modalities are complementary to each other.

This is quite a hard task because different channels, different bands, and different spectra represent different information from the same object. Intuitively, there is no easy way we can yield the optimal translation results from one modality to another, which makes it an ideal testbed for visual inversion problems.

**Remote sensing data compared to general computer vision data is much more suitable for the image-to-image translation task**, and the existing literature already has a great number of resources. Notable datasets include:
- **OpenEarthMap-SAR** <d-cite key="xiaOpenEarthMapSARBenchmark2025"></d-cite>
- **SARLANG-1M** <d-cite key="weiSARLANG1MBenchmark2025"></d-cite>
- **EarthView** <d-cite key="velazquezEarthViewLargeScale2025"></d-cite>
- **TerraMesh** <d-cite key="blumenstielTerraMeshPlanetaryMosaic2025"></d-cite>
- **SAR-TEXT** <d-cite key="heSARTEXTLargeScale2025"></d-cite>
- **MMEarth** <d-cite key="nedungadiMMEarthExploringMultimodal2024"></d-cite>

These papers provide geolocation-aligned remote sensing data across different modalities. The data sources come from Sentinel-1, Sentinel-2, high-resolution images from commercial satellites, and other feature maps like NDVI (Normalized Difference Vegetation Index), DEM (Digital Elevation Model), depth maps, and so on. Recent work has also explored remote sensing-oriented world models <d-cite key="luRemoteSensingOriented2025"></d-cite> and foundation models for Earth observation <d-cite key="wangTowardsUnifiedCopernicus2025,tsengGalileoLearningGlobal2025,szwarcmanPrithviEO20Versatile2025,danishTerraFMScalableFoundation2025"></d-cite>.

## Exploring Visual Inversion with Foundation Models

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> We formulate the visual inversion problem as a zero-shot or one-shot in-context learning task, where vision foundation models are given example pairs and asked to perform the inverse transformation without explicit specification of the transformation rules, allowing the model to explore the visual transformation through visual cues alone.
</div>

Given the success we've seen in LLMs that are able to solve inversion problems and have pathways to world models, but the underexplored nature of this problem in the vision field, we develop such an inversion problem using our remote sensing image-to-image translation dataset.

**What we are going to explore is a task setup** where:
- **Input**: We have an image-to-image translation dataset
- **Source data**: Optimal optical images (like RGB images)
- **Target data**: SAR images

We use existing vision foundation models, especially the **unified understanding and generation multimodal models** like:
- **OmniGen** <d-cite key="xiaoOmniGenUnifiedImage2025"></d-cite> for unified image generation
- **UniReal** <d-cite key="chenUniRealUniversalImage2025"></d-cite> for universal image generation and editing via learning real-world dynamics
- **ChronoEdit** <d-cite key="wuChronoEditTowardsTemporal2025"></d-cite> for temporal reasoning in image editing and world simulation
- Other image editing models that leverage video generation for image manipulation <d-cite key="rotsteinPathwaysImageManifold2025"></d-cite> and models that are able to accept reference images as input to do in-context generation

We also explore models based on **joint-embedding predictive architectures** <d-cite key="assranSelfsupervisedLearningImages2023,chenDenoisingJointEmbedding2025,moConnectingJointEmbedding2024"></d-cite> and video prediction models <d-cite key="assranVJEPA2SelfSupervised2025,bardesRevisitingFeaturePrediction2024"></d-cite> that have shown promise in understanding visual transformations. Additionally, we consider unified multimodal pretraining approaches <d-cite key="dengEmergingPropertiesUnified2025,radfordLearningTransferableVisual2021"></d-cite> that bridge understanding and generation capabilities.

**What we are going to do** is formulate this inversion problem in a **zero-shot or one-shot in-context learning style**. For example:
- We have source image (optical image A) and optical image B
- We have corresponding target images (SAR image A and SAR image B)
- We put optical image A and SAR image A along with SAR image B as the reference images for the model
- We give a prompt like: "We denote the first image (optical image A) as the input image and the result image is SAR image A. Now we give you the result image (SAR image B), please generate the input image."

We formulate this inversion problem **in-context**, and it is noted that in the prompt, we do not explicitly specify how the source and target images are related or what the transformation means. **We allow the model to explore the transformation between the given visual inputs through the visual cues themselves**.

What we are going to explore is whether current vision foundation or multimodal foundation models have the capability to do this kind of **visual inverse problem**—can they understand the mapping between optical and SAR imagery well enough to perform the inverse transformation when given only visual examples, without explicit semantic or textual guidance?

This represents a fundamental test of whether these models have developed true world model capabilities in the visual domain <d-cite key="kangHowFarVideo2025,chiEmpoweringWorldModels2025,wuVideoWorldModels2025,chenLearningWorldModels2025"></d-cite>, or whether they are still limited to pattern matching and forward prediction without the ability to reason backward through learned transformations. Recent work on video world models <d-cite key="liuWorldWeaverGeneratingLongHorizon2025,gillmanForcePromptingVideo2025,caoDimensionReductionAttack2025"></d-cite> and 3D/4D world modeling <d-cite key="guiImageWorldGenerating2025,zhouLearning3DPersistent2025,trevithickSimVSSimulatingWorld2025"></d-cite> suggests that models are beginning to capture more sophisticated understanding of visual transformations, but the specific problem of visual inversion in the context of remote sensing modalities remains largely unexplored.
