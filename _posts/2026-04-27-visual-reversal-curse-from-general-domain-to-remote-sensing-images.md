---
layout: distill
title: "Visual Reversal Curse: From General Domain to Remote Sensing Images"
description: "The 'Reversal Curse' highlights a fundamental limitation in AI: models often fail to infer inverse relationships. This post investigates whether this curse extends to Vision Foundation Models and proposes remote sensing image translation as the optimal testbed for evaluating bidirectional visual generalization."
date: 2026-04-27
future: true
htmlwidgets: true

# Anonymize when submitting
authors:
  - name: Anonymous

# must be the exact same name as your blogpost
bibliography: 2026-04-27-visual-reversal-curse-from-general-domain-to-remote-sensing-images.bib

toc:
  - name: "The Reversal Curse: A Logic Failure"
  - name: "From Language to Vision"
  - name: "The Problem with Synthetic Benchmarks"
  - name: "Remote Sensing: A Physically Grounded Testbed"
  - name: "Testing In-Context Visual Inversion"
---

## The Reversal Curse: A Logic Failure

<div style="border: 2px solid #d32f2f; background-color: #ffebee; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> The "Reversal Curse" exposes a critical flaw in modern AI: learning "A is B" does not guarantee that a model understands "B is A". This logical gap challenges the assumption that current architectures learn robust, invertible representations of the world.
</div>

Recent findings in Large Language Models (LLMs) have unveiled a puzzling phenomenon: the **Reversal Curse**. Berglund et al. <d-cite key="berglundReversalCurseLLMs2024"></d-cite> demonstrated that models trained on factual statements like "Olaf Scholz was the ninth Chancellor of Germany" frequently fail to answer the inverse question, "Who was the ninth Chancellor of Germany?". Further studies have delved deeper into the limits of this generalization <d-cite key="linDelvingReversalCurse2024"></d-cite>, investigating whether models merely mimic understanding ("Potemkin Understanding") rather than building a coherent world model <d-cite key="mancoridisPotemkinUnderstandingLarge2025,vafaEvaluatingWorldModel2024,vafaWhatHasFoundation2025"></d-cite>.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/2026-04-27-visual-reversal-curse-from-general-domain-to-remote-sensing-images/reversal_curse_gpt4_example.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Inconsistent knowledge in GPT-4. GPT-4 correctly gives the name of Tom Cruise’s mother (left). Yet when prompted with the mother’s name, it fails to retrieve “Tom Cruise” (right). We hypothesize this ordering effect is due to the Reversal Curse. Models trained on “A is B” (e.g. “Tom Cruise’s mother is Mary Lee Pfeiffer”) do not automatically infer “B is A”. <d-cite key="berglundReversalCurseLLMs2024"></d-cite>
</div>

This is not merely a failure of memory, but a breakdown in **logical generalization**. If a model truly understood the relationship, the direction of inquiry shouldn't matter. Research suggests this stems from fundamental architectural and training constraints:

- **Factorization Bias** <d-cite key="kitouniFactorizationCurseWhich2024"></d-cite>: The left-to-right prediction objective biases the model against reverse inference.
- **Binding Failures** <d-cite key="wangReversalCurseBinding2025"></d-cite>: Transformers struggle to robustly associate entities bidirectionally.
- **Training Dynamics** <d-cite key="zhuTheoreticalUnderstandingReversal2024"></d-cite>: Standard gradient descent does not naturally encourage invertible mappings without explicit bidirectional data.

While this phenomenon is well-documented in text, a critical question remains: **Does the Reversal Curse extend to vision?** If a vision model learns to map a Sketch ($A$) to a Photo ($B$), can it instinctively infer how to map the Photo back to a Sketch?

## From Language to Vision

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Image-to-image translation is the visual equivalent of the "A is B" relationship. However, visual inversion is complex, requiring the model to understand structural, textural, and illumination transformations rather than just semantic links.
</div>

In computer vision, **image-to-image translation** <d-cite key="isolaImageToImageTranslationConditional2017,zhuUnpairedImageToImageTranslation2017"></d-cite> serves as the closest parallel to semantic inversion. Classic tasks include:
- **Segmentation $\leftrightarrow$ Image**: Converting labels to realistic scenes <d-cite key="leeMaskGANDiverseInteractive2020"></d-cite>.
- **Sketch $\leftrightarrow$ Photo**: hallucinating texture from outlines.
- **Map $\leftrightarrow$ Aerial**: translating abstract cartography to satellite views.

