# vim编辑器
### 一、快捷键
#### 1.输入定位
```
从正常模式进入插入模式时：
shift+a 行尾
shift+i 行首
shift+o 上面新增一行
o 下面新增一行
```
#### 2.删除粘贴等
```
d3-> 向右删除3个
d3<- 向左删除3个
4dd 向下删除4行
p 将上面删除的进行粘贴（即上面相当于剪切
y3-> 向又复制，其余规则同上
c 和d的用法一致，但可以直接进入插入模式
w 右移一个单词
cw 删除下一个词并修改
b 移到上一个单词
ciw 在词中删除在修改
ci" 删除引号中的所有内容
pi" 复制双引号中的内容
di" 删除
fv 找到下一个v开头的
0 回到开头
df: 一直删到冒号
```
#### 3.搜索
```
/apple 找到apple
:noh 取消高亮
zz 把当前行移到中间
n 搜索下一个
G 光标移动到最后
gg 光标移动到最前
```
#### 4.分屏
```
:split 上下分屏
:q 退出
:vsplit 左右分屏
:e 路径 分屏后进入新文件
ctrl+w h 光标到左分屏
ctrl+w l 光标到右分屏
ctrl+w l 光标到上分屏
ctrl+w j 光标到下分屏
更改分屏大小
:res +5
:res -5
:vertical resize-5
:vertical resize+5
ctrl+w t ctrl+w H 上下变左右
ctrl+w t ctrl+w K 左右变上下
```
#### 5.分标签页
```
:tabe 增加新标签页
:-tabnext 向左移动标签页
:+tabnext 向右移动标签页
```
#### 6.选中
```
v 进入visual模式，移动光标可以选中,k可以进行普通模式的操作
shift+v 一行一行的选中
在visual line模式，选中几行，:normal A.png 在行尾插入.png
ctrl+v 进入可视块模式，可以选中一块区域
```
### 二、配置文件
```
noremap和map建议不要乱动

syntax on "代码高亮
set number "显示行号
set relativenumber "显示从当前行开始的行号，no就是不显示
set cursorline "光标行加线
set wrap "行满后自动换行
set showcmd "在下方显示按键
set wildmenu "显示匹配命令
set hlsearch "搜索高亮
exec "nohlsearch" 
"刷新后去高亮
set incsearch "边输入搜索内容边高亮
set ignorecase "忽略大小写
set smartcase "智能大小写

set nocompatible "新老兼容
"识别各种文件格式
filetype on 
filetype indent on 
filetype plugin on 
filetype plugin indent on
set mouse=a "可以使用鼠标
set encoding=utf-8 "设置编码
let &t_ut='' "有些配色不对改过来
"更改缩进
set expandtab
set tabstop=2
set shiftwidth=2
set softtabstop=2
set scrolloff=5 "编辑行位置
set tw=0
set indentexpr=
set backspace=indent,eol,start "行首退格到行尾
set foldmethod=indent "把代码收起来
set foldlevel=99
"光标不同
let &t_SI = "\<Esc>]50;CursorShape=1\x7"
let &t_SR = "\<Esc>]50;CursorShape=2\x7"
let &t_EI = "\<Esc>]50;CursorShape=0\x7"
set laststatus=2
set autochdir
au BufReadPost * if line("'\"") >1 && line("'\"") <= line("&") | exe "normal! g'\"" | endif "光标回到上次编辑的位置
```
### 三、插件
#### 1.安装plug（插件管理器）
去github搜vim-plug
运行其中的命令
#### 2.在文件中写入github上指定命令
```
call plug#begin()

Plug 'vim-airline/vim-airline'
Plug 'connorholyday/vim-snazzy'
Plug 'preservim/nerdtree'
Plug 'ycm-core/YouCompleteMe'
Plug 'dense-analysis/ale'
Plug ''
Plug ''
Plug ''
Plug ''

call plug#end()
```
:PlugInstall (记得空格)