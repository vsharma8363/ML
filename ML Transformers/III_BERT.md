# Bidirectional Encoder Representations from Transformers

### TLDR: BERT is a model that provides amazing representation of text sequences which can then be used by downstream models.

- Does not use the decoder stack of the transformer architecture
- Core change: **Bidirectional Attention**

Basically, the idea is as follows:
- Replace the attention layer of the transformer model with the masked self-attention layer
- Typically, the attention layer masking works with next word masking:
    - Example Input: "I like cats a lot"
    - Masked Attention Input: "I like <MASKED>"

For BERT, a random token is masked:
    - Example Input: "I like cats a lot"
    - Masked Attention Input: "I like <MASKED> a lot"

## Two Methods of Training

### Masked-Language Modeling
#### TLDR: Obfuscate a random token at random times

- The masking effectively follows three strategies:
    - Surprise model by not masking any token 10% of the time
    - Replace a token with a random token 10% of the time
    - Mask a token randomly on 80% of the dataset

### Next Sentence Prediction
#### TLDR: Show two sentences in sequence and 50% of the time, just use a random unrelated second sentence

## Pretraining + Fine-Tuning

### Pretraining

#### TLDR: Help the model understand the language

- Define the architecture
- Train the model on MLM and NSP tasks

### Fine-Tuning

#### TLDR: Setup the downstream model and fine-tune the parameters for specific tasks