<div style="border: 2px dashed #999; padding: 20px; text-align: center; color: #555; margin: 20px 0; background-color: #f9f9f9;">
  <strong>[Figure Placeholder]</strong><br>
  Suggested: <em>Conceptual Diagram of Visual Reversal</em><br>
  Show a comparison between Text Reversal (Name $\leftrightarrow$ Description) and Visual Reversal (Sketch $\leftrightarrow$ Photo). This visually explains the analogy being made.
</div>

While specialized models like CycleGAN or BBDM <d-cite key="Li_2023_CVPR,xueBiBBDMBidirectionalImage2025"> handle these tasks effectively, and diffusion-based bridge models have further refined this <d-cite key="chungDirectDiffusionBridge2023,liuI$^2$SBImagetoImageSchrodinger2023,zhouDenoisingDiffusionBridge2024"></d-cite>, they typically rely on **explicit bidirectional training** (seeing both $A \to B$ and $B \to A$) or cycle-consistency losses. The deeper question concerns **generalization**: Can a Vision Foundation Model that has primarily seen forward generations (e.g., conditioning on a sketch to generate an image) zero-shot the inverse task?

## The Problem with Synthetic Benchmarks

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Standard benchmarks often rely on man-made or algorithmically generated features (sketches, edges) which create information asymmetry. This makes them poor candidates for testing true reasoning, as failure may stem from information loss rather than a lack of understanding.
</div>

Current evaluation datasets often utilize **man-made intermediate representations** which are inherently lossy:

- **Sketches/Edges**: Often abstract away texture, lighting, and depth.
- **Segmentation Maps**: Reduce complex objects to uniform class labels.

<div style="border: 2px dashed #999; padding: 20px; text-align: center; color: #555; margin: 20px 0; background-color: #f9f9f9;">
  <strong>[Figure Placeholder]</strong><br>
  Suggested: <em>Asymmetry of Synthetic Data</em><br>
  Illustrate how Photo $\to$ Edge is deterministic/lossy, while Edge $\to$ Photo requires generative hallucination. This highlights why synthetic benchmarks are unfair for testing logic.
</div>

These mappings are **asymmetric**. Generating a photo from a sketch requires "hallucinating" missing information, while generating a sketch from a photo involves "discarding" information (edge detection). If a model fails to invert $Sketch \to Photo$, it might simply be because the inverse task ($Photo \to Sketch$) is a fundamentally different algorithmic process (feature extraction) rather than a logical inversion. To test the Reversal Curse fairly, we need a domain where both directions are non-trivial and information-rich.

## Remote Sensing: A Physically Grounded Testbed

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> Remote sensing offers native, high-fidelity multimodal pairs (Optical $\leftrightarrow$ SAR). These modalities represent the same physical reality through different sensors, providing a rigorous testbed for bidirectional translation without the artifacts of synthetic data.
</div>

Remote sensing provides the ideal experimental ground with **native multimodal data**. Unlike general computer vision datasets where one modality is often man-made (e.g., edge maps, segmentation labels), earth observation satellites capture aligned views of the same location using distinct physical mechanisms. Common sources include **Sentinel-1 (SAR)**, **Sentinel-2 (Optical)**, and high-resolution commercial imagery.

This data is natively multi-spectral, often containing bands beyond the traditional RGB channels (e.g., Near-Infrared, Short-Wave Infrared). For a specific geolocation and timestamp, we can obtain multiple image instances—Optical, SAR, multi-spectral, or even hyperspectral—each capturing different physical attributes of the same underlying object.

<div style="border: 2px dashed #999; padding: 20px; text-align: center; color: #555; margin: 20px 0; background-color: #f9f9f9;">
  <strong>[Figure Placeholder]</strong><br>
  Suggested: <em>Optical vs. SAR Examples</em><br>
  Show a side-by-side comparison of the same location in Optical (RGB) and SAR. Highlight features visible in one but different in the other (e.g., metal structures, water bodies).
</div>

This creates an optimal setup for image-to-image translation that reflects real-world physical complexity. The modalities are complementary: different bands and spectrums represent distinct information channels about the same scene. Intuitively, yielding optimal translation results from one modality to another is a non-trivial task that requires understanding the underlying physical properties, not just surface-level statistics.

