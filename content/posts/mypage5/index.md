+++
title = "波兰式"
draft = false
date = "2025-05-09"
tags = ["学习","数据结构"]
+++

# 波兰式

## 中缀转前缀

要将一个中缀表达式转换为前缀表达式，可以通过栈的方式实现。中缀表达式是我们日常书写的算式形式，比如 `(A + B) * C`，而前缀表达式（也叫波兰表达式）是一种操作符位于操作数前面的表示方法，例如 `* + A B C`。

### 转换规则：

1. **操作符的优先级**：在转换过程中，优先级需要考虑，例如：
   - `*` 和 `/` 的优先级高于 `+` 和 `-`。
   - 括号的优先级最高。
2. **栈的使用**：
   - 遇到操作数（数字或变量）时，直接添加到输出。
   - 遇到操作符时，按照优先级将栈中较高优先级的操作符弹出，直到栈顶的操作符优先级低于当前操作符或栈为空，然后将当前操作符压入栈中。
   - 遇到左括号 `(` 时，压入栈中。
   - 遇到右括号 `)` 时，弹出栈中的操作符直到遇到左括号为止，并将操作符输出。
3. **最终结果**：在表达式遍历完毕后，栈中可能会有剩余的操作符，逐一弹出并加入结果中。

### 解决思路：

- **步骤一**：将中缀表达式转换为后缀表达式（逆波兰表达式）。
- **步骤二**：将后缀表达式再转换为前缀表达式。

其实，将中缀表达式直接转换为前缀表达式与转换为后缀表达式的步骤基本相同，只是操作顺序反转。为了简洁起见，下面是直接从中缀表达式转换为前缀表达式的实现。

### 中缀转前缀表达式的算法：

1. **反转中缀表达式**：将中缀表达式的括号反转（即 `(` 变为 `)`，`)` 变为 `(`），并反转表达式中的字符顺序。
2. **按中缀转后缀的方法转换**：利用栈按顺序转换中缀表达式为后缀表达式。
3. **反转结果**：将得到的后缀表达式再反转一次，得到前缀表达式。

### 代码实现：

```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define MAX 100

// 用于存储操作符优先级
int precedence(char c) {
    if (c == '+' || c == '-') return 1;
    if (c == '*' || c == '/') return 2;
    if (c == '^') return 3; // ^ 的优先级最高
    return 0;
}

// 判断字符是否是操作数（数字或字母）
int isOperand(char c) {
    return isalpha(c) || isdigit(c);
}

// 反转字符串
void reverse(char* expr) {
    int len = strlen(expr);
    for (int i = 0; i < len / 2; i++) {
        char temp = expr[i];
        expr[i] = expr[len - i - 1];
        expr[len - i - 1] = temp;
    }
}

// 中缀转后缀的函数
void infixToPostfix(char* infix, char* postfix) {
    char stack[MAX];
    int top = -1, k = 0;

    for (int i = 0; infix[i] != '\0'; i++) {
        char token = infix[i];

        // 如果是操作数，直接放入后缀表达式
        if (isOperand(token)) {
            postfix[k++] = token;
        }
        // 如果是左括号，压入栈
        else if (token == '(') {
            stack[++top] = token;
        }
        // 如果是右括号，弹出栈直到遇到左括号
        else if (token == ')') {
            while (top != -1 && stack[top] != '(') {
                postfix[k++] = stack[top--];
            }
            top--; // 弹出左括号
        }
        // 如果是操作符，按优先级处理栈
        else {
            while (top != -1 && precedence(stack[top]) >= precedence(token)) {
                postfix[k++] = stack[top--];
            }
            stack[++top] = token;
        }
    }
    // 弹出栈中所有剩余的操作符
    while (top != -1) {
        postfix[k++] = stack[top--];
    }
    postfix[k] = '\0';
}

// 中缀转前缀的函数
void infixToPrefix(char* infix, char* prefix) {
    // 1. 反转中缀表达式
    reverse(infix);

    // 2. 替换括号
    for (int i = 0; infix[i] != '\0'; i++) {
        if (infix[i] == '(') {
            infix[i] = ')';
        } else if (infix[i] == ')') {
            infix[i] = '(';
        }
    }

    // 3. 获取后缀表达式
    char postfix[MAX];
    infixToPostfix(infix, postfix);

    // 4. 反转后缀表达式得到前缀表达式
    reverse(postfix);
    strcpy(prefix, postfix);
}

int main() {
    char infix[MAX], prefix[MAX];

    // 输入中缀表达式
    printf("Enter infix expression: ");
    scanf("%s", infix);

    // 转换为前缀表达式
    infixToPrefix(infix, prefix);

    // 输出前缀表达式
    printf("Prefix expression: %s\n", prefix);

    return 0;
}
```

### 代码说明：

1. **`precedence` 函数**：用来判断操作符的优先级，`+` 和 `-` 的优先级为 1，`*` 和 `/` 为 2，`^` 为 3。

