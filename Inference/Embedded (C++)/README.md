# Embedded ML Inference Using C++

## Executing Models on Embedded Devices

### Logging Data

- We need a structure that allows us to report tensorflow errors during execution and inference.

```cpp
#include "tensorflow/lite/experimental/micro/micro_error_reporter.h"

// Logs debug information during inference (subclass of tflite::ErrorReporter).
::tflite::MicroErrorReporter micro_error_reporter;

::tflite::ErrorReporter* error_reporter = &micro_error_reporter;
```

### Build Model Structure

- Turn a raw C-array of model data into a comprehensible and usable model structure.

```cpp
#include "tensorflow/lite/schema/schema_generated.h"

// File compiled by the tflite model.
#include "some/path/to/model_data.h"

const tflite::Model* model = ::tflite::GetModel(model_data);

if (model->version() != TFLITE_SCHEMA_VERSION) {
    error_reporter->Report("Model version does not match schema.");
}
```

### Operator Resolver

**TLDR: If a model uses a math operation, it must be in the operator resolver.**

- Defines which mathematical operations are linked into the program and available for the machine learning model to use at compile time.

```cpp
#include "tensorflow/lite/experimental/micro/kernels/all_ops_resolver.h"

::tflite::ops::micro::AllOpsResolver resolver;
```

**Note: You might not want to link in ops you aren't using, so try a MicroMutableOpsResolver if you want to limit the ops linked in.**

### Defining Tensor Arena Size

**TLDR: Allocate an area of working memory for the model's inputs, outputs, and intermediate tensors.**

**Important: ONLY depends on the architecture of the model itself, not what platform.**

#### How to find arena size?

- Trial and Error

    - Set the size high, and keep reducing until it stop working

- Profiling

    - Some platforms have built-in profilers to figure out tensor arena size

- Determine from Interpreter

    - Declare a TFLite Micro Interpreter with maximum memory usage
    - Once tensors are allocated, call `interpreter.arena_used_bytes()` and you will fine arena size
    - I found a good example [here](https://github.com/edgeimpulse/tflite-find-arena-size/blob/master/source/find_arena_size.h)

```cpp
const int tensor_arena_size = 2048;
uint8_t tensor_arena[tensor_arena_size];
```

### Creating an Interpreter

```cpp
#include "tensorflow/lite/experimental/micro/micro_interpreter.h"

::tflite::MicroInterpreter interpreter(model, resolver, tensor_arena, tensor_arena_size, error_reporter);

// Allocate memory for execution.
interpreter.AllocateTensors();
```

### Executing Inference

```cpp
// From the interpreter object, we can find a pointer to the input tensor.
TfLiteTensor* input = interpreter.input(0);

// Set the pointer to some input value (data is a TfLitePtrUnion).
input->data.f = {10.0f};

// Execute inference.
if(interpreter.Invoke() != kTfLiteOk) {
    error_reporter->Report("Inference Failed\n");
}

// Read output.
TfLiteTensor* output = interpreter.output(0);
float* output_float_arr = output->data.f;
```


