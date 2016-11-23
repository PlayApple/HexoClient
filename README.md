#### 安装brew
```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

#### 安装node.js, 会同时安装npm
```
$ brew install node
```

安装hexo客户端
```
$ npm install -g hexo-cli
```

#### 建站
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```

#### Hexo指令
https://hexo.io/zh-cn/docs/commands.html


安装模板maupassant-hexo
https://github.com/tufu9441/maupassant-hexo
```
$ git clone https://github.com/tufu9441/maupassant-hexo.git themes/maupassant
$ npm install hexo-renderer-jade --save
$ npm install hexo-renderer-sass --save
```



