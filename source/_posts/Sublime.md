[TOC]

#### 修改快捷键：Preferences -> KeyBindings

```
[   { "keys": ["command+u"], "command": "upper_case" },
    { "keys": ["command+l"], "command": "lower_case" },
    { "keys": ["ctrl+shift+p"], "command": "show_overlay", "args": {"overlay": "command_palette"} }
]
```

#### 多行操作

```
1、鼠标选中多行，按下 Ctrl Shift L (Command Shift L) ，可同时编辑这些行； 
2、鼠标选中文本，反复按 CTRL D (Command D) ，可继续向下同时选中下一个相同的文本进行同时编辑； 
3、鼠标选中文本，按下 Alt F3 (Win) 或 Ctrl Command G(Mac) ，可一次性选择全部的相同文本进行同时编辑；
4、Shift 鼠标右键 (Win) 或 Option 鼠标左键 (Mac) 或使用鼠标中键，可用鼠标进行竖向多行选择； 
5、Ctrl 鼠标左键(Win) 或 Command 鼠标左键(Mac) ，可手动选择同时要编辑。
```

#### 插件：Preferences -> Packages Setting

##### Package Control

```
Ctrl + `，打开控制台
输入 
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
回车
ctrl+shift+p
输入Package Control:install Package，可安装插件
```

##### PackageResourceViewer
> resouceopen，查看所有Sublime插件源码


##### Git：ctrl+shift+p -> 输入 Git:git指令
##### GitGutter：查看git修改内容
##### Anaconda：python脚本自动提示

```
command+b，运行程序
View -> show Console，可显示控制台输入参数
{
    "python_interpreter": "/Users/songyu/anaconda3/bin/python3",  	// 可通过which python3获取地址
    "complete_parameters": false,				// 自动补全函数时是否匹配必要参数
    "complete_all_parameters": false,			// 自动补全函数时是否匹配所有参数
    "suppress_word_completions": false,		// 禁止代码补全
    "suppress_explicit_completions": false,		// 禁止精确补全
    "auto_formatting": true,					// 代码格式检查
    "enable_docstrings_tooltip": false,			// 显示文档
    "enable_signatures_tooltip": false,			// 在悬浮窗中显示方法签名
    "display_signatures": false				// 显示方法签名
}
```

##### 格式化
 - Pretty JSON（ctrl+command+j，【json】）
 - JsFormat（ctrl+alt+f，【json、js】）
 - SublimeAStyleFormater（ctrl+alt+f，【C、C++、Java、C#】）
 - SqlBeautifier（command+j，再command+f，【sql】）

##### DocBlockr：注释
##### AutoFileName：提示文件路径
##### ConvertToUTF8：解决中文乱码问题
##### Better Completion：自动补全
> NodeJS、css、html、javascript、jquery、php、sql、react、angularjs、glossary等
##### Javatar：command+shift+k(两次)

```
Project Settings > Set Source Folder > 代码源文件夹，一般为./src
Project Settings > Dependencies > Add External .jar 或 Add Class Folder，增加依赖包或依赖类
Project Settings > Set Default JDK，设置默认JDK版本
Create > Class > package.demoName，新建类
Operations > Correct Class，自动更新包名
Operations > Organize Imports，自动更新import列表
Builds > Project，编译
Builds > Run Main Class / Current Class，运行main程序
```

##### Emmet：快速编写html（Tab 或 ctrl+e）

```
> html:5    或     !html:xt	    或      html:4s
> 标签.class值#id名		如：p.bar#foo
> 标签{标签内的值}		如：h1{标题}
> 标签[属性=属性值]	    如：a[href=#]
> 标签1+标签2			同级，a.class1+a.class2
> 标签1>子标签2		    嵌套级，p#id1>span#id2
> 标签1^上一层标签2	    提升一级，p>span>p
> 标签*N				    多个元素，ul>li.item*3
> css缩写
    - p 表示%
    - e 表示 em
    - x 表示 ex
    - h 表示 height
    - w 表示 width
    - m 表示 margin
    - @f、@f+ 表示 @font-face
    - ov:h、ov-h、ovh 表示 overflow:hidden
    - lg(left,#fff 50%,#000)，渐变颜色
    - -super-foo 表示 后缀为-super-foo的属性
```

##### Color Highlighter：shift+ctrl+c，选择颜色插入

##### Autoprefixer：css的浏览器兼容书写，需要已安装nodejs