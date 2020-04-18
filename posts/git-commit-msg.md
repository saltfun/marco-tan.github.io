---
title: '规范化 git commit message 并自动生成CHANGELOG'
date: 2020-02-08 22:35:33
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
初始化项目
```shell
npm init -y
git init
```

安装依赖
```shell
npm i husky @commitlint/cli @commitlint/config-conventional conventional-changelog-cli -D
```

创建`commitlint.config.js`文件
```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat', // 新功能（feature）
        'fix', // 修补bug
        'docs', // 文档（documentation）
        'style', // 格式（不影响代码运行的变动）
        'refactor', // 重构（即不是新增功能，也不是修改bug的代码变动）
        'test', // 增加测试
        'chore', // 构建过程或辅助工具的变动
        'revert', // 回滚
      ],
    ],
    'type-empty': [2, 'never'], // 提交不符合规范时,也可以提交,但是会有警告
    'subject-empty': [2, 'never'], // 提交不符合规范时,也可以提交,但是会有警告
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
  },
};
```

修改`package.json`文件
```shell
{
  "name": "test",
  "version": "1.0.0",
  "scripts": {},
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "pre-commit": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0 && git add CHANGELOG.md"
    }
  },
  "devDependencies": {
    "@commitlint/cli": "^8.3.5",
    "@commitlint/config-conventional": "^8.3.4",
    "conventional-changelog-cli": "^2.0.31",
    "husky": "^4.2.1"
  }
}
```

问题：
* 必须先git init，再安装husky
* 最新的commit不会出现在CHANGELOG.md里，但在生成CHANGELOG.md时能在`.git/COMMIT_EDITMSG`里读到commit message信息


