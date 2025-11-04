# Question

[simplify-path](https://leetcode.cn/problems/simplify-path/)



# Answer

> solution

### **解决思路总结：栈式目录解析法**

**格言：** “文件系统路径是层次化的。层次化的数据结构，就应该用栈来管理其深度。”

**核心思想：**
路径简化问题可以被视为一个状态机或栈操作问题。我们首先将输入的原始路径分解为独立的组件（目录名、`.`、`..` 等），然后使用一个栈来维护当前有效的、自根目录开始的目录序列。

1.  **路径分割：**
    *   使用斜杠 `/` 作为分隔符，将原始路径分解成一系列路径组件（tokens）。
    *   例如：`/home//foo/../bar/./` 被分解为 `["home", "", "foo", "..", "bar", ".", ""]`。

2.  **栈操作 (LIFO 规则)：**
    遍历解析出的每个组件，并根据其内容执行栈操作：
    *   **忽略空组件 (`""`) 和当前目录 (`.`)：** 这些组件不影响路径深度，直接跳过。
    *   **父目录 (`..`)：** 表示向上返回一级。如果栈非空（即当前不在根目录下），则从栈中弹出栈顶的目录名。如果栈为空，则忽略该操作（不能跳出根目录）。
    *   **有效目录名 (e.g., "home", "foo")：** 表示进入下一级目录。将该名称推入栈中。

3.  **路径重构：**
    *   栈中最终保留的元素就是简化后的规范路径的所有目录名。
    *   **空栈结果：** 如果栈最终为空，表示简化后的路径是根目录，结果为 `"/"`。
    *   **非空栈结果：** 按照栈中元素的顺序，用斜杠 `/` 将它们重新连接起来，并在最前面添加一个斜杠作为根目录。
        *   例如：栈内容 `["home", "bar"]` $\rightarrow$ 结果 `"/home/bar"`。

**实用主义的体现：**
该方法通过将复杂的字符串和指针操作转化为简单的栈的 `push` 和 `pop` 操作，达到了 O(N) 的时间复杂度，并且逻辑清晰，易于处理各种边界情况（如多余斜杠、尾随斜杠等）。这体现了利用正确的数据结构来解决复杂问题的简洁哲学。

### Way Of C

> source code

```c
char* simplifyPath(char* path) {
    if (path == NULL || *path == '\0') {
        // 必须返回 malloced 的结果
        char *res = (char*)malloc(2);
        strcpy(res, "/");
        return res;
    }
    
    // 1. 使用指针数组模拟栈
    // 栈的最大深度不会超过输入路径中字符的总数
    char **stack = (char**)malloc(strlen(path) * sizeof(char*));
    int top = 0; // 栈顶索引

    // 2. 使用 strtok 进行分割。注意：strtok 会修改 path 字符串
    char *token = strtok(path, "/");

    while (token != NULL) {
        if (strcmp(token, ".") == 0) {
            // 忽略当前目录
        } else if (strcmp(token, "..") == 0) {
            // 返回上级目录
            if (top > 0) {
                top--; // 弹出栈顶元素
            }
        } else {
            // 有效目录名，推入栈
            stack[top++] = token;
        }

        token = strtok(NULL, "/");
    }

    // 3. 重构路径
    // 计算最终结果的长度：至少 1 ('/') + 所有目录名长度 + 所有 '/'
    int final_len = 0;
    for (int i = 0; i < top; i++) {
        final_len += strlen(stack[i]) + 1; // 目录名 + 斜杠
    }
    
    if (final_len == 0) { // 栈为空，结果为 "/"
        final_len = 1;
    }
    
    char *result = (char*)malloc(final_len + 1);
    char *ptr = result;

    if (top == 0) {
        *ptr++ = '/';
    } else {
        for (int i = 0; i < top; i++) {
            *ptr++ = '/';
            // 复制目录名 (token 仍然指向 path 中的有效地址)
            strcpy(ptr, stack[i]);
            ptr += strlen(stack[i]);
        }
    }

    *ptr = '\0';
    free(stack);

    return result;
}
```

---

### Way Of C++

> source code

```c++
class Solution {
public:
    /**
     * 简化路径：使用栈（vector）来管理目录层次
     * 时间复杂度：O(N)
     * 空间复杂度：O(N)
     */
    std::string simplifyPath(std::string path) {
        // 1. 使用 vector 模拟栈，存储有效的目录名称
        std::vector<std::string> stack;
        
        // 2. 使用 stringstream 或迭代器来分割路径
        std::stringstream ss(path);
        std::string component;

        // 默认 delimiter 是空格，但我们可以用 getline 强制以 '/' 分割
        while (std::getline(ss, component, '/')) {
            if (component.empty() || component == ".") {
                // 忽略空组件 (多余的斜杠 //) 和当前目录 (.)
                continue;
            } else if (component == "..") {
                // 处理父目录 (..)
                if (!stack.empty()) {
                    stack.pop_back(); // 向上返回一级
                }
            } else {
                // 有效目录名，推入栈
                stack.push_back(component);
            }
        }

        // 3. 重构路径
        std::string simplified_path = "";
        if (stack.empty()) {
            // 如果栈为空，表示根目录 "/"
            return "/";
        }

        for (const auto& dir : stack) {
            simplified_path += "/";
            simplified_path += dir;
        }

        return simplified_path;
    }
};
```
