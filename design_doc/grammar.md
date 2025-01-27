## Hand-modified Tiger grammer in appropriate LL(k) grammar form

Approach:

```
naive grammar
-> operator precedences and left associate
-> remove left recursion and perform left factoring
-> parse table
-> write parse code
```

### Grammar

```bash
# tiger-program
1: <tiger-program> -> let <declaration-list> in <stat-seq> end

# declarations
2: <declaration-list> -> <declaration> <declaration-list>
3: <declaration-list> -> NULL

# declaration
4: <declaration> -> <type-declaration>
5: <declaration> -> <var-declaration>
6: <declaration> -> <func-declaration>

# type-declaration
9: <type-declaration> -> type id = <type>;
10: <type> -> <type-id>
11: <type> -> array [INTLIT] of <type-id>
12: <type> -> {id: <type-id>}
13: <type-id> -> id
14: <type-id> -> int
15: <type-id> -> float

# var-declaration
16: <var-declaration> -> var id : <type> <optional-init>;
17: <optional-init> -> := <exp>
18: <optional-init> -> NULL

# funct-declaration
19: <funct-declaration> -> function id (<param-list>) <ret-type> begin <stat-seq> end;
20: <param-list> -> <param> <param-list-tail>
21: <param-list> -> NULL
22: <param-list-tail> -> , <param> <param-list-tail>
23: <param-list-tail> -> NULL
24: <ret-type> -> : <type>
25: <ret-type> -> NULL
26: <param> -> id : <type>

# stat-seq
27: <stat-seq> -> <stat> <stat-seq>
28: <stat-seq> -> NULL

# stat : it is an expression without return value
# if-stat
29: <stat> -> if <expr> then <stat-seq> <stat-if-tail>
30: <stat-if-tail> -> else <stat-seq> endif;
31: <stat-if-tail> -> endif;

# funct-or-assgin-stat
32: <stat> -> id <stat-funct-or-assign>
33: <stat-funct-or-assign> -> <lvalue-tail> := <stat-assign>;
34: <stat-funct-or-assign> -> (<expr-list>);

35: <stat-assign> -> id <stat-assign-stuff>
36: <stat-assign> -> (<expr>) <stat-assign-tail>
37: <stat-assign> -> <const> <stat-assign-tail>

38: <stat-assign-stuff> -> (<expr-list>)
39: <stat-assign-stuff> -> <lvalue-tail> <stat-assign-tail>

40: <stat-assign-tail> -> <OR-op> <OR-expr> <stat-assign-tail>
41: <stat-assign-tail> -> <AND-op> <AND-expr> <stat-assign-tail>
42: <stat-assign-tail> -> <compare-op> <compare> <stat-assign-tail>
43: <stat-assign-tail> -> <add-op> <term> <stat-assign-tail>
44: <stat-assign-tail> -> <mul-op> <factor> <stat-assign-tail>
45: <stat-assign-tail> -> NULL

# while-stat
46: <stat> -> while <expr> do <stat-seq> enddo;

# for-stat
47: <stat> -> for id := <expr> to <expr> do <stat-seq> enddo;

# break-stat
48: <stat> -> break;

# return-stat
49: <stat> -> return <expr>;

# let-stat
50: <stat> -> let <declaration-segment> in <stat-seq> end

# expr : always be evaluated to a value
51: <expr> -> <OR-expr> <stat-assign-tail>
52: <OR-expr> -> <AND-expr> <stat-assign-tail>
53: <AND-expr> -> <compare> <stat-assign-tail>
54: <compare> -> <term> <stat-assign-tail>
55: <term> -> <factor> <stat-assign-tail>

56: <factor> -> (<expr>)
57: <factor> -> <const>
58: <factor> -> <lvalue>

# const
59: <const> -> INTLIT
60: <const> -> FLOATLIT

# binary-operator
61: <OR-op> -> |
62: <AND-op> -> &
63: <compare-op> -> <=
64: <compare-op> -> >=
65: <compare-op> -> <
66: <compare-op> -> >
67: <compare-op> -> <>
68: <compare-op> -> =
69: <add-op> -> -
70: <add-op> -> +
71: <mul-op> -> /
72: <mul-op> -> *

73: <expr-list> -> NULL
74: <expr-list> -> <expr> <expr-list-tail>
75: <expr-list-tail> -> , <expr> <expr-list-tail>
76: <expr-list-tail> -> NULL

77: <lvalue> -> id <lvalue-tail>
78: <lvalue-tail> -> [<expr>]
79: <lvalue-tail> -> NULL
```

## First Sets