Datasets such as **OpenEarthMap-SAR** <d-cite key="xiaOpenEarthMapSARBenchmarkSynthetic2025"></d-cite>, **SARLANG-1M** <d-cite key="weiSARLANG1MBenchmarkVisionLanguage2025"></d-cite>, **EarthView** <d-cite key="Velazquez_2025_WACV"></d-cite>, **TerraFM** <d-cite key="danishTerraFMScalableFoundation2025"></d-cite>, **SAR-TEXT** <d-cite key="heSARTEXTLargeScaleSAR2025"></d-cite>, and **MMEarth** <d-cite key="nedungadiMMEarthExploringMultimodal2024"></d-cite> provide these naturally aligned pairs. Moreover, recent foundation models like **Prithvi-EO-2.0** <d-cite key="szwarcmanPrithviEO20VersatileMultiTemporal2025"></d-cite>, **TerraMesh** <d-cite key="blumenstielTerraMeshPlanetaryMosaic2025"></d-cite>, **Galileo** <d-cite key="tsengGalileoLearningGlobal2025"></d-cite>, and others <d-cite key="wangUnifiedCopernicusFoundation2025"></d-cite> offer a rigorous testbed for investigating visual reasoning.

## Testing In-Context Visual Inversion

<div style="border: 2px solid #ff9800; background-color: #fff3e0; padding: 15px; border-radius: 5px; margin: 15px 0;">
  <strong>Takeaway:</strong> We propose an in-context learning evaluation where models are shown forward examples (Optical $\to$ SAR) and tested on the inverse (SAR $\to$ Optical). This setup isolates the model's ability to generalize a learned transformation rule in reverse.
</div>

We propose a rigorous evaluation protocol leveraging the in-context learning capabilities of modern **Vision Foundation Models**. We examine unified understanding and generation models like **OmniGen** <d-cite key="xiaoOmniGenUnifiedImage2025,wuOmniGen2ExplorationAdvanced2025"></d-cite>, **UniWorld** <d-cite key="linUniWorldV1HighResolutionSemantic2025"></d-cite>, and **UniReal** <d-cite key="chenUniRealUniversalImage2025"></d-cite>, as well as emerging unified architectures <d-cite key="dengEmergingPropertiesUnified2025"></d-cite>. We also evaluate commercial and open-source editing models such as **GPT-Image-1** <d-cite key="openaiGPTImage1"></d-cite>, **Qwen-Image** <d-cite key="wuQwenImageTechnicalReport2025"></d-cite>, **Nano Banana Pro** <d-cite key="googleNanoBananaPro"></d-cite>, and **Flux** (e.g., Flux.1 Kontext <d-cite key="labsFLUX1KontextFlow2025"></d-cite>). Furthermore, frameworks like **Step1X-Edit** <d-cite key="liuStep1XEditPracticalFramework2025"></d-cite> and **In-Context Edit** <d-cite key="zhangInContextEditEnabling2025"></d-cite> enable us to define these tasks via prompting.

Our protocol formulates the inversion problem as a **zero-shot or one-shot in-context learning task**:

1. **Context**: We present the model with a source image (e.g., Optical Image A) and its corresponding target image (SAR Image A).
2. **Prompting**: We instruct the model implicitly: "Here is an input image [Optical A] and its result [SAR A]. Now, given this new result image [SAR B], generate the original input image."
3. **Constraint**: Crucially, we do **not** explicitly explain the physical transformation (i.e., "convert optical to SAR"). We rely on the visual cues alone. The model must infer the transformation rule from the context pair and then apply its inverse to the test image.

<div style="border: 2px dashed #999; padding: 20px; text-align: center; color: #555; margin: 20px 0; background-color: #f9f9f9;">
  <strong>[Figure Placeholder]</strong><br>
  Suggested: <em>Experiment Flowchart</em><br>
  Visualizing the prompt structure: [Context: Optical_1->SAR_1, ..., Optical_k->SAR_k] + [Query: SAR_test] -> [Output: ?]. Contrast this with the training direction.
</div>

We hypothesize that even powerful generative models may struggle with this inversion if they have not explicitly learned the bidirectional physics of these sensors. This mirrors the LLM Reversal Curse and would suggest that current "world models" are more akin to **associative engines** than true simulators of physical reality.
