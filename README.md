个人页面: [https://yancai.github.io](https://yancai.github.io)  
此为hexo相关文件仓库


## 工程配置方式

1. 安装nodejs
2. 配置nodejs镜像源

```bash
npm config set registry http://registry.npm.taobao.org/
```

3. 安装yarn

```bash
npm install yarn -g
```

4. 安装hexo-cli

```bash
npm install hexo-cli -g
```

5. clone代码

```bash
git clone git@github.com:yancai/yancai.github.io.git
```

6. 切换分支

```bash
git checkout hexo
```

7. 进入代码目录，安装nodejs依赖包

```bash
yarn
```

8. 安装主题依赖

```bash
git clone https://github.com/theme-next/theme-next-pace themes/next6/source/lib/pace
git clone https://github.com/theme-next/theme-next-fancybox3 themes/next6/source/lib/fancybox
```
