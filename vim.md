# vim�༭��
### һ����ݼ�
#### 1.���붨λ
```
������ģʽ�������ģʽʱ��
shift+a ��β
shift+i ����
shift+o ��������һ��
o ��������һ��
```
#### 2.ɾ��ճ����
```
d3-> ����ɾ��3��
d3<- ����ɾ��3��
4dd ����ɾ��4��
p ������ɾ���Ľ���ճ�����������൱�ڼ���
y3-> ���ָ��ƣ��������ͬ��
c ��d���÷�һ�£�������ֱ�ӽ������ģʽ
w ����һ������
cw ɾ����һ���ʲ��޸�
b �Ƶ���һ������
ciw �ڴ���ɾ�����޸�
ci" ɾ�������е���������
pi" ����˫�����е�����
di" ɾ��
fv �ҵ���һ��v��ͷ��
0 �ص���ͷ
df: һֱɾ��ð��
```
#### 3.����
```
/apple �ҵ�apple
:noh ȡ������
zz �ѵ�ǰ���Ƶ��м�
n ������һ��
G ����ƶ������
gg ����ƶ�����ǰ
```
#### 4.����
```
:split ���·���
:q �˳�
:vsplit ���ҷ���
:e ·�� ������������ļ�
ctrl+w h ��굽�����
ctrl+w l ��굽�ҷ���
ctrl+w l ��굽�Ϸ���
ctrl+w j ��굽�·���
���ķ�����С
:res +5
:res -5
:vertical resize-5
:vertical resize+5
ctrl+w t ctrl+w H ���±�����
ctrl+w t ctrl+w K ���ұ�����
```
#### 5.�ֱ�ǩҳ
```
:tabe �����±�ǩҳ
:-tabnext �����ƶ���ǩҳ
:+tabnext �����ƶ���ǩҳ
```
#### 6.ѡ��
```
v ����visualģʽ���ƶ�������ѡ��,k���Խ�����ͨģʽ�Ĳ���
shift+v һ��һ�е�ѡ��
��visual lineģʽ��ѡ�м��У�:normal A.png ����β����.png
ctrl+v ������ӿ�ģʽ������ѡ��һ������
```
### ���������ļ�
```
noremap��map���鲻Ҫ�Ҷ�

syntax on "�������
set number "��ʾ�к�
set relativenumber "��ʾ�ӵ�ǰ�п�ʼ���кţ�no���ǲ���ʾ
set cursorline "����м���
set wrap "�������Զ�����
set showcmd "���·���ʾ����
set wildmenu "��ʾƥ������
set hlsearch "��������
exec "nohlsearch" 
"ˢ�º�ȥ����
set incsearch "�������������ݱ߸���
set ignorecase "���Դ�Сд
set smartcase "���ܴ�Сд

set nocompatible "���ϼ���
"ʶ������ļ���ʽ
filetype on 
filetype indent on 
filetype plugin on 
filetype plugin indent on
set mouse=a "����ʹ�����
set encoding=utf-8 "���ñ���
let &t_ut='' "��Щ��ɫ���ԸĹ���
"��������
set expandtab
set tabstop=2
set shiftwidth=2
set softtabstop=2
set scrolloff=5 "�༭��λ��
set tw=0
set indentexpr=
set backspace=indent,eol,start "�����˸���β
set foldmethod=indent "�Ѵ���������
set foldlevel=99
"��겻ͬ
let &t_SI = "\<Esc>]50;CursorShape=1\x7"
let &t_SR = "\<Esc>]50;CursorShape=2\x7"
let &t_EI = "\<Esc>]50;CursorShape=0\x7"
set laststatus=2
set autochdir
au BufReadPost * if line("'\"") >1 && line("'\"") <= line("&") | exe "normal! g'\"" | endif "���ص��ϴα༭��λ��
```
### �������
#### 1.��װplug�������������
ȥgithub��vim-plug
�������е�����
#### 2.���ļ���д��github��ָ������
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
:PlugInstall (�ǵÿո�)