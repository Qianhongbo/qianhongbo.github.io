---
title: c++多层Switch简化
date: 2022-02-10 18:07:40
categories: 
- Backend
tags: 
- c++
---

## 如何简化多层switch条件判断

### Logic before

```c++
int main() {
  switch(type) {
      case(Bird):
      		switch(color) {
        			case Red: ...
          		case Blue ...
      case Fox ...
      }
  }
}
```

### Simplify

```c++
#include <iostream>

enum class Color  {Red, Blue, End_};
enum class Animal {Bird, Fox, Lizard, End_};

template <typename Enum>
constexpr size_t enumValue(Enum aValue) {
    return static_cast<size_t>(aValue);
}

template<typename Enum>
constexpr size_t param() {
    return enumValue(Enum::End_);
}

// Just like a hash map
// Map every situation to an int
template<typename Enum1, typename Enum2>
constexpr size_t combineEnums(Enum1 e1, Enum2 e2) {
    return enumValue(e1) + param<Enum1>() * enumValue(e2);
}
```

```c++
int main() {
    auto theC1 = combineEnums(Color::Red, Animal::Lizard);
    switch(theC1) {
        case combineEnums(Color::Red, Animal::Bird):
            std::cout << "Red Bird\n";
            break;
        case combineEnums(Color::Red, Animal::Fox):
            std::cout << "Red Fox\n";
            break;
        case combineEnums(Color::Red, Animal::Lizard):
            std::cout << "Red Lizard\n";
            break;
        case combineEnums(Color::Blue, Animal::Bird):
            std::cout << "Blue Bird\n";
            break;
        case combineEnums(Color::Blue, Animal::Fox):
            std::cout << "Blue Fox\n";
            break;
        case combineEnums(Color::Blue, Animal::Lizard):
            std::cout << "Blue Lizard\n";
            break;
    }
    return 0;
}
```

