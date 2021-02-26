now I have another problem<br>
let me explain a bit before showing code:<br>
`Component` is a class with 1 member, `bool enabled`<br>
`Script` is a class inherited from `Component`, with 2 added functions: `void Frame()` and `void Begin()`<br>
`GameObject` is a class with 3 members, `bool enabled`, `std::wstring name` and `std::vector<Component *> components;`, and 1 function, `void addComponent(Component *)`<br>

now, I have a script, `MainScript`, which is inherited from `Script`
it overrides `void Frame()` by making it print "e"

the problem is, it doesn't print "e"

`Game.h`
```cpp
#pragma once
#include <string>
#include <vector>



#include "GameObject.h"
#include "GameType.h"
#include "InputType.h"

class Game
{
public:
    // end-user functions
    Game(std::wstring name, GameType type);
    void addGameObject(GameObject *gameObject);
    void run();

    // internal functions
    Game();
    void ReceiveInput(InputType type, int code, int repeatCount, int scanCode);
    
    // internal variables
    std::wstring name;
    GameType type;
    std::vector<GameObject *> gameObjects;
};
```

`Game.cpp`
```cpp
#include "Game.h"

#include <iostream>

#include <Windows.h>

#include "Component/Script.h"

Game::Game(std::wstring name, GameType type)
{
    this->name = name;
    this->type = type;
}

Game::Game() {}

void Game::ReceiveInput(InputType type, int code, int repeatCount, int scanCode)
{
    if (type == InputType::KeyPress || type == InputType::Character)
    {
        code = VkKeyScan(code);
    }
    
    std::cout << (int)type << ": " << (char)MapVirtualKey(code, MAPVK_VK_TO_CHAR) << '\n';
}

void Game::run()
{
    while (true)
    {
        for (int i = 0; i < gameObjects.size(); i++)
        {
            GameObject *gameObject = gameObjects[i];
            if (gameObject != nullptr)
            {
                for (int j = 0; j < gameObject->components.size(); j++)
                {
                    Component *component = gameObject->components[j];
                    if (component != nullptr)
                    {
                        if (typeid(component) == typeid(Script))
                        {
                            ((Script *)component)->Frame();
                        }
                    }
                }
            }
        }
    }
}

void Game::addGameObject(GameObject *gameObject)
{
    gameObjects.push_back(gameObject);
}
```

`GameObject.h`
```cpp
#pragma once
#include <string>
#include <vector>

#include "Component/Component.h"

class GameObject
{
public:
    std::wstring name;
    bool enabled;
    std::vector<Component *> components;

    void addComponent(Component *component);
};
```

`GameObject.cpp`
```cpp
#include "GameObject.h"

void GameObject::addComponent(Component *component)
{
    components.push_back(component);
}
```

`Component.h`
```cpp
#pragma once

class Component
{
public:
    bool enabled;
};
```

`Script.h`
```cpp
#pragma once
#include "Component.h"

class Script : public Component
{
public:
    void Frame() {}
    void Begin() {}
};
```

`MainScript.h`
```cpp
#pragma once

#include "../Headers/Component/Script.h"

class MainScript : public Script
{
public:
    void Frame();
};
```

`MainScript.cpp`
```cpp
#include "MainScript.h"

#include <iostream>

void MainScript::Frame()
{
    std::cout << "e" << '\n';
}
```

`TestGame.cpp`
```cpp
#include <iostream>


#include "MainScript.h"
#include "../Headers/Window.h"
#include "../Headers/GameObject.h"
#include "../Headers/Component/Script.h"

int main(int argc, char* argv[])
{
    Window window(L"Some Window", true);

    Game game(L"Test Game", GameType::TwoDimensions);
    window.game = &game;

    GameObject gameObject;
    gameObject.name = L"Test GameObject";

    MainScript mainScript;
    
    gameObject.addComponent(&mainScript);
    
    game.run();
    return 0;
}
```
