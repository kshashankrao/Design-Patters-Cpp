# Factory 

## Introduction
Creating objects directly with constructors can sometimes be limiting:
1. You might want more descriptive creation methods.
2. You might want to encapsulate complex creation logic.
3. You might want flexibility to switch object types or creation methods.

Factory patterns solve these problems by delegating object creation to separate methods, classes, or functions.

Example: Point

```
#include <cmath>
#include <iostream>

enum class PointType { cartesian, polar };

class Point
{
    float x, y;

    // Private constructor: forces usage of factories
    Point(float x, float y)
    {
        this->x = x;
        this->y = y;
    }

public:
    void print() const
    {
        std::cout << "(" << x << ", " << y << ")\n";
    }

    // Declare PointFactory as friend to access private constructor
    friend class PointFactory;
};
```

## Factory

### Factory Method

Definition -  A factory method is a static method that creates objects. It is used when object creation requires more than just calling a constructor.

1. Simplifies constructors.
2. Provides meaningful method names (NewCartesian, NewPolar).
3. Encapsulates creation logic.

Example: 

```
class PointFactory
{
public:
    static Point NewCartesian(float x, float y)
    {
        return Point{x, y};
    }

    static Point NewPolar(float r, float theta)
    {
        return Point{r * cos(theta), r * sin(theta)};
    }
};
```

Usage

```
Point p1 = PointFactory::NewCartesian(3, 4);
Point p2 = PointFactory::NewPolar(5, M_PI / 4);

p1.print(); // (3, 4)
p2.print(); // (3.53553, 3.53553)
```

### Inner Factory
Definition - A nested factory lives inside the class it creates.

1. Keeps the factory inside the class, avoiding a separate global class.
2. Good for small utility factories.
3. Syntax: Point::Factory::NewCartesian.

Example:

```
class Point
{
    float x, y;

    Point(float x, float y)
    {
        this->x = x;
        this->y = y;
    }

public:
    class Factory
    {
    public:
        static Point NewCartesian(float x, float y)
        {
            return Point{x, y};
        }

        static Point NewPolar(float r, float theta)
        {
            return Point{r * cos(theta), r * sin(theta)};
        }
    };

    void print() const
    {
        std::cout << "(" << x << ", " << y << ")\n";
    }
};
```

Usage

```
Point p1 = Point::Factory::NewCartesian(3, 4);
Point p2 = Point::Factory::NewPolar(5, M_PI / 4);
```

### Abstract Factory 
Definition - An abstract factory defines an interface for creating a family of related objects.

1. Lets you swap factories easily (e.g., switch to 3D points).
2. Useful for multiple related products.
3. Encourages interface-driven design.

Example:

```
class AbstractPointFactory
{
public:
    virtual Point NewCartesian(float x, float y) = 0;
    virtual Point NewPolar(float r, float theta) = 0;
};

class Point2DFactory : public AbstractPointFactory
{
public:
    Point NewCartesian(float x, float y) override
    {
        return Point{x, y};
    }

    Point NewPolar(float r, float theta) override
    {
        return Point{r * cos(theta), r * sin(theta)};
    }
};
```

Usage

```
AbstractPointFactory* factory = new Point2DFactory();

Point p1 = factory->NewCartesian(1, 2);
Point p2 = factory->NewPolar(5, M_PI / 4);
```


### Functional Factory 
Definition - A functional factory uses functions or lambdas to create objects.

1. Lightweight and flexible.
2. No extra class needed.
3. Can pass factories as first-class functions.

Example:

```
auto cartesianFactory = [](float x, float y)
{
    return Point{x, y};
};

auto polarFactory = [](float r, float theta)
{
    return Point{r * cos(theta), r * sin(theta)};
};

Point p1 = cartesianFactory(3, 4);
Point p2 = polarFactory(5, M_PI / 4);
```