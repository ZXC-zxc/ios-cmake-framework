修改自 https://github.com/leetal/ios-cmake 的example-lib用于支持framework，并使用其ios-tool-chain-file

```bash
mkdir buildIOS & cd buildIOS
cmake .. -G Xcode -DCMAKE_TOOLCHAIN_FILE=../ios-cmake/ios.toolchain.cmake -DPLATFORM=OS64
cmake --build . --config Release  
```