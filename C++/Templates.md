# Templates

- Pros:
    - Allows you to create one class/function for multiple types
    - Eliminate redundancies in code, reducing bugs
    - 
- Cons:
    - 


## Function Templates

#### Use Case:
- You would need multiple functions each with a corresponding type

#### Example:

```cpp
// DONT DO THIS :( ...

int CalculateMaximum(int a, int b) {
    return a < b ? b : a;
}

double CalculateMaximum(double a, double b) {
    return a < b ? b : a;
}

// DO THIS :) ...

// Returns the maximum or a if they are equal.
template<Typename T>
T CalculateMaximum(T a, T b) {
    return a < b ? b : a;
}
```

## Class Templates

#### Use Case:
- You would need multiple classes each with a corresponding type
    - Example: You have a class that does some array manipulation but the type of the element in the array is flexible.

#### Example:
```cpp
typdef int8_t ReturnStatus;

template <typename T, int64_t kSomeValue>
class SomeClass {
    public:
        explicit SomeClass() = default;
        
        ReturnStatus AddValue(T value) {
            if (storage_.full()) {
                return -1; // Array is full.
            }
            storage_.push_back(value);
        }

        ReturnStatus GetValue(T* value, int64_t index) {
            if (storage_.empty()) {
                return -1; // Array is empty.
            }
            elif (storage_.size() - 1 < index) {
                return 0; // Array does not contain element.
            }
            return storage_[index];
        }

    private:
        std::array<T, kSomeValue> storage_;
}

// Use elsewhere...
SomeClass<int64_t> some_int_class;
```

### Pitfalls and Issues

Templates are super powerful, but can cause tons of code-bloat when used without caution. Here's an example.

```cpp

```