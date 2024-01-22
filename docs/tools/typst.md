# typst
## demo
起因是两个德国人对 LaTex 不满意，然后弄出了一个 typst

官方的网页端渲染似乎比我本地快🤣

好处：确实如文档所说能快速上手，不用像 latex 上来先记住文档引入命令

不足：中文首行缩进需要一些 trick，希望未来能加入这个功能（2024/1/21）

下面是一些简单例子

```txt
#set text(size: 16pt, lang: "zh",font: "Roboto")
#set par(justify: true, first-line-indent: 2em)
#set page(columns: 1,fill: gray,numbering: "1/1",
          header: [
            #set align(right)
            #set text(size: 0.8em)
            #smallcaps("i am header")
            ],
            footer: [
             #set text(size: 0.8em)
             #set align(right)
             i am footer
            ]
          )
#set heading(numbering: "1.1",supplement: auto)
#set document(author: "wq",date:auto,keywords: "typst")


= first <firstChapter>
#lorem(20)

#lorem(20) 

引用@firstChapter

中文输入一些文字，胡言乱语。按下123发射东风

== second
#figure(image("bing.png", width: 50%), caption: "this is caption")<bingDraw>

= third
i want to use pic @bingDraw

this is some words \ code

new line 

```cpp
#include<iostream>
int main(){
  std::cout<<"hello typst\n";
  return 0;
}
```

```

## todo
`show` 规则，闭包
