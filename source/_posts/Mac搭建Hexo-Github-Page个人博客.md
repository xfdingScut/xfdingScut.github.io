---
title: Mac搭建Hexo+Github Page个人博客
date: 2018-09-06 16:40:40
tags:
---

# 安装Homebrew

## 安装命令

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## brew命令

```bash
brew update # 更新brew包

brew install 软件包 # 安装软件
brew uninstall 软件包 # 卸载软件
brew upgrade 软件包 # 更新软件
brew cleanup 软件包 # 清理软件历史版本

brew cleanup # 清理所有软件历史版本
brew upgrade # 更新所有软件
```

# 安装Node.js

```bash
brew install node
```

# 安装Git

```bash
brew install git
```

# 安装Hexo

```bash
npm install -g hexo-cli
```

# 部署步骤

1. 进入本地文件夹

   ```bash
   hexo new "文档名"
   ```

2. 编辑文档

3. 上传本地代码（在根目录）

   ```bash
   git add .
   git commit -m "提示语句"
   git push origin hexo
   ```

4. 部署hexo

   ```bash
   # 若部署
   hexo clean 
   hexo g -d
   
   # 若预览
   hexo clean
   hexo s
   ```
