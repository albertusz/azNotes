installation in c:\skia
install depot_tools

open cmd prompt
set GIT_EXECUTABLE=c:\skia\depot_tools\git.bat
cd c:\skia\skia
python tools/git-sync-deps 

gn gen out/release -args="is_official_build=true is_component_build=false"

gn gen out/release -args="is_debug=false is_official_build=true skia_use_system_expat=false skia_use_system_libjpeg_turbo=false skia_use_system_libpng=false skia_use_system_libwebp=false skia_use_system_zlib=false skia_use_system_icu=false skia_use_system_harfbuzz=false is_component_build=false"

ninja -C out/release


# note from https://news.ycombinator.com/item?id=19586159

This will build it as a static library. I believe by default it will try to use system libraries, you can make it use its own version of those libraries in this slightly more complicated process (which could be done with a script but I'm too lazy to write one right now):

    $ gn args out/release --list --short

This will print all the current build arguments, copy all the use_system_XXX = true, you'll have to modify those to be = false, and paste them into the text editor that will open once you run:

    $ gn args out/release

After that, run ninja to build

    $ ninja -C out/release

For the include files, you have to add at least these to your include directory:

    include
    include/config
    include/core
    include/effects
    include/gpu

On Windows, you might want to have two builds linking against different versions of the std lib (because of the whole iterator debug level stuff). Add these args: extra_cflags=["/MTd"] and extra_cflags=["/MT"] for debug/release builds (statically linked in this case).

