```cpp
#pragma once
#include <array>
#include <iterator>
#include <stdexcept>
#include <unordered_map>

template<typename T>
class OptimizedGrid
{
public:
    T defaultValue;
    OptimizedGrid(T defaultVal)
    {
        defaultValue = defaultVal;
    }
    
    T get(int x, int y)
    {
        try
        {
            return map.at(std::array<int, 2>({x, y}));
        } catch (std::out_of_range e)
        {
            return defaultValue;
        }
    }

    void set(int x, int y, T val)
    {
        map[std::array<int, 2>({x, y})] = val;
    }

private:
    std::unordered_map<std::array<int, 2>, T> map;
};
```
```cpp
#include <iostream>

#include "OptimizedGrid.h"

int main(int argc, char* argv[])
{
    OptimizedGrid<bool> *grid = new OptimizedGrid<bool>(false);
    grid->set(4, 0, true);
    grid->set(4, 1, true);
    grid->set(3, 1, true);
    grid->set(3, 2, true);
    grid->set(2, 2, true);
    grid->set(1, 2, true);
    grid->set(0, 2, true);

    std::cout << grid->get(4, 0) << '\n';
    std::cout << grid->get(0, 0) << '\n';
    
    delete grid;
    return 0;
}
```
two weird compile-time errors:
```
xhash(131, 53): [C2064] term does not evaluate to a function taking 1 arguments
xhash(130, 44): [C2056] illegal expression
```
