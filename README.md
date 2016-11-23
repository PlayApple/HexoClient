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


#### new
```
$ hexo new [layout] <title>
```
新建一篇文章。如果没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数代替。如果标题包含空格的话，请使用引号括起来。

#### generate
```
$ hexo generate
```
生成静态文件。

选项	描述
-d, --deploy	文件生成后立即部署网站
-w, --watch	监视文件变动

#### publish
```
$ hexo publish [layout] <filename>
```
发表草稿。

#### server
```
$ hexo server
```
启动服务器。默认情况下，访问网址为： http://localhost:4000/。

选项	描述
-p, --port	重设端口
-s, --static	只使用静态文件
-l, --log	启动日记记录，使用覆盖记录格式

#### deploy
```
$ hexo deploy
```
部署网站。

参数	描述
-g, --generate	部署之前预先生成静态文件

#### render
```
$ hexo render <file1> [file2] ...
```
渲染文件。

参数	描述
-o, --output	设置输出路径

#### migrate
```
$ hexo migrate <type>
```
从其他博客系统 迁移内容。

#### clean
```
$ hexo clean
```
清除缓存文件 (db.json) 和已生成的静态文件 (public)。

#### list
```
$ hexo list <type>
```
列出网站资料。

#### version
```
$ hexo version
```
显示 Hexo 版本。

#### 选项
安全模式
```
$ hexo --safe
```
在安全模式下，不会载入插件和脚本。当您在安装新插件遭遇问题时，可以尝试以安全模式重新执行。

调试模式
```
$ hexo --debug
```
在终端中显示调试信息并记录到 debug.log。当您碰到问题时，可以尝试用调试模式重新执行一次，并 提交调试信息到 GitHub。

简洁模式
```
$ hexo --silent
```
隐藏终端信息。

自定义配置文件的路径
```
$ hexo --config custom.yml
```
自定义配置文件的路径，执行后将不再使用 _config.yml。

显示草稿
```
$ hexo --draft
```
显示 source/_drafts 文件夹中的草稿文章。

自定义 CWD
```
$ hexo --cwd /path/to/cwd
```
自定义当前工作目录（Current working directory）的路径。

