## 1. The lexical grammars of Python and Haskell are not regular. What does that mean, and why aren’t they?

## Python 和 Haskell 的词汇语法并不规则 。这意味着什么，为什么不是？

Python 和 Haskell 使用的都是缩减语法。所以它们必须将缩进级别的变化识别为标记。这样做需要比较连续行开头的空白量，这不能使用常规语法来完成。

## 2. Aside from separating tokens—distinguishing print foo from printfoo—spaces aren’t used for much in most languages. However, in a couple of dark corners, a space does affect how code is parsed in CoffeeScript, Ruby, and the C preprocessor. Where and what effect does it have in each of those languages?

## 除了分隔标记 （ 区分 print foo 和 printfoo）之外 ， 空格在大多数语言中并没有太多使用。然而，在几个黑暗的角落里，一个空格确实会影响 CoffeeScript、Ruby 和 C 预处理器中代码的解析方式。它在每种语言中都有什么影响？

作者的答案：

In CoffeeScript, parentheses are option in function calls. You can call a function like:

doStuff withThis
Also, there is a nice syntax for lambda functions:

() -> someLambda
-> anotherOne
On the second line, you can see that you can omit the () if the lambda takes no parameters. So what does this do:

someFunction () -> someLambda
Does it call someFunction with zero parameters and then call the result of that with one parameter, a lambda? Or does it call someFunction with one parameter, the lambda? The answer depends on spaces:

someFunction() -> someLambda
# Means the same as:
someFunction()(() -> someLambda)

someFunction () -> someLambda
# Means the same as:
someFunction(() -> someLambda)
Ruby has similar corner cases because it also allow omitting the parentheses in method calls (which is where CoffeeScript gets it from).

The C preprocessor relies on spaces to distinguish function macros from simple macros:

#define MACRO1 (p) (p)
#define MACRO2(p) (p)
Here, MACRO1 is a simple text macro that expands to (p) (p) when used. MACRO2(p) is a function-like macro that takes a parameter and expands to (p) with p replaced by the parameter.

## 3. Our scanner here, like most, discards comments and whitespace since those aren’t needed by the parser. Why might you want to write a scanner that does not discard those? What would it be useful for?

## 我们这里的扫描仪，就像大多数扫描仪一样，丢弃了注释和空格，因为这些 解析器不需要。为什么要编写一个扫描仪 不丢弃那些？它有什么用？

注释也可以提供有用的信息，利用注释生成文档之类的？

## 4. Add support to Lox’s scanner for C-style /* ... */ block comments. Make sure to handle newlines in them. Consider allowing them to nest. Is adding support for nesting more work than you expected? Why?
## 为 Lox 的扫描仪添加对 C 样式 /* ... */ 块注释的支持。确保处理其中的换行符。考虑让它们筑巢。添加对嵌套的支持是否比您预期的要多？为什么？

作者的答案：

You can see where I've implemented them for a similar language here:

https://github.com/munificent/wren/blob/c6eb0be99014d34085e2d24c696aed449e2fb171/src/vm/wren_compiler.c#L663

The interesting part is the nesting variable. Like challenge #1, we require some extra state to track the nesting, which makes this not quite regular.

Note also that we need to handle an unterminated block comment.