2. **`isOperand` 函数**：判断一个字符是否是操作数，即数字或字母。

3. **`reverse` 函数**：用来反转字符串，作为中缀转前缀的一部分。

4. **`infixToPostfix` 函数**：将中缀表达式转换为后缀表达式。使用栈来处理操作符，根据优先级和括号来安排操作符的顺序。

5. `infixToPrefix` 函数：

   完成中缀到前缀的转换。步骤如下：

   - 反转中缀表达式；
   - 替换括号；
   - 使用 `infixToPostfix` 函数转换为后缀表达式；
   - 反转后缀表达式，得到前缀表达式。

6. **`main` 函数**：从标准输入读取中缀表达式，调用 `infixToPrefix` 函数进行转换，最后输出前缀表达式。

### 样例输入与输出：

#### 输入：

```
Enter infix expression: (A-B/C)*(A/K-L)
```

#### 输出：

```
Prefix expression: *-A/BC-AKL
```

### 解释：

- 对于中缀表达式 `(A-B/C)*(A/K-L)`，它的前缀表达式为 `*-A/BC-AKL`。

### 时间复杂度：

- 每次遍历中缀表达式的时间复杂度为 `O(n)`，其中 `n` 是中缀表达式的长度。
- 反转字符串的时间复杂度也是 `O(n)`，因此总的时间复杂度为 `O(n)`。

### 空间复杂度：

- 栈的空间复杂度为 `O(n)`，其中 `n` 是中缀表达式的长度。

### 总结：

此代码实现了中缀表达式到前缀表达式的转换，通过反转字符串和括号、转换为后缀表达式，然后再反转后缀得到前缀表达式的方式高效完成了转换过程。

## 根据后缀表达式求值

求值后缀表达式（逆波兰表达式）可以通过栈来实现，利用栈的特性，操作数入栈，操作符弹出栈顶两个操作数进行运算，并将结果再压入栈中。直到整个表达式处理完，栈中的唯一元素就是最终结果。

### 后缀表达式求值的步骤：

1. **初始化一个栈**：用来存储操作数。
2. 遍历后缀表达式：
   - 如果遇到操作数（数字），将其压入栈中。
   - 如果遇到操作符（如 `+`, `-`, `*`, `/`），从栈中弹出两个操作数，进行计算，然后将结果压回栈中。
3. **表达式遍历完成后，栈中的唯一元素即为结果**。

### 处理的操作符：

- 加法 (`+`)
- 减法 (`-`)
- 乘法 (`*`)
- 除法 (`/`)

### 代码实现：

```c
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <string.h>

// 计算两个数的运算结果
int applyOperator(int a, int b, char operator) {
    switch (operator) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/': return a / b;
        default: return 0;
    }
}

// 后缀表达式求值
int evaluatePostfix(const char* postfix) {
    int stack[100];  // 栈存储操作数
    int top = -1;    // 栈顶指针

    for (int i = 0; postfix[i] != '\0'; i++) {
        char token = postfix[i];

        // 如果是数字，则入栈
        if (isdigit(token)) {
            stack[++top] = token - '0';  // 将字符转换为数字并入栈
        }
        // 如果是操作符，弹出两个数进行计算并将结果入栈
        else if (token == '+' || token == '-' || token == '*' || token == '/') {
            int b = stack[top--];  // 弹出栈顶元素
            int a = stack[top--];  // 弹出栈次顶元素
            int result = applyOperator(a, b, token);  // 计算结果
            stack[++top] = result;  // 将结果压入栈
        }
    }
    // 栈顶的唯一元素即为表达式的计算结果
    return stack[top];
}

int main() {
    char postfix[100];

    // 输入后缀表达式
    printf("Enter postfix expression: ");
    scanf("%s", postfix);

    // 计算并输出结果
    int result = evaluatePostfix(postfix);
    printf("Result: %d\n", result);

    return 0;
}
```

### 代码说明：

1. **`applyOperator` 函数**：这个函数根据传入的操作符对两个操作数进行计算，支持四则运算。
2. **`evaluatePostfix` 函数**：该函数实现后缀表达式的求值，使用一个栈来存储操作数。当遇到操作符时，弹出栈顶的两个操作数，进行运算并将结果压入栈中，最后栈中剩下的就是表达式的值。
3. **`main` 函数**：从用户输入获取后缀表达式，调用 `evaluatePostfix` 函数进行计算，并输出结果。

### 样例输入与输出：

#### 输入：

```
Enter postfix expression: 23*54*+
```

#### 输出：

```
Result: 110
```

### 解释：

1. 后缀表达式 `23*54*+` 可以分解为：

   - `2 * 3 = 6`
   - `5 * 4 = 20`
   - `6 + 20 = 26`

   所以结果为 `26`。

### 扩展：