```bash
First(<tiger-program>) = {let}
First(<declaration-segment>) = {type var function NULL}
First(<type-declaration-list>) = {type NULL}
First(<var-declaration-list>) = {var NULL}
First(<funct-declaration-list>) = {function NULL}
First(<type-declaration>) = {type}
First(<type>) = {int float array id}
First(<type-id>) = {int float}
First(<var-declaration>) = {var}
First(<id-list>) = {id}
First(<id-list-tail>) = {, NULL}
First(<optional-init>) = {:= NULL}
First(<funct-declaration>) = {function}
First(<param-list>) = {id NULL}
First(<param-list-tail>) = {, NULL}
First(<ret-type>) = {: NULL}
First(<param>) = {id}
First(<stat-seq>) = {if id while for break return let}
First(<stat-seq-tail>) = {if id while for break return let NULL}
First(<stat>) = {if id while for break return let}
First(<stat-if-tail>) = {else endif}
First(<stat-funct-or-assign>) = {[ ( :=}
First(<stat-assign>) = {id ( INTLIT FLOATLIT}
First(<stat-assign-stuff>) = {( [ | & <= >= < > <> = - + / * NULL}
First(<stat-assign-tail>) = {| & <= >= < > <> = - + / * NULL}
First(<expr>) = {( INTLIT FLOATLIT id}
First(<OR-expr>) = {( INTLIT FLOATLIT id}
First(<AND-expr>) = {( INTLIT FLOATLIT id}
First(<compare>) = {( INTLIT FLOATLIT id}
First(<term>) = {( INTLIT FLOATLIT id}
First(<factor>) = {( INTLIT FLOATLIT id}
First(<const>) = {INTLIT FLOATLIT}
First(<OR-op>) = {|}
First(<AND-op>) = {&}
First(<compare-op>) = {<= >= < > <> =}
First(<add-op>) = {- +}
First(<mul-op>) = {/ *}
First(<expr-list>) = {( INTLIT FLOATLIT id NULL}
First(<expr-list-tail>) = {, NULL}
First(<lvalue>) = {id}
First(<lvalue-tail>) = {[ NULL}
```

## Follow Sets

```bash
Follow(<tiger-program>) = {$}
Follow(<declaration-segment>) = {in}
Follow(<type-declaration-list>) = {var function in}
Follow(<var-declaration-list>) = {function in}
Follow(<funct-declaration-list>) = {in}
Follow(<type-declaration>) = {type var function in}
Follow(<type>) = {; := begin , )}
Follow(<type-id>) = {; := begin , )}
Follow(<var-declaration>) = {var function in}
Follow(<id-list>) = {:}
Follow(<id-list-tail>) = {:}
Follow(<optional-init>) = {;}
Follow(<funct-declaration>) = {function in}
Follow(<param-list>) = {)}
Follow(<param-list-tail>) = {)}
Follow(<ret-type>) = {begin}
Follow(<param>) = {, )}
Follow(<stat-seq>) = {end endif else enddo}
Follow(<stat-seq-tail>) = {end endif else enddo}
Follow(<stat>) = {if id while for break return let end endif else enddo}
Follow(<stat-if-tail>) = {if id while for break return let end endif else enddo}
Follow(<stat-funct-or-assign>) = {if id while for break return let end endif else enddo}
Follow(<stat-assign>) = {;}
Follow(<stat-assign-stuff>) = {;}
Follow(<stat-assign-tail>) = {then ) do to ; ] ,}
Follow(<expr>) = {then ) do to ; ] ,}
Follow(<OR-expr>) = {| & <= >= < > <> = - + / * then ) do to ; ] ,}
Follow(<AND-expr>) = {| & <= >= < > <> = - + / *  then ) do to ; ] ,}
Follow(<compare>) = {| & <= >= < > <> = - + / *  then ) do to ; ] ,}
Follow(<term>) = {| & <= >= < > <> = - + / *  then ) do to ; ] ,}
Follow(<factor>) = {| & <= >= < > <> = - + / *  then ) do to ; ] ,}
Follow(<const>) = {| & <= >= < > <> = - + / *  then ) do to ; ] ,}
Follow(<OR-op>) = {( INTLIT FLOATLIT id}
Follow(<AND-op>) = {( INTLIT FLOATLIT id}
Follow(<compare-op>) = {( INTLIT FLOATLIT id}
Follow(<add-op>) = {( INTLIT FLOATLIT id}
Follow(<mul-op>) = {( INTLIT FLOATLIT id}
Follow(<expr-list>) = {)}
Follow(<expr-list-tail>) = {)}
Follow(<lvalue>) = {| & <= >= < > <> = - + / *  then ) do to ; ] ,}
Follow(<lvalue-tail>) = {:= | & <= >= < > <> = - + / *  then ) do to ; ] ,}
```
