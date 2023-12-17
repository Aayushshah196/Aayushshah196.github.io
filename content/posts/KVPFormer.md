+++ 
title = "KVPFormer" 
date = "2023-08-09" 
author = "Aayush Shah Kanu" 
readingTime = true 
cover = "images/blogs/kvpformer/architecture.png" 
description = "KVPFormer is a new QA-based key-value pair extraction approach that uses Transformer encoders to identify key entities in form-like document images. The approach predicts their corresponding answers in parallel, reducing learning difficulty and improving prediction accuracy. Additionally, it introduces a spatial compatibility attention bias to better model spatial interactions between entities. KVPFormer outperforms previous methods by 7.2% and 13.2% in F1 score on FUNSD and XFUND datasets." 
+++

What is it?
A new question-answering based key-value extraction approach.
First identifies key entities from all entities in an image with a Transformer encoder, then takes these key entities as questions and feeds them into a Transformer decoder to predict their corresponding answers
To get higher accuracy, it proposes a coarse to fine approach. In the coarse stage, the answer candidates for a question entity are identified and in the fine stage, among the answer candidates, the most likely answer is predicted.

Why are we looking into it?
Outperforms previous best-performing model for FUNSD & XFUNSD by 7.2% and 13.2% in F1-score respectively.

Main contributions of the paper:
Formulates key-value extraction as QA problem and proposes new Transformer based encoder-decoder model
Introduce three effective techniques, namely a DETR-style decoder, a coarse-to-fine answer prediction algorithm and a new spatial compatibility attention bias into the self-attention/cross-attention mechanism, to improve significantly both the efficiency and accuracy

Main Concept used for Question-Answering: 
Instead of finding answers for questions, it finds the questions for the answers. It assumes that a question entity can have multiple answer entities but an answer entity can have only one question entity. 
Example: On FUNSD and XFUND datasets, since one key entity may have multiple corresponding value entities while each value entity only has at most one key entity, we follow (Zhang et al. 2021) (Paper Link) to first identify all the value entities as questions and then predict their corresponding key entities as answers to obtain key-value pairs

Architecture

Methodology
Entity Representation Extraction
Uses layoutlm / layoutxlm as a base for entity representation and token classification
Considers document as a sequence of n semantic entities ( D = [E1, E2, …, En]  ) where an entity is a group of words [w1, w2, …, wn]
The 1D sequence of the entity is tokenized and sent to the feature extractor to get the embedding of each token. After that, Average the embeddings of all the tokens in each entity to obtain its entity representation.


Question Identification with Transformer Encoder
We feed all entity representations into a Transformer encoder to identify questions. To explicitly model the spatial relationships between different entities, we introduce a new spatial-aware self-attention mechanism into the self attention layers in the vanilla Transformer encoder

Uses a binary classification to predict whether the entity is other or non-other(Question)

Coarse-to-Fine Answer Prediction with Transformer Decoder
The question representation from the previous encoder is fed to the transformer decoder to predict the candidate entity (answers) in parallel in the coarse stage and the most likely one is selected in the fine stage.
Let H=[h1,h2,...,hn] denote entity representations and Q=[q1,q2,...,qm] denote the question representations. 
In the coarse stage, a binary classifier is used to get the likelihood of each entity being an answer for that question. 


This gives a score to an entity hj for how likely it is the answer to the question qi. It is sorted in descending order to get the candidate answers.

In the fine stage, among the candidate answers, a multi-class classifier is used to get the most likely answer.


Loss function
Question Identification Loss
Softmax cross-entropy loss is calculated for the labels of entity and predicted logits from representation of transformer encoder


Coarse-to-Fine Answer Prediction Loss
Binary cross-entropy loss for coarse stage of answer prediction

M: Number of question entity and N: Total number of entity

Softmax cross-entropy loss for multi-class fine stage answer prediction

Overall Loss
The sum all the previous losses as the modules are jointly trained in end-to-end manner

Dataset and Experiments


The gold label or entity label representation is concatenated to improve the accuracy following the results from SERA(Zhang et al. 2021)
*SERA: (Semantic Entity Relation extraction As dependency parsing) (Paper Link)
