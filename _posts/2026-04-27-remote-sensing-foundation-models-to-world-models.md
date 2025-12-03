---
layout: distill
title: "From Remote Sensing Foundation Models to World Models: A pathway in the perspective of Generative Modeling"
description: "We reframe remote sensing image-to-image translation as a world model inversion problem, arguing that native multi-sensor data is the best substrate for learning invertible environmental simulators."
date: 2026-04-27
future: true
htmlwidgets: true


# Anonymize when submitting
authors:
  - name: Anonymous

# must be the exact same name as your blogpost
bibliography: 2026-04-27-remote-sensing-foundation-models-to-world-models.bib

toc:
  - name: "Defining the Simulator: World Models"
  - name: "Capturing the State: Remote Sensing Foundation Models"
  - name: "The Synthesis Pathway: Bridging Observation and Simulation"
  - name: "\"What I Cannot Create, I Do Not Understand\""
  - name: "Beyond the Textual Shortcut: Why World Models Need More Than Language"
  - name: "The Ultimate Compression: From Neural Networks to Physical Laws"
---


## Defining the Simulator: World Models

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> We treat a world model as an invertible map \(f: \mathcal{X} \rightarrow \mathcal{Y}\) that can be checked like an integer block: every forward translation must be paired with an equally consistent inverse.
</div>

The ICML 2025 survey on world models frames the simulator as an invertible mapping between latent state spaces, emphasizing that correct prediction requires both forward simulation and backward inference <d-cite key="dingUnderstandingWorldPredicting2025 vafaEvaluatingWorldModel2024 vafaWhatHasFoundation2025"></d-cite>. Given input \(x\) and output \(y=f(x)\), the model must also recover \(x=g(y)\) whenever the pair obeys the physical correlation. This bidirectional contract is what we call the “integer block check” of a world model, and it naturally surfaces every time we talk about an image inversion problem or an image-to-image translation pipeline.

The language community has internalized this definition. If we know “A is B’s father,” a large language model can infer the inverse relation “B is A’s son,” demonstrating that symbolic simulators can track state inversions over text <d-cite key="liHowDoLanguage2025 wangCanLanguageModels2024 mancoridisPotemkinUnderstanding2025"></d-cite>. Remote sensing image problems, however, remain mostly one-directional: we train a model to go from sketch to photo, edge map to RGB, or optical to SAR, but seldom demand the reversed prediction without explicit labels or textual scaffolding. This blog rewrites our approach so that remote sensing images, world model reasoning, and image-to-image translation all coincide under a single inversion requirement.

{% raw %}{% include figure.liquid path="assets/img/2026-04-27-remote-sensing-foundation-models-to-world-models/world_models_timeline.png" class="img-fluid" %}{% endraw %}
<div class="caption">
    The timeline of world models defined in <d-cite key="dingUnderstandingWorldPredicting2025"></d-cite>.
</div>

## Capturing the State: Remote Sensing Foundation Models

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Remote sensing foundation models compress planetary state, but the richest supervision comes from native, co-registered sensors such as Sentinel-1/2, commercial VHR constellations, and hyperspectral payloads.
</div>

Remote sensing foundation models (RSFMs) already excel at encoding massive, heterogeneous snapshots of the Earth—think Prithvi-EO-2.0, TerraFM, the Unified Copernicus FM, Galileo, and MMEarth learned from petabytes of Sentinel imagery <d-cite key="szwarcmanPrithviEO2025 danishTerraFM2025 wangUnifiedCopernicus2025 tsengGalileo2025 nedungadiMMEarth2024"></d-cite>. Yet most of these models learn a single modality at a time, forfeiting the best part of remote sensing imagery: the fact that the same geolocation and timestamp can be sensed by radar, multispectral, hyperspectral, or even video payloads that jointly describe the state of the world.

