`Visual Studio 2019`와 `CMake`를 사용해 C++ 크로스 플랫폼 게임 서버를 만들고 있다

`윈도우10`과 `WSL Ubuntu-20.04`로 작업하고 있는데, 윈도우와 리눅스는 사용하는 라이브러리 파일이 달라 플랫폼을 변경할 때 마다 파일 위치를 수정해 주어야 한다

`CMakeLists.txt`파일을 매번 수정해 파일 위치를 변경해주면 되긴 하지만 이건 상당히 귀찮은 일이다

그래서 MS문서를 찾아보니 `CMakeSetting.json`이라는 파일을 통해 플랫폼 별로 `CMake` 변수를 지정하는 방법이 있었다




```Cmake
# CMakeLists.txt
cmake_minimum_required (VERSION 3.8)

include_directories(${INCLUDE_DIRS})
link_directories(${LIBRARY_DIRS})

add_executable (CMakeTest "main.cpp")
```
위의 `CMakeLists.txt`파일에서 사용되는 변수는 `INCLUDE_DIRS`와 `LIBRARY_DIRS`이다

이를 플랫폼 별로 지정하기 위해서는

```yaml
# CMakeSetting.json
{
  "configurations": [
    {
      "name": "x64-Debug",
      "generator": "Ninja",
      "configurationType": "Debug",
      "inheritEnvironments": [ "msvc_x64_x64" ],
      "buildRoot": "${projectDir}\\out\\build\\${name}",
      "installRoot": "${projectDir}\\out\\install\\${name}",
      "cmakeCommandArgs": "",
      "buildCommandArgs": "",
      "ctestCommandArgs": "",
      "variables": []
    }
  ]
}
```
위와 같이 구성된 `CMakeSetting.json` 파일의 원하는 플랫폼 객체에 `variables` 배열을 수정해 주어야 한다

`INCLUDE_DIRS` 변수를 `D:/git/vcpkg/installed/x64-windows/include`, `LIBRARY_DIRS` 변수를 `D:/git/vcpkg/installed/x64-windows/debug/lib`로 지정한다면

```yaml
"variables": [
    { 
      "name": "INCLUDE_DIRS",
      "value": "D:/git/vcpkg/installed/x64-windows/include",
      "type": "FILEPATH"
    },
    {
      "name": "LIBRARY_DIRS",
      "value": "D:/git/vcpkg/installed/x64-windows/debug/lib",
      "type": "FILEPATH"
    }
]
```
이렇게 된다
`name`에는 변수의 이름, `value`에는 변수의 값, `type`에는 변수의 타입을 넣어주면 된다


`CMake`에 대한 정보는 [CMake-3.8 document],
`CMakeSetting.json`에 대한 정보는[CMakeSettings.json 스키마 참조]에서 가저왔다

[CMake-3.8 document]: https://cmake.org/cmake/help/v3.8/
[CMakeSettings.json 스키마 참조]: https://docs.microsoft.com/ko-kr/cpp/build/cmakesettings-reference?view=vs-2019
