| 插入          |               |
| ------------- | ------------- |
| i             | append here   |
| a             | append after  |
| I             | append at beginning of current line that is non-whitespace |
| A             | append at end of current line |
| o             | 当前行的下方打开一行 |
| O             | 当前行的上方打开一行 |
<br/>

| 删除/剪切     |               |
| ------------- | ------------- |
| x             | 当前字符      |
| 3x            | 当前字符及其后的两个字符 |
| dd            | 当前行        |
| 5dd           | 当前行及随后的四行文本 |
| dW            | 从光标位置开始到下一个单词的开头 |
| d$            | 从光标位置开始到当前行的行尾 |
| d0            | 从光标位置开始到当前行的行首 |
| d^            | 从光标位置开始到文本行的第一个非空字符 |
| dG            | 从当前行到文件的末尾 |
| d20G          | 从当前行到文件的第20行 |
`d`命令不仅删除文本，它还剪切文本。过后我们执行小`p`命令把剪切板中的文本粘贴到光标位置之后，或者是大`P`命令把文本粘贴到光标之前。`y`命令用来拉（复制）文本：

| 复制          |               |
| ------------- | ------------- |
| yy            | 当前行        |
| 5yy           | 当前行及随后的四行文本 |
| yW            | 从光标位置开始到下一个单词的开头 |
| y$            | 从光标位置开始到当前行的行尾 |
| y0            | 从光标位置开始到当前行的行首 |
| y^            | 从光标位置开始到文本行的第一个非空字符 |
| yG            | 从当前行到文件的末尾 |
| y20G          | 从当前行到文件的第20行 |
<br />

| 移动          |               |
| ------------- | ------------- |
| 0             | move to beginning of current line |
| ^             | move to beginning of current line that is non-whitespace |
| $             | move to end of current line |
| w             | 移动到下一个单词或标点符号的开头 |
| W             | 移动到下一个单词的开头，忽略标点符号 |
| b             | 移动到上一个单词或标点符号的开头 |
| B             | 移动到上一个单词的开头，忽略标点符号 |
| {             | move to previous paragraph |
| }             | move to next paragraph |
| gg            | move to beginning line |
| G             | move to ending line |
| numberG       | 移动到第number行 |
| C-e           | scroll down (without moving cursor unless it would go out of screen) one line |
| C-y           | scroll up one line |
| C-d           | move screen down 1/2 page |
| C-u           | move screen up 1/2 page |
| C-f           | move screen down 1 page |
| C-b           | move screen up 1 page |
<br />

| 查找和替换    |               |
| ------------- | ------------- |
| 查找一行      | `f`命令查找一行，移动光标到下一个所指定的字符上。<br/> 在一行中执行了字符的查找命令之后，通过输入分号来重复这个查找。|
| 查找整个文件  | `/`命令，输入要查找的单词或短语后，按下回车。<br/>通过`n`命令来重复先前的查找。vi允许使用正则表达式。|
| 全局查找和替换| `ex`命令。例如：`:%s/Line/line/g`，冒号字符运行一个ex命令，<br/>`%`指定要操作的行数，百分号是一个快捷方式，表示从第一行到最后一行。<br/>操作范围也可以用`1,5`来代替，或者用`1,$`来代替，<br/>意思是从第一行到文件的最后一行。如果省略了文本行的范围，则操作只对当前行有效。<br/>`s`指定操作，为替换。`/Line/line`指定查找和替代文本。<br/>`g`是全局的意思，如果没有`g`，则只替换每个文本行中第一个匹配的字符串。<br/>`c`可以给替换加上确认。|
<br />

| 操作          |               |
| ------------- | ------------- |
| u             | 撤销上次操作 |
| J             | 连接行        |
| "*y           | copy to system clpboard (if :echo(has 'clipboard') returns 1) |
| "*p           | paste from system clipboard |
