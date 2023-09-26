# Model and Composition

## Strategies

### Transfer Learning

Very good starting point for a model

#### 1. Find Original Model

Find a model trained on some similar 'source domain' problem to what you 'target domain' problem is.

Places to look:
- TFHub

#### 2. Remove Last Layer of Network + Some Fully Connected Layers

This step essentially involves removing all of the non-feature-extraction layers from the model.

#### 3. Replace Removed Layers With New Layers For Problem

Add in many layers to overfit for the problem, and then you can adjust accordingly.

#### 4. Freeze All Pre-Existing Layers

#### 5. Train Network on Target Domain

Train the non-frozen new layers you added on the target domain data.

#### 6. [Optional] Unfreeze Layers + Fine-Tuning

Unfreeze the layers from the original model once you have something close to convergence and fine-tune the feature extraction portion to your problem.

### Geoffrey Hinton General Approach

#### 1. Investigate Similar Problems

Find a problem that has been solved similarly and pick it as a starting point for architecture

#### 2. Add Layers/Neurons Using Best Guess Until Network Overfits

#### 3. Use Strategies to Reduce Overfitting (And Variance)

- You can use many strategies to solve this problem of overfitting:
    - Dropout (Start with 0.5)
    - Regularization
    - Early Stopping