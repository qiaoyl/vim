# vim-
我的Vim之路
## 安装遇到的问题
#### YouCompleteMe
- [Vundle](https://github.com/VundleVim/Vundle.vim#about) PluginInstall 后，按照官方教程快速安装运行`install.py --clang-completer` 其中`--clang-completer` 项支持C语言，提示`ycmd_core`未安装，查阅[Full instation Guide](https://github.com/Valloric/YouCompleteMe#full-installation-guide) 需要手动编译安装，这里需要注意
  1. 如果不需要`c`支持运行
  ```  cmake -G "<generator>" . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp```
  2. 如果需要`c`支持运行
  ```cmake -G "<generator>" -DPATH_TO_LLVM_ROOT=~/ycm_temp/llvm_root_dir . ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp```
- 打开纯英文的文件正常，打开含有中文的文件出现非常多的错误，参考[YouCompleteMe插件安装全攻略和问题总结](http://blog.csdn.net/mergerly/article/details/51684155)

> 这个问题需要修改用到转码的地方改为支持GBK，即把字符先gbk decode, 再encode成utf8，如果输入代码的过程中有中文，还是会报错
修改文件 /usr/share/vim/vim74/plugin/YouCompleteMe/third_party/ycmd/ycmd/utils.py中的函数ToUnicode
```
# Returns a unicode type; either the new python-future str type or the real  
# unicode type. The difference shouldn't matter.  
def ToUnicode( value ):  
  if not value:  
    return str()  
  if isinstance( value, str ):  
    return value  
  if isinstance( value, bytes ):  
    # All incoming text should be utf8  
    return str( value, 'utf8' )  
  return str( value )  
  ```
  改为：
  ```
  def ToUnicode( value ):  
  if not value:  
    return str()  
  if isinstance( value, unicode ):  
    return value         
  if isinstance( value, str ):  
    return value         
  if isinstance( value, bytes ):  
    # All incoming text should be utf8  
    try:  
      return str( value.decode('gbk'), 'utf8' )  
    except UnicodeDecodeError,e:  
      pass  
    return str( value, 'utf8' )  
  return str( value )  
  ```

#### ultisnips与YCM一起 tab 键冲突，无法完成自动填写
- 参考[issue#732](https://github.com/SirVer/ultisnips/issues/732)

>      `inoremap <Tab> <c-r>=UltiSnips#ExpandSnippet()<cr>`

## 配色方案
- 自动变换配色 [vim-plugin-random-colorscheme-picker](https://github.com/sunuslee/vim-plugin-random-colorscheme-picker)

## 修改字体 Source Code Pro + YaHei
- 修改注册表 `HKLM/Software/Microsoft/Windows NT/CurrentVersion/FontLink/SystemLink`,Create a Multistring Value添加`Source Code Pro`
`Value`里添加`Microsoft YaHei Mono.ttf,Microsoft YaHei Mono,128,96`
## 终于搞定latex修改后的对齐问题
- 参考[用 Vim 高效地编辑 LaTeX 文档](https://mssun.me/blog/tips-vim-latex.html)
> gq家族 根据 textwidth 自动换行
> - gqgq 换行当前段
> - gqap 格式化整个段
> - {Visual}gq 格式化选定的部分
