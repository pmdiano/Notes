| Command       | Meaning       |
| ------------- | ------------- |
| i             | append here   |
| a             | append after  |
| I             | append at beginning of current line that is non-whitespace |
| A             | append at end of current line |
|               |               |
| 0             | move to beginning of current line |
| ^             | move to beginning of current line that is non-whitespace |
| $             | move to end of current line |
| {             | move to previous paragraph |
| }             | move to next paragraph |
| gg            | move to beginning line |
| G             | move to ending line |
| C-e           | scroll down (without moving cursor unless it would go out of screen) one line |
| C-y           | scroll up one line |
| C-d           | move screen down 1/2 page |
| C-u           | move screen up 1/2 page |
| C-f           | move screen down 1 page |
| C-b           | move screen up 1 page |
|               |               |
| "*y           | copy to system clpboard (if :echo(has 'clipboard') returns 1) |
| "*p           | paste from system clipboard |
