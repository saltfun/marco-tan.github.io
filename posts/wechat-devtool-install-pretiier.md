---
title: '微信开发者工具安装Prettier格式化插件'
date: 2020-03-06 15:46:26
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
* 先在VSCode中安装Prettier
* 打开`~/.vscode/extensions`目录，找到prettier的拓展文件夹
* 打开微信开发者工具，选择菜单 「编辑」→「打开编辑器拓展目录」，将prettier的拓展文件夹复制到此目录
* 选择「编辑」→「管理编辑器拓展...」，添加 "esbenp.prettier-vscode" （格式：package.json中的 \${publisher}.\${name}）
* 在开发者工具任意文件中右键，选择「格式化文档，方法是使用...」，选择Prettier
* 在小程序项目中创建`.prettierrc`文件

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5"
}
```

参考：
* [开发者工具功能更新](https://developers.weixin.qq.com/community/develop/doc/000c8e132c47c8e19b99a4aa85b001)
* [Prettier](https://prettier.io/)
