### 参考命令

```bat
cd {Hbuilder安装目录}\plugins\uniapp-cli

set UNI_PLATFORM=h5

set UNI_INPUT_DIR={项目根目录}
set UNI_OUTPUT_DIR={项目根目录}/targetDir  # 目标目录
set NODE_ENV=production  # develoment

node bin/uniapp-cli.js

```

### demo
```bat
cd D:\soft\HBuilderX\plugins\uniapp-cli
set UNI_PLATFORM=h5
set UNI_INPUT_DIR=D:\test\front_ceekeepublic\
set UNI_OUTPUT_DIR=D:\pack\
set NODE_ENV=release
node D:\soft\HBuilderX\plugins\uniapp-cli\bin\uniapp-cli.js 
pause
```