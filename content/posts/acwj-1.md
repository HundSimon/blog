+++
title = 'acwj 1 Lexical Scanner'
date = 2025-11-05T18:53:29+08:00
draft = false
+++

分词器 Lexer

<!--more-->

对于编译器来说，想要识别特定的语法和逻辑，就需要用到把代码分成有意义的每一小节，这一小节被称为 token，而识别 token 的编译器部分被称为 lexical analyzer 或 lexer

Lexer 是编译器编译的第一步

本文将从零构建一个能识别 + + * / 的扫描器

## Token

在这里，token被定义为

```c
struct token {
  int token;      // what type of token this is
  int intvalue;   // value, if it’s an integer literal
};
```

而 `int token` 则为

```c
enum {
  T_PLUS, T_MINUS, T_STAR, T_SLASH, T_INTLIT
};
```

## 核心 scan()

scan() 是这个示例的核心，我们将一步步构建

### next()

这个函数的作用是从文件顺序读取下一个字符，有两个核心变量`int Putback` 和 `Line`

在现在的编译器中，Lexer需要一个一个字读字符，但有时候会多读一个。多读的这一个不能丢弃，因为它是下一个token的开头，放回去后我们在下次就可以读到它

`int Putback` 是一个单字符缓冲区，用于把多读出来的字符放回去

`int Line` 是行数

```c
int Putback;
int Line;
FILE *Infile;

static int next(void) {
    int c;

    // If there's value in Putback, use it
    if (Putback) {
        c = Putback;
        Putback = 0;
        return c;
    }

    // Otherwise, read next character
    c = fgetc(Infile);

    // Add Line number
    if ('\n' == c) {
        Line++;
    }

    return c;
}
```

### 跳过空格

空格不影响数学计算，遂跳过

```c
static int skip(void) {
  int c = next();
  while (isspace(c))
    c = next();
  return c;
}
```

### 扫描数字

数字有可能不是一位数，因此我们要读取完整数字

```c
// Return subscript
// Find position of c in s
static int chrpos(char *s, int c) {
    char *p = strchr(s, c);
    return (p ? p - s : -1);
}

// Turn string into decimals
static int scanint(int c) {
    int k, val = 0;

    while ((k = chrpos("0123456789", c)) >= 0) {
        val = val * 10 + k;
        c = next();
    }

    putback(c);
    return val;
}
```

### scan()

scan函数用于读取字符，填充 `token struct`

```c
int scan(struct token *t) {
    int c = skip();

    switch (c) {
        case EOF: return 0;
        case '+': t->token = T_PLUS; break;
        case '-': t->token = T_MINUS; break;
        case '*': t->token = T_STAR; break;
        case '/': t->token = T_SLASH; break;
        default:
            if (isdigit(c)) {
                t->token = T_INTLIT;
                t->intvalue = scanint(c);
                break;
            }

            printf("Unrecognized token %c on Line %d", c, Line);
            exit(1);
    }

    return 1;
}
```

样例输入

```
12+1*2
```

样例输出

```
Token intlit, value 12
Token +
Token intlit, value 1
Token *
Token intlit, value 2
```

## 完整代码

```c
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int Putback;
int Line;
FILE *Infile;

struct token {
    int token;      // what type of token this is
    int intvalue;   // value, if it’s an integer literal
};

enum {
    T_PLUS, T_MINUS, T_STAR, T_SLASH, T_INTLIT
  };

static int next(void) {
    int c;

    // If there's value in Putback, use it
    if (Putback) {
        c = Putback;
        Putback = 0;
        return c;
    }

    // Otherwise, read next character
    c = fgetc(Infile);

    // Add Line number
    if ('\n' == c)
        Line++;

    return c;
}

static int skip(void) {
    int c = next();
    while (isspace(c))
        c = next();
    return c;
}

static void putback(int c) {
    Putback = c;
}

static int chrpos(char *s, int c) {
    char *p = strchr(s, c);
    return (p ? p - s : -1);
}

static int scanint(int c) {
    int k, val = 0;

    while ((k = chrpos("0123456789", c)) >= 0) {
        val = val * 10 + k;
        c = next();
    }

    putback(c);
    return val;
}

int scan(struct token *t) {
    int c = skip();

    switch (c) {
        case EOF: return 0;
        case '+': t->token = T_PLUS; break;
        case '-': t->token = T_MINUS; break;
        case '*': t->token = T_STAR; break;
        case '/': t->token = T_SLASH; break;
        default:
            if (isdigit(c)) {
                t->token = T_INTLIT;
                t->intvalue = scanint(c);
                break;
            }

            printf("Unrecognized token %c on Line %d", c, Line);
            exit(1);
    }

    return 1;
}

static void scanfile() {
    struct token T;
    char *tokstr[] = { "+", "-", "*", "/", "intlit" };

    while (scan(&T)) {
        printf("Token %s", tokstr[T.token]);
        if (T.token == T_INTLIT)
            printf(", value %d", T.intvalue);
        printf("\n");
    }
}

int main(int argc, char *argv[]) {
    Line = 1;
    Putback = '\n';
    Infile = fopen(argv[1], "r");
    scanfile();
    return 0;
}
```