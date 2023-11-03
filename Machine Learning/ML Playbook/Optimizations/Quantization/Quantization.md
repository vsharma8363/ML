# Quantization Overview

# TODO(vik): Reformat this to be an overview to quantization as outlined here: https://deci.ai/quantization-and-quantization-aware-training/, and then create a guide for cheatsheet to quantization in every library, then link to colabs where you have that info.

**TLDR: Convert 32-bit floats to 8-bit integers for smaller size and faster execution**

- Default: Models store weights/biases as `32-bit` FP numbers for *high-precision calculations*
- Quantization --> Allows you to reduce precision into 8-bit integers (*4x size reduction*)

Pros:
- Less Size
- Faster Runtime
- Little Degradation of Model Accuracy

Cons:
- Rarely Can Reduce Accuracy

# Post-Training Quantization (PTQ)

| PTQ Type | Ideal Environment | Speedup | Size Reduction |
|---|---|---|---|
| Dyanmic Range | CPUs | 2x-3x | **4x** |
| Full-Integer | CPUs, Edge TPUs, MCUs | **3x+** | **4x** |
| 16x8 | Integer-only hardware (MCUs, Edge TPUs) | Slight | **3x-4x** |
| Float16 | GPUs | Some on GPUs, increase runtime on CPUs | 2x |

**TLDR: Consult the [PTQ documentation](https://www.tensorflow.org/lite/performance/post_training_quantization) to learn about the types of optimizations you can do.**

## Dynamic Range Quantization

**TLDR: Convert weights and biases to integers but keep everything else as floats.**

### Ideal Environment:
- CPUs

### Benefits:
- 4x smaller
- 2x-3x speedup

### Fast Facts:
- Good starting point
- Does not require representative dataset
- Outputs still stored in floating-point

### Example:

```py
import tensorflow as tf

# Boilerplate model loading code from TFLiteConverter.
converter = tf.lite.TFLiteConverter.from_saved_model(saved_model_dir)

converter.optimizations = [tf.lite.Optimize.DEFAULT]

tflite_quant_model = converter.convert()
```

## Full-Integer Quantization

**TLDR: Convert weights, biases, activations, etc. to integers but keep the interface as floats.**

### Ideal Environment:
- CPUs, Edge TPU, MCUs

### Benefits:
- 4x smaller
- 3x+ speedup

### Fast Facts:
- Converts all numbers to integers
- Requires representative dataset to calibrate all quantized variables

### Scenario #1: You can use floats for I/O interface:

```py
# ... same code as previous example

converter.optimizations = [tf.lite.Optimize.DEFAULT]

# Add representative dataset to automatically activate FULL-INTEGER QUANTIZATION.
converter.representative_dataset = representative_dataset_gen

tflite_quant_model = converter.convert()
```

### Scenario #2: You can NOT use floats for I/O interface:

```py
# ... same code as previous example

converter.optimizations = [tf.lite.Optimize.DEFAULT]

converter.representative_dataset = representative_dataset

# Force only int-8 representation for data.
converter.target_spec.supported_ops = [tf.lite.OpsSet.TFLITE_BUILTINS_INT8]
converter.inference_input_type = tf.int8  # or tf.uint8
converter.inference_output_type = tf.int8  # or tf.uint8

tflite_quant_model = converter.convert()
```

## Float16 Quantization

**TLDR: Convert weights to float16 and run faster on GPUs that support lower-precision execution.**

**IMPORTANT: DO NOT RUN ON CPUs, it will scale the float16 -> float32 and result in slower performance.**

### Ideal Environment:
- GPUs

### Benefits:
- 2x smaller
- Some speedup on GPU

### Example:

```py
# ... same code as previous example

converter.optimizations = [tf.lite.Optimize.DEFAULT]

converter.representative_dataset = representative_dataset

# Force only float-16 representation for data.
converter.target_spec.supported_types = [tf.float16]

tflite_quant_model = converter.convert()
```

## 16x8 Quantization

**TLDR: Scale activations to 16-bit int and weights to 8-bit**

### Ideal Environment:
- Integer-only hardware (MCUs, Edge TPUs)

### Benefits:
- 3x-4x smaller
- Some speed improvements

### Example:

```py
# ... same code as previous example

converter.optimizations = [tf.lite.Optimize.DEFAULT]
converter.representative_dataset = representative_dataset

# Set 16x8 activation and weights setting.
converter.target_spec.supported_ops = [tf.lite.OpsSet.EXPERIMENTAL_TFLITE_BUILTINS_ACTIVATIONS_INT16_WEIGHTS_INT8]

tflite_quant_model = converter.convert()
```