# Hello

Welcome to my blog. This blog contains some of the notes I've taken when reading papers, books or something else. Now the topics are updated to Computer Vision (temporarily including object detection, ImageNet evolution and semantic segmentation) and Natural Language Processing (temporarily including only some prior knowledge, deep learning methods are on the TODO list). The articles are all in Chinese since the papers are all in English. 

Hope this can push me learning all the time :-)

## About me

I'm an undergraduate student at Department of Electronic Engineering, Tsinghua University. Next year I'm starting PhD at THU-NGNLAB, majoring natural language processing and knowledge graph. Also, I'm a research intern at Owlii Inc. in 2017 summer vacation, studying computer graphics and 3D deep learning.

## Content
- Computer Vision
    - Object detection
        - Region Proposal Based Method
            - [Selective Search](Object_detection/Selective_Search.md)
            - [R-CNN (Region-based CNN)](Object_detection/R-CNN.md)
            - [SPP (Spatial Pyramid Pooling)](Object_detection/SPP.md)
            - [Fast R-CNN (Faster version of R-CNN)](Object_detection/Fast_R-CNN.md)
            - [Faster R-CNN (Faster version of Fast R-CNN)](Object_detection/Faster_R-CNN.md)
            - [FPN (Feature Pyramid Network)](Object_detection/FPN.md)
            - [Mask R-CNN (Add semanic segmentation task on Faster R-CNN)](Object_detection/Mask_R-CNN.md)
        - Without Proposal Method
            - YOLO (You only look once, object detection without region proposals)
            - SSD (Single-shot MultiBox Detector, object detection without region proposals)
    - ImageNet Evolution
        - [AlexNet (The first neural network for ImageNet classification, ILSVRC 12 winner)](ImageNet_evolution/AlexNet.md)
        - [GoogLeNet (ILSVRC 14 winner, Inception v1)](ImageNet_evolution/GoogLeNet.md)
            - [Inception v2 (Inception + BN)](ImageNet_evolution/Inception-v2.md)
            - [Inception v3 (Inception + Factorization)](ImageNet_evolution/Inception-v3.md)
            - [Inception v4 (Optimized Inception v3)](ImageNet_evolution/Inception-v4.md)
            - [Inception-ResNet v1 (Inception + Residual Connection)](ImageNet_evolution/Inception-ResNet-v1.md)
            - Inception-ResNet v2
        - VGGNet (ILSVRC 14 second winner)
        - ResNet (ILSVRC 15 winner, COCO 15 winner)
        - ResNeXt (Combined version of ResNet and Inception)
        - DenseNet (CVPR 2017 Best Paper)
    - Semantic Segmentation
        - [FCN (Fully Connected Networks)](Semantic_segmentation/FCN.md)
        - DeepLab (First introducing CRF)
        - FCIS (COCO 2016 winner)
        - R-FCN (Region-based FCN)
    - 3D Deep Learning
        - PointNet & PointNet++ (Point cloud deep learning architecture)
        - PointCNN (Another point cloud deep learning method)

- Natural Language Processing
    - Machine Learning basis
        - [LDA (Latent Dirichlet Allocation)](Machine_learning_basis/LDA.md)
        - CRF (Conditional Random Field)
    - Relation Extraction
        - Fully Supervised Method
        - Distant Supervised Method

## TODO

- DESIGN SIDE BAR NAV MENU
- Complete the above list

## Note

- This page originally come from jekyll Cayman theme. All the other parts and color adjustments are written by myself with CSS and JS. Writing a static web page is tried but interesting :-) The network architecture is supported by ```mermaid``` ([Github](https://github.com/knsv/mermaid)). Great thanks to the author!
- The side bar is under construction.
