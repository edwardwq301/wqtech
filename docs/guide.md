---
hide:
  - navigation
  - toc
  - path
---

# 用法
隐藏侧边栏
对于具有前言属性的文档，可以隐藏导航和/或目录侧边栏。在 Markdown 文件的顶部添加以下
```
---
hide:
  - navigation
  - toc
  - path
---
```



!!! note "Phasellus posuere in sem ut cursus"

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.

??? note

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.


- note
- abstract
- info
- tip
- success
- question
- warning
- failure
- danger
- bug
- example
- quote

=== "C"

    ``` c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ``` c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```

