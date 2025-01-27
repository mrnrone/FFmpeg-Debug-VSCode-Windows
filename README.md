# FFmpeg-Debug-VSCode-Windows

This guide is a modern version of [neptune46/ffmpeg-vscode](https://github.com/neptune46/ffmpeg-vscode) written four years ago. You can read it but I recommend use these steps: 

## Preparation for MINGW64 build

### 1. Download MSYS2

Just follow [the official installation guide](https://www.msys2.org/#installation).

### 2. Install tools and packages for building FFmpeg

These commands must be ran on **mingw64** prompt.

Press 'Y' whenever asked.

```bash
pacman -Syu        // <-- when finished it will close the terminal! Reopen it and continue with the same command once again! 
pacman -Syu
pacman -S git 
pacman -S make diffutils nasm yasm
pacman -S mingw-w64-x86_64-gcc
pacman -S mingw-w64-i686-gcc
pacman -S mingw-w64-x86_64-gdb 
pacman -S git nasm yasm
```
## Clone & Build FFmpeg

These commands must be ran on **mingw64** prompt.

```bash
cd C:\msys64
./mingw64.exe
cd ~
git clone https://github.com/FFmpeg/FFmpeg.git
```
Download and copy SDL2-2.30.11 next to FFMpeg directory! (it's required for ffplay)

```bash
cd FFmpeg
 ./configure --enable-debug --enable-static --disable-optimizations --enable-ffplay --extra-cflags="-I/home/{user_name}/SDL2-2.30.11/include" --extra-ldflags="-L/home/{user_name}SDL2-2.30.11/lib"
make -j8
```

After build, `ffmpeg_g.exe` will be genereated in `~/FFmpeg`. Congratulation!

## Debug FFmpeg on VSCode!

Before start debugging in VSCode make sure mingw-w64-x86_64-gdb is installed (see launch.json "miDebuggerPath")

If you downloaded MSYS2 following official guide, your FFmpeg project may be located in `C:\msys64\home\{user_name}\FFmpeg`.

These commands must be ran on **Commpand Prompt of Windows not MSYS2**.

```bash
cd C:\msys64\home\{user_name}\FFmpeg
mkdir .vscode
cd .vscode
type NUL > launch.json
code .
```

Install [C/C++ Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) on your VSCode.

Set `.vscode/launch.json`.
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "gdb",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/ffmpeg_g.exe",
            "args": [""], //define your args!
            "stopAtEntry": true,
            "cwd": "${workspaceFolder}",
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "C:/msys64/mingw64/bin/gdb.exe"
        }
    ]
}
```

Press F5!
The next step is to define your own `"args"` in `.vscode/launch.json` file.

# Preparation for MSVC build

1. Follow the same steps 1. and 2. defined for ming64 build above. If you've already done it just continue. 
2. Make sure Visual Studio 2017 (or higher) is installed
3. Go to C:\msys64\usr\bin and rename 'link.exe' to something else like 'link.exe.bak'
4. Start 'x64 Nativ tools Command prompt for VS2017' and navigate to 'C:\msys64' directory. In this directory run 'msys2_shell.cmd -msys2 -use-full-path'
5. Be sure that 'pkg-config' is installed via pacman ('pacman -S pkg-config')
6. Navigate to you FFmpeg source directory and run the following command:
   ./configure --toolchain=msvc --enable-debug --enable-static --disable-optimizations --enable-ffplay --extra-cflags="-I/home/{user_name}/SDL2-2.30.11/include" --extra-ldflags="-L/home/{user_name}/SDL2-2.30.11/lib"
7. make -j8
   It will generate binary files and PDB files which are usually required for debugging.

## Debug FFmpeg (built with MSVC) on VSCode!

If you downloaded MSYS2 following official guide, your FFmpeg project may be located in `c:\msys64\home\{user_name}\FFmpeg`.

These commands must be ran on **Commpand Prompt of Windows not MSYS2**.

```bash
cd C:\msys64\home\{user_name}\FFmpeg
mkdir .vscode
cd .vscode
type NUL > launch.json
code .
```

Install [C/C++ Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) on your VSCode.

Set `.vscode/launch.json`.
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "VS Debugger",
            "type": "cppvsdbg",
            "request": "launch",
            "program": "${workspaceFolder}/ffprobe_g.exe",
            "args": [""], //define your args!
            "stopAtEntry": true,
            "cwd": "${workspaceFolder}",
            "externalConsole": true,
        }
    ]
}
```
Press F5!
The next step is to define your own `"args"` in `.vscode/launch.json` file.   
