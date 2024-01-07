## Setting up OpenGL (GLFW and GLAD) on Mac M1 (ARM64)

With the default `brew install glfw` command, you will get GLFW **x86_64** architecture instead of desired **ARM64**, this will cause error when running later.

To make this functional:
1. Install [CMake GUI](https://cmake.org/download/).
2. Download **Source Package** from [the GLFW download page](https://www.glfw.org/download.html) and unzip it.
3. Open CMake app, when asked `where is the source code:`, click **Browse Sources** and **select the folder that is the result from when you unzip the source package from step 2**, click open.
4. On `Where to build the binaries: `, just click browse build and select another location on the PC. (Try not to choose the same folder as source package). Remember this location `/path/to/build`. 
5. Click on the **Configure** button at the bottom.
6. In the big box located in the middle of the app, lists of **Name** and **Value** will appear.
7. Find a row with `CMAKE_OSX_ARCHITECTURES` as the name. [1]
8. Change the value to `arm64`
9. Click **Generate**, the button should be next to the **Configure** button from earlier.
10. Open your terminal and go to where you build the binaries in step 4. example: `cd /path/to/build`
11. Run `make`
12. Now you can use **libglfw3.a** file copied from `/path/to/build/src/libglfw3.a` inside your project's `/lib` folder.
13. For headers file, you can simply return to the [the GLFW download page](https://www.glfw.org/download.html), but this time, select `macOS pre-compiled binaries`
14. Unzip and copy the contents of `/include` into your project's `/include` folder (Should be a single folder folder named `GLFW`, copy that entire folder into your include folder). 
15. Go to [GLAD download page](https://glad.dav1d.de/), and select the following settings.
    * Language: C/C++
    * Specification: OpenGL
    * API
    * gl: Version 3.3 (Do not modify other gles, glsc)
    * Profile: Core
    * Do not modify any extensions or bottom checkboxes
    * Click Generate
16. Download `glad.zip` and unzip.
17. Copy the content inside `/include` to your /include
18. Copy `glad.c` inside `/src` into your source code folder
19. Create a `main.cpp ` where you will do your work.

Your folder might look similar to this:
```
.
├── include
│   ├── GLFW
│   │   ├── glfw3.h
│   │   └── glfw3native.h
│   ├── KHR
│   │   └── khrplatform.h
│   └── glad
│       └── glad.h
├── lib
│   └── libglfw3.a
└── src
    ├── glad.c
    └── main.cpp
```
With 
* `/src` being where the downloaded `glad.c` is copied into along with the `main.cpp` we will be coding in.
* `/lib` being where the file from step 12 is located and copied into.
* `/include` where folders copied from step 14 and 17 are located.

## Running with VSCode

In the root of the directory (where this `README.md` is located), create a folder named `.vscode` with a file named `tasks.json` within. Inside the JSON file, add:

```JSON
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "cppbuild",
			"label": "C/C++: g++ build active file",
			"command": "/usr/bin/g++",
			"args": [
				"-std=c++17",
				"-fdiagnostics-color=always",
				"-g",
				"-I${workspaceFolder}/include",
				"-L${workspaceFolder}/lib",
				"${workspaceFolder}/src/*.cpp",
				"${workspaceFolder}/src/*.c",
				"-lglfw3",
				"-o",
				"${workspaceFolder}/bin/main",
				"-framework",
				"OpenGL",
				"-framework",
				"Cocoa",
				"-framework",
				"IOKit",
				"-framework",
				"CoreVideo",
				"-framework",
				"CoreFoundation",
    			"-Wno-deprecated"
			],
			"options": {
				"cwd": "${fileDirname}"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"detail": "compiler: /usr/bin/g++"
		}
	]
}
```

On the line with `"command": "/usr/bin/g++",` and `"detail": "compiler: /usr/bin/g++"`,
You might need to change the path to the output given by:
`where g++`

## Building and running
1. Open `main.cpp`
2. Use <kbd>Command ⌘</kbd> + <kbd>Shift</kbd> + <kbd>B</kbd>
3. You will notice a folder named `/bin` appearing
4. If you use a terminal and cd to `/bin` and run `./main`, you will see that it works!

### Reference:
1. https://github.com/glfw/glfw/issues/1805

## Addendum:
* [Installing `homebrew`](https://medium.com/mycostech/%E0%B8%A1%E0%B8%B2%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-homebrew-%E0%B8%9A%E0%B8%99-macbook-m1-%E0%B8%AA%E0%B8%B3%E0%B8%AB%E0%B8%A3%E0%B8%B1%E0%B8%9A%E0%B8%8A%E0%B8%B2%E0%B8%A7-dev-%E0%B8%81%E0%B8%B1%E0%B8%99-%E0%B8%89%E0%B8%9A%E0%B8%B1%E0%B8%9A%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88-%E0%B9%80%E0%B8%9E%E0%B8%B4%E0%B9%88%E0%B8%A1%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-node-js-%E0%B8%94%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%89%E0%B8%B2%E0%B8%A2-d33a7444eb53)
* [Installing g++](https://youtu.be/wY24ehH6mC0?si=kmViY_szpgO-53jG)
