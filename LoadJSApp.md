# React-Native加载本地文件
## 创建配置

-配置项目架构
系统环境安装react-native
执行npm install react-native -g
构建react-native项目环境
执行react-native init [projectName]

## 发布安装
-编译JS文件
cd到项目目录下
执行react-native start
在android/app/src/main中创建assets文件夹
再开个命令行窗口运行下列命令
```base
curl "http://localhost:8081/index.android.bundle?platform=android" -dev=false -o "android/app/src/main/assets/index.android.bundle"
```
运行react-native run-android安装