When we align Sentinel-1 SAR backscatter, Sentinel-2 optical reflectance, high-resolution commercial RGB, and derived feature layers such as NDVI, DEM, or interferometric coherence, we obtain a dense lattice of measurements that is closer to the latent state space \(\mathcal{X}\) than any handcrafted sketch or edge map. The signal is high-bandwidth, continuous, and machine-native—no semantic shortcutting is required. Importantly, this remains a video-only (purely visual) problem: we operate entirely on sensor values rather than injecting textual hints.

## The Synthesis Pathway: Bridging Observation and Simulation

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Native multi-sensor pairs (optical ↔ SAR, SAR ↔ multispectral, hyperspectral ↔ elevation) are the ideal bridge between remote sensing observation and world model simulation.
</div>

Classic image-to-image translation data sets rely on man-made derivatives—edge maps, segmentation masks, sketches—that reuse the exact same photons as the target image <d-cite key="isolaPix2Pix2017 zhuCycleGANICCV2017 zhuCycleGANArXiv2020"></d-cite>. These surrogates introduce little new information, making it difficult to evaluate “world understanding” when perceptual metrics (FID, LPIPS, DISTS) cannot distinguish stylistic but semantically wrong generations. Even sophisticated bridges such as BBDM, BiBBDM, I$^2$SB, or diffusion-bridge solvers enforce bidirectionality without introducing genuinely new sensing physics <d-cite key="liBBDMImageImage2023 xueBiBBDM2025 liuI2SB2023 chungDirectDiffusionBridge2023 zhouDenoisingDiffusionBridge2024"></d-cite>. We need data that injects complementary measurements instead of stylized copies.

Geolocation-aligned remote sensing corpora such as OpenEarthMap-SAR, EarthView, TerraMesh, SARLANG-1M, SAR-TEXT, and TerraFM already provide multiple modalities per scene <d-cite key="xiaOpenEarthMapSAR2025 velazquezEarthView2025 blumenstielTerraMesh2025 weiSARLANG1M2025 heSARTEXT2025 danishTerraFM2025"></d-cite>. They capture Sentinel-1/Sentinel-2 stacks, airborne SAR, multispectral or hyperspectral cubes, and even ancillary layers like NDVI, DEM, or land-surface temperature. Because every modality views the same object with different physics, translating between them is a true inversion task: a successful model must infer hidden structure (e.g., moisture content) that is not explicitly present in the source channel.

This pathway finally lets us move from “labeling the world” to “modeling the world.” We can propose evaluation protocols where the forward pass (optical → SAR) and the backward pass (SAR → optical) are both required, and where accuracy is computed via native geophysical metrics such as backscatter reconstruction error, band-wise spectral angular distance, or task-specific retrieval scores.

## "What I Cannot Create, I Do Not Understand"

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Generative modeling, framed as inversion, is our litmus test: if a model can recreate a missing modality from another, it has internalized the governing physics.
</div>

Following Feynman’s mantra, we design a zero-shot or one-shot in-context protocol that forces models to both observe and create <d-cite key="huhPlatonicRepresentationHypothesis2024 lecunPathTowardsAutonomous2025"></d-cite>. The prompt template works like this:

1. Provide a reference pair \((\text{Optical}_A, \text{SAR}_A)\) and optionally an additional \((\text{Optical}_B, \text{SAR}_B)\) pair.
2. Supply only the target modality for a new scene (e.g., \(\text{SAR}_C\)) while reminding the model that “the first image is the input, the second image is the output” based on the reference pair.
3. Ask the model to generate the missing modality (here, \(\text{Optical}_C\)) purely from visual cues. No textual description of the physics or transformation is given.

