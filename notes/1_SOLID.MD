# SOLID principles

## Single responsibility principle

Each classs/struct should have only 1 responsibility. 
Ex. If a struct has a task to log the jounral. No other fn is added to it. It is best to put persistent fn to be in a second class where multiple classes can reuse it. Also easy to modify. 

## Open closed principle

The classes must be open to extension but closed for modification. It means that the already tested code must not be modified to prevent disasters. Instead structure the code to extend the class fn without modifiying the existing code. 

Example - Use abstract fn to create interface. Create multiple classes for each super feature class and then implement the common fns within it. 

## Liskov substitution principle

The superclass should be correctly defined such as that it defines the characterstics of all the subclasses. 

Example (see the code) - Having rect as a superclass of square is not correct as the area formula is different. Instead use shape as superclass, or as in the code create a factory. 

## Interface segreagation principle

If a class (A) has many interface functions, it becomes cumbersome when you create a new class that inherits from A but only needs to implement a few of those functions. The remaining interface methods must either be left empty or throw a "not implemented" error. There's no reason to include these empty or non-functional methods in the final release.

```
#include <iostream>
using namespace std;

// Too big interface
class IMachine {
public:
    virtual void print() = 0;
    virtual void scan() = 0;
    virtual void fax() = 0;
    virtual ~IMachine() = default;
};

// OldPrinter only supports printing, but is forced to implement scan & fax too!
class OldPrinter : public IMachine {
public:
    void print() override { cout << "Printing...\n"; }
    void scan() override { cout << "Scan not supported!\n"; }
    void fax() override { cout << "Fax not supported!\n"; }
};
```

The solution is to split the large interface into multiple, smaller ones. This allows a new class to inherit only the specific interfaces it needs, preventing it from having to implement unnecessary functions. This approach keeps classes lean and avoids the burden of carrying unneeded methods.

```
#include <iostream>
using namespace std;

// Smaller, focused interfaces
class IPrinter {
public:
    virtual void print() = 0;
    virtual ~IPrinter() = default;
};

class IScanner {
public:
    virtual void scan() = 0;
    virtual ~IScanner() = default;
};

class IFax {
public:
    virtual void fax() = 0;
    virtual ~IFax() = default;
};

// OldPrinter only implements what it supports
class OldPrinter : public IPrinter {
public:
    void print() override { cout << "Printing...\n"; }
};

// MultiFunctionPrinter can implement multiple interfaces
class MultiFunctionPrinter : public IPrinter, public IScanner, public IFax {
public:
    void print() override { cout << "Printing...\n"; }
    void scan() override { cout << "Scanning...\n"; }
    void fax() override { cout << "Faxing...\n"; }
};
```

## Dependency inversion principle

A high level entity should not depend on low level modules. Instead it should depend through abstraction. 

The problem with the below code is that switch directly depends on lightbuld. When a new device, fan is added the switch class needs to be modified and may break the previous functionality. 

```
#include <iostream>
using namespace std;

class LightBulb {
public:
    void turnOn() { cout << "LightBulb ON\n"; }
    void turnOff() { cout << "LightBulb OFF\n"; }
};

class Switch {
    LightBulb& bulb; // Switch depends directly on LightBulb
public:
    Switch(LightBulb& b) : bulb(b) {}
    void operate(bool on) {
        if (on) bulb.turnOn();
        else bulb.turnOff();
    }
};

int main() {
    LightBulb bulb;
    Switch sw(bulb);
    sw.operate(true);
    sw.operate(false);
}
```

The solution is to define an interface (or abstract class) that all devices, such as LightBulb and Fan, will implement. This interface is then passed to the Switch class. As a result, whenever we add a new device, we only need to create a new class that implements the interface, without modifying the Switch class.

```
#include <iostream>
using namespace std;

// Abstraction
class ISwitchable {
public:
    virtual void turnOn() = 0;
    virtual void turnOff() = 0;
    virtual ~ISwitchable() = default;
};

// Low-level module 1
class LightBulb : public ISwitchable {
public:
    void turnOn() override { cout << "LightBulb ON\n"; }
    void turnOff() override { cout << "LightBulb OFF\n"; }
};

// Low-level module 2
class Fan : public ISwitchable {
public:
    void turnOn() override { cout << "Fan ON\n"; }
    void turnOff() override { cout << "Fan OFF\n"; }
};

// High-level module depends on abstraction, not concrete class
class Switch {
    ISwitchable& device;
public:
    Switch(ISwitchable& d) : device(d) {}
    void operate(bool on) {
        if (on) device.turnOn();
        else device.turnOff();
    }
};

int main() {
    LightBulb bulb;
    Fan fan;

    Switch sw1(bulb);
    Switch sw2(fan);

    sw1.operate(true);
    sw1.operate(false);

    sw2.operate(true);
    sw2.operate(false);
}
```




 
