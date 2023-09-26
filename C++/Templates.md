# Templates

## Must Read Section

- Allows you to create one class/function for multiple types
- Eliminate redundancies in code, reducing bugs
- High-Performance and allows for compilation optimization (reduces space)


### Don't Do This
- Don't use `&` to get the address, use `std::addressof()` as the `&` operator can be overloaded for the object.
- Don't do any complicated template metaprogramming
- [Avoid code bloat](#code_bloat)

### Do This
- You can use `inline` to recommend to the compiler that it gets inlined, but it's up to the computer


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

## Pitfalls and Issues

#### <h3 id="code_bloat">Avoiding Code Bloat</h3>

This is so important it's in a separate section. Don't bloat the code.
```cpp
// DON'T DO THIS...
std::unique_ptr<StarShip> BuildStarShip(int8_t type) {
    switch (type) {
        case 0:
            return std::make_unique<StarShip</*model_no=*/142,/*crew_compliment=*/100>>();
        case 1:
            return std::make_unique<StarShip</*model_no=*/1701,/*crew_compliment=*/200>>();
        case 2:
            return std::make_unique<StarShip</*model_no=*/1704,/*crew_compliment=*/150>>();
        default:
            return std::make_unique<StarShip<0, 0>();
    }
}
```
In that example, when linking happens, it literally creates 3 StarShip objects, but you might only use 1, this is code bloat. Instead, do this:
```cpp
// DO THIS...
constexpr int GetModelNumber(int8_t type) {
    switch(type) {
        case 0:
            return 142;
        case 1:
            return 1701;
        case 2:
            return 1704;
        default:
            return 0;
    }
}

constexpr int GetCrewCompliment(int8_t type) {
    switch(type) {
        case 0:
            return 100;
        case 1:
            return 200;
        case 2:
            return 150;
        default:
            return 0;
    }
}

std::unique_ptr<StarShip> BuildStarShip(int8_t type) {
    return std::make_unique<StarShip<GetModelNumber(type), GetCrewCompliment(type)>>();
}
```