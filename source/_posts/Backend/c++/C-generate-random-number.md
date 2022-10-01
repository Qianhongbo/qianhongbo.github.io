---
title: C++ generate random number
date: 2022-09-14 12:27:50
categories: 
- c++
tags: 
- c++
---

```c++
#include <iostream>

int main() {
  // initialize random seed
  srand(time(NULL));
  // * generate random number
  for (int i{0}; i < 10; i++) {
    std::cout << rand() % 10 << " ";
  }
  std::cout << "\n";

  // * generate random number in range [a, b), e.g. [3, 6)
  for (int i{0}; i < 10; i++) {
    std::cout << rand() % (6 - 3) + 3 << " ";
  }
  std::cout << "\n";

  // * generate random number in range [a, b], e.g. [3, 6]
  for (int i{0}; i < 10; i++) {
    std::cout << rand() % (6 - 3 + 1) + 3 << " ";
  }
  std::cout << "\n";

  // * generate random number in range (a, b], e.g. (3, 6]
  for (int i{0}; i < 10; i++) {
    std::cout << rand() % (6 - 3) + 3 + 1 << " ";
  }
  std::cout << "\n";
}

/*
1 5 6 4 5 2 1 2 1 9
3 4 3 4 4 5 3 4 5 3
6 5 4 4 5 3 5 4 3 6
4 6 4 4 6 5 6 5 4 5
*/
```