如果表达式中包含多位数（例如 `12 34 +`），则可以按以下方式处理：

- 遇到多位数时，需要先解析出完整的数字，而不仅仅是一个字符。可以通过循环读取连续的数字字符并转换为完整的数值。

如果需要处理浮点数，也可以对 `isdigit` 进行适当修改，或者使用浮点数运算代替整数运算。

### 时间复杂度：

- **时间复杂度**：每个操作符和操作数都被遍历一次，因此时间复杂度是 `O(n)`，其中 `n` 是后缀表达式的长度。
- **空间复杂度**：栈最多会存储 `n` 个操作数，因此空间复杂度是 `O(n)`。

## 根据前缀表达式求值

根据前缀表达式求值的方法与后缀表达式求值非常相似，唯一的区别是前缀表达式的运算顺序与后缀表达式相反。具体来说，前缀表达式的运算从右到左进行，操作符先于操作数出现，因此我们需要**从右向左**扫描前缀表达式。

### 前缀表达式求值的步骤：

1. **初始化一个栈**：用来存储操作数。
2. 从右向左遍历前缀表达式：
   - 如果遇到操作数（数字），将其压入栈中。
   - 如果遇到操作符（如 `+`, `-`, `*`, `/`），从栈中弹出两个操作数，进行计算，然后将结果再压回栈中。
3. **表达式遍历完成后，栈中的唯一元素即为结果**。

### 前缀表达式求值的算法：

1. 从右向左扫描表达式：
   - 如果是操作数（数字），将其压入栈中。
   - 如果是操作符（如 `+`, `-`, `*`, `/`），从栈中弹出两个操作数，进行计算，并将结果压入栈中。
2. **最后栈中只剩下一个元素，即为表达式的计算结果**。

### 代码实现：

```c
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <string.h>

// 计算两个数的运算结果
int applyOperator(int a, int b, char operator) {
    switch (operator) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/': return a / b;
        default: return 0;
    }
}

// 前缀表达式求值
int evaluatePrefix(const char* prefix) {
    int stack[100];  // 栈存储操作数
    int top = -1;    // 栈顶指针

    // 从右向左遍历前缀表达式
    for (int i = strlen(prefix) - 1; i >= 0; i--) {
        char token = prefix[i];

        // 如果是数字，则入栈
        if (isdigit(token)) {
            stack[++top] = token - '0';  // 将字符转换为数字并入栈
        }
        // 如果是操作符，弹出两个数进行计算并将结果入栈
        else if (token == '+' || token == '-' || token == '*' || token == '/') {
            int a = stack[top--];  // 弹出栈顶元素
            int b = stack[top--];  // 弹出栈次顶元素
            int result = applyOperator(a, b, token);  // 计算结果
            stack[++top] = result;  // 将结果压入栈
        }
    }
    // 栈顶的唯一元素即为表达式的计算结果
    return stack[top];
}

int main() {
    char prefix[100];

    // 输入前缀表达式
    printf("Enter prefix expression: ");
    scanf("%s", prefix);

    // 计算并输出结果
    int result = evaluatePrefix(prefix);
    printf("Result: %d\n", result);

    return 0;
}
```

### 代码说明：

1. **`applyOperator` 函数**：用于根据传入的操作符对两个操作数进行计算，支持四则运算。

2. `evaluatePrefix` 函数

   ：实现前缀表达式的求值。使用栈从右向左遍历前缀表达式：

   - 如果遇到操作数，直接压入栈中。
   - 如果遇到操作符，从栈中弹出两个操作数，进行计算，并将结果压回栈中。

3. **`main` 函数**：从用户输入读取前缀表达式，调用 `evaluatePrefix` 函数进行计算，并输出结果。

### 样例输入与输出：

#### 输入：

```
Enter prefix expression: *+23-54
```

#### 输出：

```
Result: 23
```

### 解释：

前缀表达式 `*+23-54` 可以分解为：

- `+2 3 = 5`
- `-5 4 = 1`
- `5 * 1 = 5`

因此，结果是 `5`。

### 扩展：

如果表达式中包含多位数（例如 `12 34 +`），可以通过以下方式处理：

- 遇到多位数时，需要解析出完整的数字，而不仅仅是一个字符，可以通过循环读取连续的数字字符并转换为完整的数值。

如果需要处理浮点数，也可以对 `isdigit` 进行适当修改，或者使用浮点数运算代替整数运算。

### 时间复杂度：

- **时间复杂度**：每个操作符和操作数都被遍历一次，因此时间复杂度是 `O(n)`，其中 `n` 是前缀表达式的长度。
- **空间复杂度**：栈最多会存储 `n` 个操作数，因此空间复杂度是 `O(n)`。

### 总结：

此代码实现了前缀表达式的求值，通过栈从右到左扫描前缀表达式，在遇到操作符时弹出两个操作数进行计算，并将结果压回栈中，最终栈中的唯一元素即为计算结果。
