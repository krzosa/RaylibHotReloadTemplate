## Info

Template for a game/game engine (GCC compiler / MSVC compiler)(64 bit)

* Sets up hot reload, so we can recompile the code while the application is running and automatically apply the changes without losing game state, very convenient for games and doesn't impact performance. 
* Sets up raylib (which is an amazing library for developing games)so that the library is as easy to modify as possible, it's very easy to switch versions and you dont have to download it separately.
* Sets up compiling scripts for msvc compiler (compiler from windows build tools / visual studio) and gcc compiler packaged along with raylib.

## How to run:

You need either the gcc compiler (which is packaged with raylib installation) or the msvc compiler (which you can find how to setup here https://hero.handmade.network/episode/code/day001/) the compiler needs to be on your system path so that the system can find it.

1. After you get the compiler, run the build-raylib script of your choice, which is going to create a dynamic library of raylib, that library is going to get copied into the bin folder. 
2. Call the build script of your choice.
3. Call the run script or execute the binary manually.

## Structure

* executable_main.c -> Loads the dynamic library(hotloaded_main.c) and calls Update from it in a loop. On every iteration checks if the dynamic library changed, if it did then it swaps the code 
* hotloaded_main.c -> Gamecode(dynamic library), this is where you actually write the engine, game etc. It has Update function which is called on every frame, it also has Initialize function which is called on program start, HotReload function which is called when you recompile the program while its running and the code gets swapped
* shared.h -> code shared between the executable and the dynamic library 

## How it works

We have three entities here: 
1. the executable
2. the dynamic library of our thing
3. the dynamic(shared) library of raylib

and a dynamic (or shared) library is a library that can by loaded at runtime of a program, so the basic idea of "hot loading" is to have the executable as the base, that holds everything together, this is the place that performs the program loop and all the data in the program belongs to the executable. The dynamic library holds the main code that's going to be executed and that code is loaded while the application is running using windows calls "LoadLibrary" and "GetProcAddress". Then the application is checking on every frame when was the dynamic library updated and if the value of that check diverges in any way from the value that we stored then we perform the code reload.

The raylib shared library on the other hand is linked using a .lib/.a file at compile time, it's not loaded while the application is running. But it still has the properties of a dynamic or a "shared" library meaning that it has it's own internal state that it shares with the executable thanks to that we can easily use that library without losing any state when things get recompiled from our hotloaded dynamic library because we have all the function addresses through linking the .lib file. amazin

## Source of the idea

https://hero.handmade.network/episode/code/day021/