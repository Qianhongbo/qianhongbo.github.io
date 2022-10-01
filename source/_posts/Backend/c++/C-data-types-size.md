---
title: C++ data types size
date: 2022-10-01 13:09:44
categories: 
- c++
tags: 
- c++
---

## Result

```
the size of bool is: 1
the size of char is: 1
the size of short is: 2
the size of int is: 4
the size of unsigned int is: 4
the size of long is: 8
the size of unsigned long is: 8
the size of float is: 4
the size of double is: 8
the size of long int is: 8
the size of long long is: 8
the size of long double is: 8
the max of short is 32767
the min of short is -32768
the max of int is 2147483647
the min of int is -2147483648
the max of unsigned int is 4294967295
the min of unsigned int is 0
the max of long is 9223372036854775807
the min of long is -9223372036854775808
the max of unsigned long is 18446744073709551615
the min of unsigned long is 0
the max of long long is 9223372036854775807
the min of long long is -9223372036854775808
```

## Code 

``` c++
#include <iostream>
#include <limits>

int main() {
  bool a;
  char b;
  short c;
  int d;
  unsigned int e;
  long f;
  unsigned long g;
  float h;
  double i;
  long int j; // this is the same as long
  long long k; // for linux x64 long and long long are both 8 bytes
  long double l;

  std::cout << "the size of bool is: " << sizeof(a) << "\n";
  std::cout << "the size of char is: " << sizeof(b) << "\n";
  std::cout << "the size of short is: " << sizeof(c) << "\n";
  std::cout << "the size of int is: " << sizeof(d) << "\n";
  std::cout << "the size of unsigned int is: " << sizeof(e) << "\n";
  std::cout << "the size of long is: " << sizeof(f) << "\n";
  std::cout << "the size of unsigned long is: " << sizeof(g) << "\n";
  std::cout << "the size of float is: " << sizeof(h) << "\n";
  std::cout << "the size of double is: " << sizeof(i) << "\n";
  std::cout << "the size of long int is: " << sizeof(j) << "\n";
  std::cout << "the size of long long is: " << sizeof(k) << "\n";
  std::cout << "the size of long double is: " << sizeof(l) << "\n";

  std::cout << "the max of short is " << std::numeric_limits<short>::max() << "\n";
  std::cout << "the min of short is " << std::numeric_limits<short>::min() << "\n";

  std::cout << "the max of int is " << std::numeric_limits<int>::max() << "\n";
  std::cout << "the min of int is " << std::numeric_limits<int>::min() << "\n";

  std::cout << "the max of unsigned int is " << std::numeric_limits<unsigned int>::max() << "\n";
  std::cout << "the min of unsigned int is " << std::numeric_limits<unsigned int>::min() << "\n";

  std::cout << "the max of long is " << std::numeric_limits<long>::max() << "\n";
  std::cout << "the min of long is " << std::numeric_limits<long>::min() << "\n";

  std::cout << "the max of unsigned long is " << std::numeric_limits<unsigned long>::max() << "\n";
  std::cout << "the min of unsigned long is " << std::numeric_limits<unsigned long>::min() << "\n";

  std::cout << "the max of long long is " << std::numeric_limits<long long>::max() << "\n";
  std::cout << "the min of long long is " << std::numeric_limits<long long>::min() << "\n";
}
```