We apply the same protocol in reverse (optical → SAR) to complete the integer block check. Contemporary multimodal generators are surprisingly well-suited for this experiment: BELGiUM (BetterDownSeed), OmniGen, OmniGen 2, GPT-Image-1, LanoBlana and LanoBlana Pro, Flux, Flux.2 / FluxContext (BlackForestLab), GPT-Image-Edit, Queen Image Edit, and similar reference-guided editors can all ingest exemplars plus a query frame <d-cite key="xiaoOmniGen2025 chenUniReal2025 rotsteinPathwaysImageManifold2025 yangCogVideoX2025 chenLearningWorldModels2025 liuWorldWeaver2025 kong3D4DWorldModeling2025 guiImageWorld2025 trevithickSimVS2025 assranVJEPA22025 chenDenoisingJEPA2025 assranSelfSupervisedLearning2023 metaVJEPA2025"></d-cite>. Our study probes whether these vision foundation models already encode a latent “world model” that generalizes from one paired example to a new scene.

Because the supervision is native imagery, we can score results with physics-aware metrics (speckle-preserving SSIM, coherent change detection residuals, spectral angle mapper, NDVI consistency, DSM slope errors) rather than relying solely on human preference tests. If the generated image cannot drive downstream tasks—flood mapping, ship detection, or crop-type classification—we know the model has not truly understood the world it is trying to recreate.

## Beyond the Textual Shortcut: Why World Models Need More Than Language

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Text is a low-bandwidth zip file of human experience; remote sensing Any2Any signals carry the raw physics that a world model must master.
</div>

Large language models already solve inversion puzzles in the semantic domain, but they operate on abstractions that humans curated <d-cite key="radfordLearningTransferableVisual2021 vafaWhatHasFoundation2025 liHowDoLanguage2025 wangCanLanguageModels2024"></d-cite>. That shortcut is useful for reasoning, yet it obscures the underlying dynamics we care about when modeling the planet. Remote sensing image inversion has no textual crutch: the “prompt” is another image, and the supervision is whether we recover the correct electromagnetic response. This is a stricter and more informative test for world models.

By grounding the simulator on Any2Any sensor data (optical, SAR, multispectral, hyperspectral, LiDAR, DEM, NDVI, even atmospheric soundings), we keep the learning signal in the original units of reality <d-cite key="luRemoteSensingWorldModel2025 wangUnifiedCopernicus2025 kong3D4DWorldModeling2025"></d-cite>. The model must therefore learn causal, geometric, and radiometric relationships instead of memorizing word co-occurrences. We see this as the missing stepping stone between today’s LLM-style “world models” and physically grounded ones.

## The Ultimate Compression: From Neural Networks to Physical Laws

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Remote sensing inversion pushes models toward the efficient compression offered by physical laws, because success demands learning the reversible rules that govern Earth observation.
</div>

Foundation models approximate the world through billions of parameters; physics compresses it into a few equations <d-cite key="lecunPathTowardsAutonomous2025 zhangWhenDoNeural2025 vafaEvaluatingWorldModel2024 kong3D4DWorldModeling2025"></d-cite>. Remote sensing provides the empirical bridge between the two. When a model can both synthesize SAR from optical and reclaim optical from SAR—respecting incidence angles, polarization, sun glint, and terrain-induced foreshortening—it implicitly captures the governing equations without us hard-coding them. That is the path from brute-force memorization to elegant simulation.

Our next steps are clear: (1) curate geolocation-aligned, multi-modality benchmark splits (Sentinel-1/2, commercial VHR, hyperspectral, NDVI, DEM) with proper citations for OpenEarthMap-SAR, EarthView, TerraMesh, TerraFM, SARLANG-1M, and SAR-TEXT; (2) finalize evaluation metrics that measure both perceptual fidelity and geophysical correctness; (3) run the zero/one-shot inversion protocol across BELGiUM, OmniGen/2, GPT-Image-1, LanoBlana(Pro), Flux/Flux.2, FluxContext, GPT-Image-Edit, Queen Image Edit, and other multimodal foundation models; and (4) report whether any current system already behaves like a world model in this purely visual regime. If not, we have a concrete research agenda for bringing world models to the remote sensing image domain <d-cite key="xiaOpenEarthMapSAR2025 velazquezEarthView2025 blumenstielTerraMesh2025 danishTerraFM2025 weiSARLANG1M2025 heSARTEXT2025 xiaoOmniGen2025 chenUniReal2025"></d-cite>.
