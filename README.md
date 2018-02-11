# My HotSpot project for Eclipse

## For the impatient 

Copy this repository into your HotSpot source tree (i.e. `<OpenJDK>/src/hotspot`):

```
wget https://github.com/simonis/HotSpot_Eclipse_Project/archive/master.zip
unzip master.zip
```

Import the project into Eclipse:

```
File -> Import -> General -> Existing Projects into Workspace -> un-click "Copy project into workspace"
```

Adjust the output directory in the project to match your local build output directry (this is necessary in order to index the generated source files):

```
Project (jdk-hs) -> <right-click> -> Properties -> Resource -> Linked Resources -> (tab) Path Variables -> (select) OUTPUT_ROOT -> Edit
```

Set `OUTPUT_ROOT` to the OpenJDK build root directory (i.e. the directory from which you've called configure or `<OpenJDK>/build/linux-x86_64-normal-server-{release|fastdebug|slowdebug}` if you've called configure right from the source directory).   

Enjoy :)

## For real developers

Clone this repository right into your hostspot source tree. This can't be done directly, because the hotspot repository obviously already contains files. Here's how it works. First we clone this repository into an arbitrary location (called `${GIT_ROOT}` in this example):

```
git clone https://github.com/simonis/HotSpot_Eclipse_Project.git ${GIT_ROOT}
```

Now change into the HotSpot root directory `src/hotspot` of your repository (called `${HOTSPOT_ROOT}` in this example) and copy the project there:

```
cd ${HOTSPOT_ROOT}
cp -r ${GIT_ROOT}/HotSpot_Eclipse_Project/.git .
git reset --hard HEAD
git pull
```
Alternatively, if you don't plan to apply any changes to the Eclipse project which you want to commit, you could simply copy the project files from this repositroy into your source tree like this:

```
cp ${GIT_ROOT}/.project ${HOTSPOT_ROOT}/
cp ${GIT_ROOT}/.cproject ${HOTSPOT_ROOT}/
cp -r ${GIT_ROOT}/.settings ${HOTSPOT_ROOT}/
```

If you've already imported this project before, you may want to edit/change its name:

```
vi .project 
```

Now you can import the project into Eclipse:

```
File -> Import -> General -> Existing Projects into Workspace -> un-click "Copy project into workspace"
```

If you've decided to clone the whole repositroy into your HotSpot source tree and have the Git-plugin installed, Eclipse will detect this Ecplicse project (the one we've just copied) and put small question marks on all your HotSpot source files and directories because they aren't tracked by Git. To fix this, disable Git for your new `jdk-hs` HotSpot project:

```
Project (jdk-hs) -> <right-click> -> Team -> Disconnect
```

Finally, we have to adjust the output directory in the project to match your local build output directry (this is necessary in order to index the generated hotspot source files):

```
Project (jdk-hs) -> <right-click> -> Properties -> Resource -> Linked Resources -> (tab) Path Variables -> (select) OUTPUT_ROOT -> Edit
```

Set `OUTPUT_ROOT` to the OpenJDK build root directory (i.e. the one from which you've called configure or the one which contains the `images/jdk` build results). This directory should contain the generated hotspot sources under `hotspot/variant-server/gensrc`. If the name of this directory should change, you can adjust it in the `Linked Resources` tab.

Finally, you can select an active build configuration (i.e. `linux-amd64-dbg`) from:

```
Project (jdk-hs) -> Build configurations -> Set active
```

For some unknown reason, the Eclipse CDT Indexer sometimes forgets your active build configuration. The following bug tracks this issue: https://bugs.eclipse.org/bugs/show_bug.cgi?id=205299. If you want to change your configuration frequently, the following setup is recommanded:

```
Window -> Preferences -> C/C++ -> Indexer -> Build configuration for the indexer -> (choose) "Use active build configuration"
Project (jdk-hs) -> <right-click> -> Properties -> C/C++ General -> Code Analysis -> Indexer -> Build configuration for the indexer -> (check) "Use active build configration" and "Reindex project on change of active build configuration"
```
## Bugs

There's a long standing issue with the Eclipse CDT indexer which can't properly handle non-toplevel includes (i.e. including parts of a class defintion from another file). HotSpot heavily uses this uncommen programming style in order to specialize classes for different platforms/architectures. See for example:

http://hg.openjdk.java.net/jdk/jdk/file/tip/src/hotspot/share/runtime/osThread.hpp

which includes (on Linux):

http://hg.openjdk.java.net/jdk/jdk/file/tip/src/hotspot/os/linux/osThread_linux.hpp

like this:

```
class OSThread: public CHeapObj<mtThread> {
  ...
  // Platform dependent stuff
  #include OS_HEADER(osThread)
  ...
};
```

`OS_HEADER` is a macro which expands to one of:

```
osThread_linux.hpp
osThread_win.hpp
osThread_bsd.hpp
osThread_aix.hpp
```

depending on the current platform. 

If you want, you can vote for this Eclipse CDT issue at https://bugs.eclipse.org/bugs/show_bug.cgi?id=315964
