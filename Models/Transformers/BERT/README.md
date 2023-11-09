# Bidirectional Encoder Representations From Transformers (BERT)

## Table of Contents

- [Building BERT Applications](#building-bert-applications)
- [General Notes](#notes)

---

## Building BERT Applications

### Step 1: Pretraining of getting a BERT Model

- Doing NSP or Masked Language Modeling to pretrain BERT

- Potentially re-training on language more specific to downstream application (e.g. medical language)

### Step 2: Fine-Tuning BERT with Downstream Tasks

- Initialize BERT Model with pre-trained data

- Continue training BERT with downstream task

---

## Notes


Only uses blocks of encoders from Transformers

**Important: No decoder stack**

- No decoder stack, only provide representation of data
- Produce better results with more dimensionality of data and more training date

### How BERT was trained

BERT was used as the encoder representation frontend for training two tasks in order to give BERT the ability to *better represent* the data.

#### Task 1: Masked Language Modeling

Original Sentence: `The cat sat on the dog`

- Randomly masked input: `The cat sat on <MASK> dog`

- Example output: `that`

- Expected output: `the`

Sometimes mask is replaced by a random token, or not replaced at all to prevent overfitting.

#### Task 2: Next Sentence Prediction

Original Two Sentences: `The cat slept on the rug. It likes sleeping.`

- Preprocessed Input: `The cat slept on the rug <SEP> It likes sleeping <SEP>`

- Example output: `IsNextSentence`

- Expected output: `IsNextSentence`

It tries to predict whether the next sentence likely follows the first one, for example `The cat slept on the rug <SEP> The president was angry <SEP>` would probably return `NotNextSentence`
