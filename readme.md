## configure makefile
```
cmake -S .  -B build/
```
## build the project
```
make -C build/
```
## run the project
```
./build/${PROJECT_NAME}
```
## VERSION
```
.
├── build/
│     └── ${PROJECT_NAME}Config.h #auto generate
│        ├── #define PROJECT_NAME_VERSION_MAJOR 1
│        └── #define PROJECT_NAME_VERSION_MINOR 0
├── CMakeLists.txt
│        ├── project(${PROJECT_NAME} VERSION 1.0)
│        ├── add_executable(${PROJECT_NAME} main.cpp)
│        └── configure_file(PROJECT_NAMEConfig.h.in PROJECT_NAMEConfig.h)
├── PROJECT_NAMEConfig.h.in
│        ├── #define PROJECT_NAME_VERSION_MAJOR @PROJECT_NAME_VERSION_MAJOR@
│        └── #define PROJECT_NAME_VERSION_MINOR @PROJECT_NAME_VERSION_MINOR@
│
└── main.cpp
```

## Simple way
```
.
├── build/
├── CMakeLists.txt
│        ├── project(${PROJECT_NAME} VERSION 1.0)
│        └── add_executable(${PROJECT_NAME} main.cpp [.cpp])
└── main.cpp
        └── ## don't need to include [.cpp] because cmake execute for us

## Library
.
├── LIBRARY_FODLER/
│   ├── [.cpp]
│   ├── [.h]
│   └── CMakeLists.txt
│           └── add_library(LIBRARY_NAME [.cpp] [.h])
├── build/
├── CMakeLists.txt
│        ├── project(${PROJECT_NAME} VERSION 1.0)
│        ├── add_executable(${PROJECT_NAME} main.cpp)
│        ├── add_subdirectory(LIBRARY_FODLER)
│        ├── target_include_directories(${PROJECT_NAME} PUBLIC LIBRARY_FODLER)
│        ├── target_link_directories(${PROJECT_NAME} PRIVATE LIBRARY_FODLER)
│        └── target_link_libraries(${PROJECT_NAME} LIBRARY_NAME)
└── main.cpp
         ├── #include "[.h]"
         └── or #include "LIBRARY_FODLER/[.h]" w/o target_include_directories(...)
``` 

## Optional Library
```
cmake -DUSE_ADDER=ON -S . -B out/build
```
```
.
├── LIBRARY_FODLER/
│   ├── [.cpp]
│   ├── [.h]
│   └── CMakeLists.txt
│           └── add_library(LIBRARY_NAME [.cpp] [.h])
├── build/
├── CMakeLists.txt
│        ├── project(${PROJECT_NAME} VERSION 1.0)
│        ├── option(USE_ADDER "simple explanation" ON)
│        ├── if (USE_ADDER)
│        │    │  add_subdirectory(Adder)
│        │    │  list(APPEND EXTRA_LIB_DIRS "Adder")
│        │    │  list(APPEND EXTRA_INCLUDES_DIRS "Adder")
│        │    │  list(APPEND EXTRA_LINKS adder)
│        │    endif()
│        │
│        ├── add_executable(${PROJECT_NAME} main.cpp)
│        ├── configure_file(PROJECT_NAMEConfig.h.in PROJECT_NAMEConfig.h)
│        ├── add_executable(${PROJECT_NAME} main.cpp)
│        ├── add_subdirectory(LIBRARY_FODLER)
│        ├── target_include_directories(${PROJECT_NAME} ${EXTRA_INCLUDES_DIRS})
│        ├── target_link_directories(${PROJECT_NAME} ${EXTRA_LIB_DIRS})
│        └── target_link_libraries(${PROJECT_NAME} ${EXTRA_LINKS})
│
├── ${PROJECT_NAME}Config.h.in
│        ├── #define PROJECT_NAME_VERSION_MAJOR @PROJECT_NAME_VERSION_MAJOR@
│        ├── #define PROJECT_NAME_VERSION_MINOR @PROJECT_NAME_VERSION_MINOR@
│        └── #cmakedefine USE_ADDER
│
└── main.cpp
         ├── #ifdef USE_ADDER
         │    │   #include <adder.h>
         │    #endif
         │
         └── #ifdef USE_ADDER
              │   std::cout << add(72.1f, 0.0f) << std::endl;
             #endif
```
## Install with CPack
```
.
├── License.txt
│
└── CMakeLists.txt
         ├── include(InstallRequiredSystemLibraries)
         ├── set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
         ├── set(CPACK_PACKAGE_VERSION_MAJOR "${OLAS_VERSION_MAJOR}")
         ├── set(CPACK_PACKAGE_VERSION_MINOR "${OLAS_VERSION_MINOR}")
         └── include(CPack)

```
## Find Library
```
.
├── License.txt
│
└── CMakeLists.txt
         ├── find_library(CUSTOMIZED_NAME ACTUAL_LIBRARY)
         └── if (CUSTOMIZED_NAME STREQUAL CUSTOMIZED_NAME-NOTFOUND)
              │  message(FATAL_ERROR "...")
             else()
              │  message(STATUS "...")
             endif()
```