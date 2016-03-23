# 快捷列表

* [Atom](#atom)
* [Eclipse](#eclipse)
* [Emacs](#emacs)
* [NetBeans](#netbeans)
* [Sublime Text](#sublime-text)
* [TypeScript Builder](#typescript-builder)
* [Vim](#vim)
* [Visual Studio](#visual-studio-20132015)
* [Visual Studio Code](#visual-studio-code)
* [WebStorm](#webstorm)

# Atom

[Atom-TypeScript](https://atom.io/packages/atom-typescript)，由TypeStrong開發的針對Atom的TypeScript語言服務。

# Eclipse

[Eclipse TypeScript 外掛](https://github.com/palantir/eclipse-typescript)，由Palantir開發的Eclipse外掛。

# Emacs

[tide](https://github.com/ananthakumaran/tide) - TypeScript Interactive Development Environment for Emacs

# NetBeans

* [nbts](https://github.com/Everlaw/nbts) - NetBeans TypeScript editor plugin
* [Geertjan's TypeScript NetBeans Plugin](https://github.com/GeertjanWielenga/TypeScript)

# Sublime Text

[Sublime的TypeScript外掛](https://github.com/Microsoft/TypeScript-Sublime-Plugin)，可以通過[Package Control](https://packagecontrol.io/)來安裝，支援Sublime Text 2和Sublime Text 3.

# TypeScript Builder

[TypeScript Builder](http://www.typescriptbuilder.com/)，TypeScript專用IDE.

# Vim

### 語法高亮

* [leafgarland/typescript-vim](https://github.com/leafgarland/typescript-vim)提供了語法檔案用來高亮顯示`.ts`和`.d.ts`。
* [HerringtonDarkholme/yats.vim](https://github.com/HerringtonDarkholme/yats.vim)提供了更多語法高亮和DOM關鍵字。

### 語言服務工具

有兩個主要的TypeScript外掛：

* [Quramy/tsuquyomi](https://github.com/Quramy/tsuquyomi)
* [clausreinke/typescript-tools.vim](https://github.com/clausreinke/typescript-tools.vim)

如果你想要輸出時自動補全功能，你可以安裝[YouCompleteMe](https://github.com/Valloric/YouCompleteMe)並新增以下程式碼到`.vimrc`裡，以指定哪些符號能用來觸發補全功能。YouCompleteMe會呼叫它們各自TypeScript外掛來進行語義查詢。

```vimscript
if !exists("g:ycm_semantic_triggers")
  let g:ycm_semantic_triggers = {}
endif
let g:ycm_semantic_triggers['typescript'] = ['.']
```

# Visual Studio 2013/2015

[Visual Studio](https://www.visualstudio.com/)裡安裝Microsoft Web Tools時就帶了TypeScript。

TypeScript for Visual Studio 2015 在[這裡](http://www.microsoft.com/en-us/download/details.aspx?id=48593)

TypeScript for Visual Studio 2013 在[這裡](https://www.microsoft.com/en-us/download/details.aspx?id=48739)

# Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/)，是一個輕量級的跨平臺編輯器，內建了對TypeScript的支援。

# Webstorm

[WebStorm](https://www.jetbrains.com/webstorm/)，同其它JetBrains IDEs一樣，直接包含了對TypeScript的支援。