---
title: Connect different classes in C++
date: 2022-02-19 18:04:57
categories: 
- c++
tags: 
- c++
---

## Problem description

How to connect different classes, but they don't know each other?

## Solution

### IMessenger Interface

```c++
struct IMessenger {
    
    virtual void tell(const std::string &aMsg) = 0;
    virtual void receive(const std::string &aMsg) = 0;
    
    void setOther(IMessenger *anOther) {other=anOther;}
    
    IMessenger* other;
};
```

### Two class

```c++
struct A : public IMessenger {
    A() {}
    
    void tell(const std::string &aMsg) override {
        other->receive(aMsg);
    }
    void receive(const std::string &aMsg) override {
        std::cout << "A got " << aMsg << "\n";
    }
};

struct B : public IMessenger {
    B() {}
    
    void tell(const std::string &aMsg) override {
        other->receive(aMsg);
    }
    void receive(const std::string &aMsg) override {
        std::cout << "B got " << aMsg << "\n";
    }
};
```

### Main

```c++
int main(int argc, const char * argv[]) {
    A theA;
    B theB;
    theA.setOther(&theB);
    theB.setOther(&theA);
    theA.tell("Hello world!");
    theB.tell("Happy everyday!");
    return 0;
}
```

### Result

```c++
// output:
// B got Hello world!
// A got Happy everyday!
```

