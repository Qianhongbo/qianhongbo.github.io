---
title: lambda expression in C++
date: 2022-02-26 16:22:27
categories: 
- c++
tags: 
- c++
---

There are three ways to call the function.

- Just call
- Function pointer
- Lambda expression

## Just call

```c++
#include <iostream>
#include <vector>

size_t foo(size_t aValue) {
    return rand() % 5;
}

struct Container {
    Container() {
        values.push_back(10);
        values.push_back(20);
        values.push_back(30);
        values.push_back(40);
        values.push_back(50);
    }
    
    void eachFrom(size_t anIndex) {
        while ((anIndex = foo(values[anIndex]))) {
            std::cout << values[anIndex] << "\n";
        }
    }

    std::vector<int> values;
};

int main() {
    srand((unsigned)time(NULL));
    Container theContainer;
    theContainer.eachFrom(1);
    return 0;
}
```

## Function pointer

```c++
#include <iostream>
#include <vector>

struct Container {
    Container() {
        values.push_back(10);
        values.push_back(20);
        values.push_back(30);
        values.push_back(40);
        values.push_back(50);
    }

    using EachVisitor = std::function<size_t(size_t)>;
    void eachFrom(size_t anIndex, EachVisitor aVisitor) {
        while ((anIndex = aVisitor(values[anIndex]))) {
            std::cout << values[anIndex] << "\n";
        }
    }

    std::vector<int> values;
};

size_t foo(size_t aValue) {
    return rand() % 5;
}

int main() {
    srand((unsigned)time(NULL));
    Container theContainer;
    theContainer.eachFrom(1, foo);
    return 0;
}
```

## Lambda expression

```c++
#include <iostream>
#include <vector>

struct Container {
    Container() {
        values.push_back(10);
        values.push_back(20);
        values.push_back(30);
        values.push_back(40);
        values.push_back(50);
    }

    using EachVisitor = std::function<size_t(size_t)>;
    size_t eachFrom(size_t anIndex, EachVisitor aVisitor) {
        while((anIndex = aVisitor(values[anIndex]))) {
            std::cout << "value" << values[anIndex] << "\n";
        }
        return 0;
    }

    std::vector<int> values;
};

int main() {
    srand((unsigned)time(NULL)); 
    Container theContainer;
    theContainer.eachFrom(1, [&](size_t aValue) {return rand() % 5;});
    return 0;
}
```

