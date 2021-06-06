# Tutorial

El presente documento es un resumen de los aspectos más relevantes del tutorial oficial de *Python* 3.9. Los títulos se han mantenido en inglés, que es el idioma del documento que se ha utilizado como base para este resumen.

## 2. USING THE PYTHON INTERPRETER

### 2.1 Invoking the Interpreter

Intérprete en modo interactivo: `python3.9`. Para salir, `Ctrl-D` (`Ctrl-W` en Windows), o `quit()`. Si se le pasa como argumento (o como entrada estándar) un nombre de archivo, ejecuta ese *script*.

#### 2.1.1 Argument passing

Argumentos son recogidos en `sys.argv`. En este caso, `sys.argv[0]` es un *string* con el nombre del *script*. Si no se ha especificado *script*, es un *string* vacío.

#### 2.1.2 Interactive mode

Cuando la entrada al intérprete es la entrada estándar.

### 2.2 The Interpreter and Its Environment

#### 2.2.1 Source Code Encoding

Un *script* se trata por defecto como codificado con *UTF-8*. Se puede codificar el *script* de otro modo, pero deberemos indicarlo, poniendo en la *primera línea* del archivo:

`# -*- coding: <encoding> -*-`

Donde 'encoding' es el denominador de la codificación concreta (códec válido soportado por *Python*). Esta línea podría ser la segunda si la primera es la línea *shebang* de los *scripts* ejecutables.

## 3. AN INFORMAL INTRODUCTION TO PYTHON

### 3.1 Using Python as a Calculator

#### 3.1.1 Numbers

Para comentarios se usa el carácter `#` (no dentro de literales *string*). Hasta el final de la línea física.

En modo interactivo, entrar una expresión la evalúa y muestra su valor.

El símbolo `/` es el operador división de punto flotante (*float*); la división entera (*int*) es `//` (devuelve el *floor*); resto (módulo) es `%`. Para potencias se usa `**`. Suma es `+`, resta `-`, y asignación `=`.

Las expresiones con enteros y *floats*, convierten los enteros a *float* antes de evaluar.

En modo interactivo, la variable guión bajo (***_***) se refiere al resultado anterior:

```python
>>> 11 * 3.5
38.5
>>> 5 + _
43.5
>>> round(_,2)
43.5
```

Tipos numéricos: `int`, `float` y `complex`. Estos últimos usan el sufijo `j` (o `J`) para la parte imaginaria (p.e. ***3+5j***).

#### 3.1.2 Strings

Pueden ir entre comillas simples o dobles. En el primer caso, si hay comillas simples en el interior, deberán ir *escaped* (precedidas de ***\\***). Lo mismo para las dobles.

En modo interactivo, cuando se presenta como salida un *string*, aparece entre comillas simples, a no ser que haya comillas simples en el interior, en cuyo caso lo presentará con comillas dobles. Presenta los caracteres *escaped*. La función `print()` los presenta sin entrecomillar y sin *escape characters*.

Un *raw string* toma los caracteres tal cual, no entiende de *escaped characters*:

***r'Hola,\\nen'*** (o ***r"Hola,\\nen"***) incluye el carácter '***\\***' entre la coma y la '***n***'.

Para *multiline strings* podemos usar *triple-quoted strings*: ***"""..."""*** o ***'''...'''*** y todos los *newlines* que tecleemos quedarán también definidos en el *string*.

De todas formas, si el último carácter de una línea es '***\\***', la línea se concatena con la siguiente, aunque estemos dentro de un *triple-quoted string*. Es la forma de definir una línea lógica, compuesta por dos o más líneas físicas.

El operador ***+*** se puede usar para concatenar *strings*, y ***\**** para duplicarlos, triplicarlos, etc.:

```python
>>> letter = 'A'
>>> word = 'Help' + letter
>>> word
'HelpA'
>>> '<' + word * 5 + '>'
'<HelpAHelpAHelpAHelpAHelpA>'
```

Literales *string* contiguos separados por cualquier cantidad de espacios (incluyendo saltos de línea) son concatenados automáticamente.

Si 'st' es un *string*, `st[0]` será el primer carácter, `st[1]` el segundo, etc. `st[-1]` será el último, `st[-2]` el penúltimo, etc. También funciona el *slicing:* `st[0:2]` son los caracteres desde el 0 (incluido) al 2 (excluido), `st[2:5]` del 2 al 4, etc. El primero va siempre incluido y el segundo excluido, ya que de ese modo, `st[:i]` + `st[i:]` es el *string* entero. La omisión del primer número se refiere a 0 (principio del *string*), y la omisión del segundo, al número de elementos del *string* (final del *string*).

Tanto un índice como un *slice* devolverán un *string* (si es un índice, un *string* de longitud 1, no existe el tipo carácter).

Un índice negativo contará desde la derecha: -1 es el último carácter, -2 el penúltimo, etc.

En el *subscripting* (`st[N]`), especificar un índice *out of bounds* dará error, tanto si es positivo como negativo. En *slicing* no da error:

```python
>>> word='Python'
>>> word[4:42]
'on'
>>> word[42:]
''
```

Los *strings* son inmutables. Si queremos otro contenido hay que crear uno nuevo.

La *built-in function* `len(s)` da la longitud del *string* (número de caracteres).

#### 3.1.3 Lists

Las listas pueden contener elementos de tipos distintos. Para acceder a los elementos de una lista, se puede indexar o usar *slicing*. También se pueden concatenar listas mediante ***+***, o multiplicar con ***\****.

Un índice devuelve un elemento de la lista, mientras que un *slice* devolverá una lista, aunque sea de un solo elemento, o una *shallow copy* (ver más abajo) de la lista original en el caso de ***lista[:]***.

Las listas son mutables, con lo que se puede cambiar su contenido. Otra cosa sería cambiar el contenido de sus elementos, que solo será posible si el elemento en cuestión es, a su vez, mutable:

```python
>>> letters=['a','b','c','d','e','f','g']
```

Añadir elementos al final:

```python
>>> letters.append('h')  # append() es un método del tipo lista
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
```
Cambiar elementos:

```python
>>> letters[0]=['A']
>>> letters[2:5]=['C','D','E']
>>> letters
['A', 'b', 'C', 'D', 'E', 'f', 'g', 'h']
```

Eliminar elementos:
```python
>>> letters[2:5]=[]
>>> letters
['A', 'b', 'f', 'g', 'h']
```

La built-in function ***len()*** también se aplica a listas, devolviendo el número de elementos de primer nivel (un elemento lista con varios elementos a su vez, contará como uno).

```python
>>> len(letters)
5
```

Borrar todos los elementos

```python
>>> letters[:]=[]
>>> letters
[]
```

Las listas son anidables.

Para ver si un elemento 'e' existe en una lista 'L', `e in L` devuelve ***True*** si es cierto y ***False*** de otro modo.

### Notas personales

#### Índices y *slices*

***letters[0]*** se refiere al primer elemento; ***letters[0:1]*** se refiere a una sublista que contiene el primer elemento:

```python
>>> letters = ['a', 'b', 'c', 'd', 'e']
>>> letters[2] = [1, 2, 3]  # letters[2] era 'c'
>>> letters
['a', 'b', [1, 2, 3], 'd', 'e']
>>> letters = ['a', 'b', 'c', 'd', 'e']
>>> letters[2:3] = [1, 2, 3]  # letters[2:3] era ['c']
>>> letters
['a', 'b', 1, 2, 3, 'd', 'e']
```

Si queremos borrar un solo elemento, `letters[2] = []` sustituirá el elemento por una lista vacía; en cambio `letters[2:3] = []` elimina el elemento.

Para añadir uno o más elementos a una lista sin usar `append()`, se puede hacer con cualquier *slice* que empiece *out of bounds* por la derecha:

```python
>>> a = [1, 2, 3]
>>> a[len(a):] = [4, 5]
>>> a
[1, 2, 3, 4, 5]
```
Para sustituir *n* elementos por *m* elementos, seleccionar los elementos a sustituir en el *slice*, y asignarle la nueva lista. Por ejemplo, para sustituir 2 elementos (3º y 4º) por uno solo:

```python
>>> a = [1, 2, 3, 4, 5]
>>> a[2:4] = ['tres y cuatro']
>>> a
[1, 2, 'tres y cuatro', 5]
```

Para insertar uno o más elementos en la posición *n*, insertar una lista en un *slice* que inicie en la posición deseada (*n*) y termine antes de esa, por ejemplo mediante el *slice* ***[n:n]***:

```python
>>> a = [1, 2, 3, 4, 5]
>>> a[3:3] = [3.25, 3.5, 3.75]
>>> a
[1, 2, 3, 3.25, 3.5, 3.75, 4, 5]
```

Para insertar uno o más elementos por el principio, usar un *slice* que inicie en el primero o antes, y termine antes del primero:

```python
>>> a = [1, 2, 3]
>>> a[:0]=['a', 'b', 'c']
>>> a
['a', 'b', 'c', 1, 2, 3]
```

#### Organización interna de los datos

*Python* trata las variables como apuntadores, o referencias a una tabla *hash*, o cache de memoria. Se puede ver una variable como un apuntador puro y duro a un objeto.

En *Python*, cada objeto tiene su propio ID, que es el número utilizado para acceder a él en memoria (ya sea por *hash*, dirección de memoria, o lo que *Python* estime conveniente). En todo caso es un número único por cada objeto.

Para conocer el ID de un objeto, podemos usar `id()` pasándole por parámetro la variable que lo referencia. Cuando dos objetos ***A*** y ***B*** tienen el mismo ID, ***A is B*** devuelve ***True***.

Algunos objetos *inmutables* (números, *strings*, etc.) pueden tener un ID fijo, utilizado en un *hash* en Python para ahorrar memoria.

El operador asignación (***=***) simplemente asigna un ID o referencia a la variable.

```python
>>> a = [[1, 2, 3], [11, 22, 33], [111, 222, 333]]
>>> b=a
>>> a is b
True
```

Ambos objetos apuntan al mismo lugar, por tanto:

```python
>>> b[0] = 99  # afectará también al objeto a
>>> a
[99, [11, 22, 33], [111, 222, 333]]
```

Si queremos que cada variable sea una referencia a un objeto distinto, debemos hacer copias del objeto original.

#### *Shallow copy* / *deep copy*

Siguiendo con el ejemplo anterior, y una vez definido 'a', podemos hacer una *shallow copy* en 'b', de varias formas distintas:
- `b=a[:]` - si el objeto *sliced* está a la izquierda del operador asignación, no se crea una *shallow copy*, sino que sirve solamente para manipular los elementos de la lista a los que se refiere, como hemos visto antes.
- `b=list(a)`
- `b=a.copy()`
- `b=copy.copy(a)` (se debe importar el módulo ***copy*** de la biblioteca estándar).

Después de ejecutar una de estas acciones:

```python
>>> a is b
False
```

Ahora ***a*** y ***b*** son objetos distintos. Sin embargo al ser una copia superficial, a pesar de ser dos listas en zonas distintas de memoria, comparten las referencias a sus elementos. Si esos elementos son mutables, entonces cambiar su contenido en uno de ellos afectará al otro objeto.

```python
>>> a = [[1, 2, 3], [11, 22, 33], [111, 222, 333]]
>>> b = a[:]
>>> a is b
False
>>> b[0]=99  # no afectará al objeto a
>>> a
[[1, 2, 3], [11, 22, 33], [111, 222, 333]]
>>> a[0] is b[0]
True
>>> a[1] is b[1]
True
>>> a[2] is b[2]
True
>>> b[1][0]=99  # sí afectará al 2º elemento de a
>>> a
[[1, 2, 3], [99, 22, 33], [111, 222, 333]]
```

Si lo que queremos es hacer una copia profunda (*deep copy*), solo hay una forma:

```python
>>> b = copy.deepcopy(a)
>>> a is b
False
>>> a[0] is b[0]
False
```

A no ser que el elemento sea inmutable, en cuyo caso puede devolver ***True***. Esto se ve fácilmente con este ejemplo:

```python
>>> a = 33
>>> b = 33
>>> a is b  # ambos objetos apuntan al hash del número 33
True
```

### 3.2 First Steps Towards Programming

```python
>>> # Fibonacci series:
... # the sum of two elements defines the next
... a, b = 0, 1
>>> while b < 10:
...     print(b)
...     a, b = b, a+b
...
1
1
2
3
5
8
```

Queda presentada la sentencia `while`.

En el código podemos ver el *multiple assignment*, la forma de agrupar sentencias en *Python*, mediante la indentación, y el uso de `print()`, que toma un número indeterminado de parámetros, y los imprime separados por un espacio y al final imprime un salto de línea (a no ser que le demos el argumento `end=','`).

Las expresiones de asignación múltiple a la derecha del operador asignación (`=`) se evalúan de izquierda a a derecha. Solo cuando están todas evaluadas empiezan a asignarse a las variables a la izquierda del `=`.

En cuanto a la evaluación de condiciones, cualquier entero distinto de 0 es verdadero (***True***), mientras que cero es falso (***False***), como en *C*. Cualquier secuencia con más de cero elementos da verdadero, mientras que si está vacía da falso.

## 4. MORE CONTROL FLOW TOOLS

### 4.1 if Statements

```python
>>> x = int(input("Please enter an integer: "))
Please enter an integer: 42
>>> if x < 0:
...     x = 0
...     print('Negative changed to zero')
... elif x == 0:
...     print('Zero')
... elif x == 1:
...     print('Single')
... else:
...     print('More')
...
More
```

Puede haber 0 o más secciones `elif`, y 0 o 1 secciones `else`.

### 4.2 for Statements

La sentencia `for` itera sobre una secuencia:

```python
>>> a = ['cat', 'window', 'defenestrate']
>>> for x in a:
...     print(x, len (x))
...
cat 3
window 6
defenestrate 12
```

Un código que modifique la secuencia original dentro de las iteraciones, puede ser muy difícil de depurar. En ese caso es mejor iterar sobre una copia de la secuencia o crear una nueva para realizar las operaciones.

Veamos un ejemplo con un diccionario (se verá este concepto más adelante):

Itera sobre una copia:

```python
>>> for user, status in users.copy().items():
...     if status == 'inactive':
...         del users[user]
```

Usa una secuencia nueva:
```python
>>> active_users = {}  # el nuevo diccionario
>>> for user, status in users.items():
...     if status == 'active':
...         active_users[user] = status
```

### 4.3 The range() Function

Genera una secuencia con una progresión aritmética:

`range(10)` genera los valores 0, 1, 2, 3, 4, 5, 6, 7, 8, 9.

`range(5,10)` genera los valores 5, 6, 7, 8, 9.

`range(0,10,3)` genera los valores 0, 3, 6, 9 (*step* 3).

`range(-10,-100,-30)` genera los valores -10, -40, -70.

Es un iterable, pero no crea los elementos en memoria, ahorrando espacio. Para crear una lista a partir de cualquier iterable, se usa la función `list()` (construye una lista a partir de un iterable):

```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 4.4 break and continue Statements, and else Clauses on Loops

`break` sale del *enclosing* `while` o `for`.

`continue` pasa a la siguiente iteración.

El bucle `while` y `for` pueden tener un `else`, que se ejecutará después del bucle, pero no se ejecuta si salimos con `break`. Es similar a la cláusula `else` de la sentencia
`try`, que se ejecuta solo si no se produce una excepción.

### 4.5 pass Statements

La sentencia `pass` no hace nada: para bucles infinitos (vacíos), clases vacías, o *placeholder* de una función todavía no implementada, etc.

### 4.6 Defining Functions

```python
>>> def fib(n):  # write Fibonacci series up to n
...     """Print a Fibonacci series up to n."""    # docstring
...     a, b = 0, 1
...     while a < n:
...         print(a, end=' ')
...         a, b = b, a+b
...     print()
...
>>> fib(2000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597
```

Definición de la función, con nombre y lista de *formal parameters*. En la llamada, se denominan *arguments* o *actual parameters*.

La *docstring* (literal *string* como primera sentencia de la función) es opcional. Es buena práctica: algunas herramientas la usan para generar documentación.

Durante la ejecución de la función, se crea una nueva tabla de nombres local. **Todas** las asignaciones (con `=`) se hacen en la tabla local. Cuando se hace *referencia* a un nombre (de función u otro tipo de objeto), se busca primero en esa tabla local, luego en la de la
*enclosing function*, (*nesting allowed*), etc., hasta la tabla global; finalmente busca en los *builtin names*. Una *asignación* se hace directamente en la tabla local. Así, no podemos asignar valor a una variable global desde una función (a no ser que declaremos el nombre como `global` para variable global, o `nonlocal` para variable en una *enclosing function*), pero sí usar su valor.

Al pasar una variable como argumento, se pasa el valor de esa variable; como ese valor es, de hecho un ID, es decir, una referencia al objeto, en la práctica es un paso por referencia: los cambios hechos al objeto (siempre que sea mutable) en la función son visibles al *caller*. En todo caso, el valor de esta referencia se almacena en la tabla local de la función mientras esta se ejecuta. A parte de poder cambiar el valor del objeto referenciado por este parámetro, también podríamos cambiar el valor de la variable, es decir hacer que referenciara a otro objeto (esto no afectaría a la variable del módulo llamador).

La definición de una función almacena al identificador de la misma en la tabla de nombres actual (como si fuese una asignación). Es decir, el identificador es un apuntador a un objeto función.

```python
>>> fib
<function fib at 10042ed0>
>>> f = fib
>>> f(100)  # o fib(100)
0 1 1 2 3 5 8 13 21 34 55 89
```

Todas las funciones retornan un valor. Si no tiene sentencia `return` (o vuelve con `return` sin argumento), devuelve ***None*** (valor *built-in*).

La sintaxis es `return <valor>`.

### 4.7 More on Defining Functions

#### 4.7.1 Default Argument Values

En la definición, no puede haber un *default parameter* (con valor por defecto) antes de un *non-default parameter*.

```python
def ask_ok(prompt, retries=4, complaint='Yes or no, please!'):
```

En este caso podemos pasar 1, 2 o 3 argumentos en la llamada. Si le damos 2, irá asignando en orden, es decir, el segundo argumento se asignará a ***retries***.

Los valores por defecto de los parámetros pueden ser constantes o variables, que deben estar definidas en el *scope* donde realizamos la definición de la función.

El valor por defecto se evalúa una sola vez. Si ese valor es mutable, mantendrá las modificaciones entre llamadas. Para solucionarlo:

```python
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```

Inicializamos la variable cada vez que no le pasamos valor, en el resto de ocasiones mantiene su valor; lo hacemos en lugar de:

```python
def f(a, L=[]):
    L.append(a)
    return L
```

Que iría añadiendo valores a la lista vacía inicial o la dejaría igual.

#### 4.7.2 Keyword Arguments

```python
def parrot(voltage, state='stiff', action='voom', type='Bluish'):
```

**En la llamada**, podemos usar *keyword arguments* ('nombre=valor') o *positional arguments* o una mezcla de ambos, pero **primero van los positional arguments y luego los keyword arguments** (la posición de estos últimos entre sí no es importante). Hay que tener en cuenta que usemos el modo que usemos, debemos dar valor a todos los *non-default parameters*. Llamadas válidas:

```python
parrot(1000)
parrot(action = 'VOOOOOM', voltage = 1000000)
parrot('a thousand', state = 'pushing up the daisies')
parrot('a million', 'bereft of life', 'jump')
```

Estas son incorrectas:

```python
parrot()    # voltage missing
parrot(voltage=5.0, 'dead')    # non-keyword following keyword
parrot(110, voltage=220)    # duplicate voltage
parrot(actor='John Cleese')    # what's 'actor'?
```

**En la definición**, se puede incluir opcionalmente un *final formal parameter*, del tipo ***\*\*parm*** (tiene que ser el último de todos los parámetros), el cual recoge *en un diccionario* todos los *keyword arguments* que se pasen en la llamada y que no correspondan a ningún parámetro de la definición. El orden en el que estarán en el diccionario será el mismo que en la llamada.

De forma similar, también podemos incluir *en la definición* un parámetro del tipo ***\*parm***, que recoge *en una tupla* todos los *non-keyword arguments* (en la llamada) que no correspondan a ningún parámetro de la definición.

Si hay un parámetro **definido** después de ***\*parm***, no puede recibir valor en la llamada a través de un *positional argument*, ya que todos estos irán a parar a ***\*parm***. Si dicho parámetro es un *default parameter*, obtendrá siempre su valor por defecto, lo cual es un poco absurdo; y si no es *default parameter*, recibiremos error de ejecución, ya que no será posible dar valor a un *keyword-only argument*. Por lo tanto, todos los parámetros definidos después de ***\*parm*** deben *llamarse* como *keyword arguments*.

#### 4.7.3 Special parameters

Los argumentos que pasamos a una función son, por defecto, del tipo *positional-or-keyword*, significando que podemos dar valor a los parámetros utilizando argumentos posicionales o con nombre. Pero es posible restringir eso.

Hay dos tipos de parámetros formales que no se corresponden con un parámetro (no se utilizan en la llamada), pero son indicaciones de este extremo.

Si se define el parámetro ***/***, todos los argumentos anteriores solo pueden pasarse posicionalmente, es decir, son *positional-only*.

Por otro lado, si existe el parámetro ***\****, los argumentos posteriores son *keyword-only*.

Si ambos están definidos, los argumentos entre ellos son *positional-or-keyword*.

Por ejemplo, si definimos:

```python
def foo(name, **parms):
    pass
```

Esta llamada dará error:

```python
foo('Pepe', name = 'Pep')
```

Pero si la definimos así:

```python
def foo(name, /, **parms):
    pass
```

La llamada ya no da error:

```python
foo('Pepe', name = 'Pep')
```

Ahora, el parámetro posicional ***name*** recibe el valor ***'Pepe'***, y el siguiente argumento ya no se refiere al parámetro posicional ***name***, con lo que irá a ***\*\*parms***.

#### 4.7.4 Arbitrary Argument List

El parámetro formal ***\*parm*** puede utilizarse para definir funciones con un número arbitrario de parámetros (deben pasarse como posicionales; si queremos también argumentos arbitrarios con nombre, deberemos usar también un parámetro de tipo ***\*\*parm***).

#### 4.7.5 Unpacking Argument Lists

Se utiliza en la *llamada* a una función.

Podemos utilizar listas o tuplas como una secuencia de argumentos *non-keyword*, utilizando el operador ***\****. Sea ***L*** una lista o tupla de 3 elementos, entonces, `foo(*L)` le pasa 3 *non-keyword* arguments a ***foo***.

Análogamente, se pueden usar diccionarios para desempaquetar sus elementos como argumentos con nombre, usando el operador `**`. Si ***D*** es un diccionario con 5 elementos, `foo(**D)` le pasa 5 *keyword arguments* a ***foo***.

Se pueden combinar ambos métodos, incluso varias veces, en una llamada.

#### 4.7.6 Lambda Expressions

Es una manera de crear objetos función de forma muy simple sintácticamente. Son pequeñas funciones sin nombre. Debe limitarse a una sola expresión, y es mucho más sencillo de definir que con `def`.

```python
foo = lambda x, y: 3*(x + y)
```

Equivale a:

```python
def foo(x, y):
    return 3*(x + y)
```

#### 4.7.7 Documentation Strings

Convenio sobre las *docstrings*: *triple-quoted*; primera línea, un breve sumario de la función, sin incluir nombres; si hay más cosas, dejar la segunda línea en blanco; descripción (*calling convention*, *return value*, *side effects*, etc.).

#### 4.7.8 Function Annotations

Información (metadatos) completamente opcional sobre cada parámetro y sobre el valor de retorno.

Su efecto es básicamente el de almacenar esos metadatos en el atributo ***\_\_annotations__*** de la función, como diccionario.

Podemos darle la información que queramos, el compilador no lo usa para nada, lo pueden usar aplicaciones de terceros.

Podemos, por ejemplo, indicar el tipo de cada parámetro, el tipo de retorno, añadir información sobre restricciones, etc.

La información del tipo de parámetro se define mediante '***:***', después del nombre del parámetro y antes de su valor por defecto (si lo tiene). La del tipo de retorno se define mediante '***->***'. En todo caso, se debe escribir una sola expresión para cada uno, con la
expresión que dé la información que estimemos oportuna.

```python
def f(pr:'precio/kg - int', gr:int=100) -> 'precio total, int':
    return pr * gr / 1000
print(f(45))
print(f.__annotations__)

4.5
{'pr': 'precio/kg - int', 'gr': <class 'int'>, 'return': 'precio total, int'}
```

En caso de incluir esta información, no es necesario definirla para cada parámetro y/o el valor de retorno.

### 4.8 Intermezzo: Coding Style

El estilo de *Python* definido en el *PEP 8* se toma como oficial:
- Identación de 4 espacios sin tabuladores.
- Líneas de máximo 79 caracteres.
- Uso de líneas en blanco para separar funciones y grandes bloques de código dentro de ellas.
- Cuando sea posible incluir comentarios en su propias líneas.
- Uso de *docstrings*.
- Usar espacios alrededor de operadores y después de coma, pero no directamente entre los paréntesis: `a = f(1, 2) + g(3, 4)`.
- Nombrar clases y funciones consistentemente: la convención es ***CamelCase*** para clases y ***lower\_case\_with\_underscores*** para funciones y métodos. Usar `self` como nombre del primer argumento de un método.
- Usar codificaciones estándar si vas a compartir código. *UTF-8* va bien, o incluso *ASCII* puro y duro.
- No usar caracteres *non-ASCII* si alguien de otro idioma que el tuyo va a tener que ver el código.

## 5. DATA STRUCTURES

### 5.1 More on Lists

Las listas tienen también los siguientes métodos (sea 'a' una lista):
- `a.append(x)` - añade un elemento al final de la lista; equivale a `a[len(a):]=[x]`.
- `a.extend(L)` - añade elementos de cualquier iterable ***L***; equivale a `a[len(a):]=L`.
- `a.insert(i,x)` - inserta antes del elemento con índice 'i'; si 'i' es igual a `len(a)`, equivale a `append()`. Si es 0, inserta por delante.
- `a.remove(x)` - elimina el primer elemento cuyo valor es 'x'. Levanta excepción ***RaiseError*** si no hay ninguno.
- `a.pop([i])` - devuelve el elemento con índice 'i' y lo elimina de la lista; si no especificamos índice 'i' (opcional), usa el último elemento de la lista.
- `a.clear()` - elimina todos los elementos de la lista. Equivale a `del a[:]`.
- `a.index(x [,start [,end]])` - devuelve el índice del primer elemento con valor 'x'. Los parámetros 'start' y 'end', en numeración de *slice*, limitan la búsqueda a ciertas posiciones. El resultado es siempre relativo al principio absoluto.
- `a.count(x)` - devuelve cuántos elementos iguales a 'x' hay.
- `a.sort(key=None, reverse=False)` - ordena los elementos.
- `a.reverse()` - invierte el orden de los elementos.
- `a.copy()` - devuelve una *shallow copy* de la lista; equivale a `a[:]`.

#### 5.1.1 Using Lists as Stacks

Combinar `append()` con `pop()`.

#### 5.1.2 Using Lists as Queues

Mientras que `append()` y `pop()` son eficientes, eliminar por el principio no lo es, ya que se tienen que desplazar una posición todos los elementos a partir del segundo. Para ello es mejor usar el tipo ***collections.deque***, especial para colas, ya que tiene un método `popleft()`.

#### 5.1.3 List Comprehensions

Modo de crear listas a partir de secuencias o iterables. *Brackets* encerrando una expresión seguida por una cláusula `for`, seguida por cero o más cláusulas `for` y/o `if`. El resultado es una nueva lista resultado de evaluar la expresión según los valores que va
tomando.

```python
>>> [(x, y) for x in [1, 2, 3] for y in [3, 1, 4] if x != y]
[(1, 3), (1, 4), (2, 3), (2, 1), (2, 4), (3, 1), (3, 4)]
```

En el ejemplo, obtenemos una lista de tuplas de 2 elementos.

Cuando hay más de un `for`, se anidan en el orden que aparecen escritos, siendo el primero de todos el más exterior.

La expresión inicial, que marca la forma general de cada elemento, puede ser a su vez una lista:

```python
>>> [[x, x ** 2, x ** 3] for x in range(10) if x==(x // 2) * 2]
[[0, 0, 0], [2, 4, 8], [4, 16, 64], [6, 36, 216], [8, 64, 512]]
```

Aplanar una lista:

```python
>>> vec = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> [num for elem in vec for num in elem]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Se entendería así: para cada elemento en ***vec***, hacer: para cada número en el elemento, hacer: devolver ***num*** (expresión inicial).

#### 5.1.4 Nested List Comprehensions

La expresión puede ser incluso una *list comprehension* a su vez. Un modo de trasponer filas/columnas de una matriz:

```python
>>> matrix = [
...     [1, 2, 3, 4],
...     [5, 6, 7, 8],
...     [9, 10, 11, 12] ]
>>> [ [row[i] for row in matrix] for i in range(4)]
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```

### 5.2 The del statement

Como método `pop()`, pero no devuelve valor (`del a[N]`), y además puede eliminar un *slice* (`del a[2:4]`), o la lista entera (`del a[:]`), o eliminar la variable (`del a`).

## 5.3 Tuples and Sequences

A parte de los *strings* y listas, hay otros tipos de *secuencias*. Uno de ellos es la *tupla*.

```python
>>> t = 12345, 67.5, 'hello'
>>> t[0]
12345
>>> t
(12345, 54321, 'hello!')
>>> # Pueden ser anidadas:
... u = t, (1, 2, 3, 4, 5)
>>> u
((12345, 54321, 'hello!'), (1, 2, 3, 4, 5))
>>> # Son inmutables:
... t[0] = 88888
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> # Pero pueden contener objetos mutables:
... v = ([1, 2, 3], [3, 2, 1])
>>> v
([1, 2, 3], [3, 2, 1])
```

Los paréntesis no son obligatorios al *definir* la tupla. Aunque en muchos lugares, como en la expresión de una *list comprehension*, se deben incluir. En todo caso, si deseamos definir una tupla de 0 elementos, deberemos incluirlos obligatoriamente. Para una tupla de 1 elemento hay que incluir una *trailing comma*, ya que aunque incluyamos los paréntesis, lo tomará como una expresión de un solo elemento.

```python
>>> empty = ()
>>> singleton = 'hello',  # obsérvese la trailing comma
>>> len(empty)
0
>>> len(singleton)
1
>>> singleton
('hello',)
```

*Packing*/*unpacking* data:

```python
>>> t = 12345, 54321, 'hello!'  # packing
>>> x, y, z = t  # unpacking
```

Las tuplas se suelen usar para este *packing*/*unpacking*, aunque también se puede hacer con listas.

### 5.4 Sets

Los *sets* no tienen elementos duplicados. Se crean con `set()` o mediante llaves ***{}***. Se pueden hacer operaciones como unión, intersección, diferencia o diferencia simétrica.

```python
>>> basket = {'apple', 'orange', 'apple', 'pear', 'orange', 'banana'}
>>> print(basket)  # se han eliminado los duplicados
{'orange', 'banana', 'pear', 'apple'}
>>> 'orange' in basket  # test rápido de pertenencia al conjunto
True
>>> 'crabgrass' in basket
False
```

Admiten operaciones matemáticas de conjuntos:

```python
>>> a = set('abracadabra')  # equivale a set(['a', 'b', 'r', 'a', 'c', 'a', 'd', 'a', 'b', 'r', 'a'])
>>> b = set('alacazam')
>>> a
{'a', 'r', 'b', 'c', 'd'}
>>> a - b # letras en a pero no en b
{'r', 'd', 'b'}
>>> a | b # letras en a o b
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}
>>> a & b # letras in ambos conjuntos a la vez
{'a', 'c'}
>>> a ^ b # letras en a o b pero no en ambos
{'r', 'd', 'b', 'm', 'z', 'l'}
```

También se pueden hacer *set comprehensions* (elimina los repetidos):

```python
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'r', 'd'}
```

### 5.5 Dictionaries

Es un *set* de pares *key:value*. El acceso al diccionario es por la clave (*key*), y no por número de índice. Las claves deben ser de tipo inmutable y deben ser únicas (no podemos repetir una clave). Por lo tanto, podríamos usar como clave cosas como un número, un *string*, o incluso una tupla (siempre y cuando no contuviera elementos mutables).

El diccionario vacío se puede definir mediante ***{}***.

Añadir un valor mediante una clave ya existente, simplemente actualiza el *value* correspondiente a esa clave. Mediante `del` podemos eliminar uno de los pares.

Si tenemos un diccionario d, haciendo `list(d)` obtenemos una lista con las claves (con el orden en que fueron insertadas en su momento). Si lo queremos ordenado, lo haremos mediante `sorted(d)`.

Para saber si una clave existe en el diccionario se puede mirar mediante `'clave' in d`.

```python
>>> tel = {'jack': 4098, 'sape': 4139}
>>> tel['guido'] = 4127
>>> tel
{'jack': 4098, 'sape': 4139, 'guido': 4127}
>>> tel['jack']
4098
>>> del tel['sape']
>>> tel['irv'] = 4127
>>> tel
{'jack': 4098, 'guido': 4127, 'irv': 4127}
>>> list(tel)
['jack', 'guido', 'irv']
>>> sorted(tel)
['guido', 'irv', 'jack']
>>> 'guido' in tel
True
>>> 'jack' not in tel
False
```

Se puede usar el constructor `dict()` para crear un diccionario, pasándole una secuencia que contenga a su vez secuencias de dos elementos (clave y valor):

```python
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'jack': 4098, 'guido': 4127}
```

Aunque el constructor también acepta parámetros con nombre, cuyos nombres convierte en *strings* para actuar como claves:

```python
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'guido': 4127, 'jack': 4098}
```

Se pueden crear *dict comprehensions*:

```python
>>> {x: x ** 2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
```

### 5.6 Looping Techniques

Método `items()` de un *diccionario* devuelve lista de parejas *key*:*value*:

```python
>>> knights = {'gallahad': 'the pure', 'robin': 'the brave'}
>>> for k, v in knights.items():
... print(k, v)
...
gallahad the pure
robin the brave
```

El método `enumerate()`, de cualquier *secuencia*, itera sobre la misma, devolviendo parejas formadas por el número de índice y el valor.

Se pueden combinar 2 secuencias con la *built-in function* `zip()`, de tal modo que el elemento N del *zip* será una pareja formada por el elemento N de la primera secuencia y el elemento N de la segunda secuencia.

`reversed()` devuelve una secuencia al revés, mientras `sorted()` la devuelve ordenada. No alteran el objeto original.

Recordemos que es una mala idea modificar una lista mientras se está iterando sobre ella.

### 5.7 More on Conditions

Operadores de comparación `in` y `not in`, miran existencia de un elemento. Operadores `is` e `is not`, miran si los objetos comparados son en realidad el mismo. Los operadores de comparación tienen todos la misma prioridad, y menor prioridad que todos los operadores numéricos.

Si encadenamos comparaciones, como por ejemplo `a < b == c < d`, equivale a `a < b and b == c and c < d`.

Se puede usar `and`, `or` y `not`; `not` tiene la máxima prioridad, y `or` la mínima de los tres. Estos tienen menor prioridad que los operadores de comparación. Operadores `and` y
`or` se evalúan de izquierda a derecha, y cortocircuitan. El resultado de una expresión lógica se puede asignar a una variable. No siempre devolverán ***True*** o ***False***.

```python
>>> 5 and not 4
False
>>> 6 and 3
3
>>> 6 or 3
6
>>> 0 or not 5
False
>>> 0 or 5
5
>>> 0 and True
0
```

No se puede utilizar el operador de asignación (como en *C*) en medio de una expresión. Pero sí se puede hacer como en *C* utilizando en su lugar el operador morsa `:=`. La subexpresión devuelve el valor asignado.

```python
>>> a = 3 + ( b = 5)
  File "<stdin>", line 1
    a=3+(b=5)
          ^
SyntaxError: invalid syntax
>>> a=3+(b:=5)
>>> a
8
```

### 5.8 Comparing Sequences and Other Types

Se pueden comparar entre sí secuencias *del mismo tipo*. Si son de distinto tipo pero existen mecanismos de comparación, también. Se comparan desde el primer elemento hasta el último (recursivamente si hay subsecuencias) hasta encontrar una diferencia.

Ejemplos que devuelven verdadero:

```python
(1, 2, 3) < (1, 2, 4)
[1, 2, 3] < [1, 2, 4]
'ABC' < 'C' < 'Pascal' < 'Python'
(1, 2, 3, 4) < (1, 2, 4)
(1, 2) < (1, 2, -1)
(1, 2, 3) == (1.0, 2.0, 3.0)
(1, 2, ('aa', 'ab')) < (1, 2, ('abc', 'a'), 4)
```

## 6. MODULES

Un módulo (del tipo ***nombre.py***, es decir, se trata de un *script*) se puede importar en otro módulo o en el módulo principal (módulo de nivel superior o intérprete interactivo), mediante `import <nombre_módulo>`.

Al importar un módulo no inserta los nombres de las funciones allí definidas en la tabla de nombres actual, sino solo el nombre del módulo. Se puede ver como un objeto módulo en la tabla de nombres actual. A través de ese objeto podemos acceder a las funciones así: `modulo.foo()`. Aunque podemos insertar una función en la tabla actual así: `fun = modulo.foo`.

Uno de los atributos del objeto módulo es la variable ***\_\_name__***, que simplemente guarda el nombre del módulo. ***\_\_name__*** a secas es el nombre del módulo actual, y
***modulo.\_\_name__*** es el nombre del módulo ***modulo*** (que es precisamente ***'modulo'***).

### 6.1 More on Modules

Al importar el módulo con la sentencia `import`, se ejecutan todas las sentencias del mismo. No se vuelven a ejecutar más tarde (una sentencia `def` define una función, una sola vez es suficiente; lo mismo para clases etc.).

El módulo se puede ejecutar también como *script* si así se le invoca. Las variables definidas en un módulo se guardan en la tabla particular del módulo, que, vista desde el interior del módulo, es una tabla global (no hay tablas superiores visibles); los objetos de este, vistos desde el código que lo importa, son accesibles mediante ***modulo.var***.

Un módulo (o *script*) puede importar otros módulos. Cada módulo tiene su tabla global, donde coloca los nombres de los módulos que importa. A parte, cada función del módulo tendrá su tabla local.

```python
>>> from fibo import fib, fib2
```

Esto añade los nombres (objetos) especificados directamente en la tabla del módulo importador, sin añadir el nombre del módulo importado en ella.

```python
>>> from fibo import *
```

Esto último importa todas las definiciones menos las empezadas por '***\_***'. No se debería usar este tipo de importación, porque importa a la tabla local nombres desconocidos, que podrían ocultar los nuestros.

Para modificar los nombres en el momento de añadirlos a nuestra tabla, podemos usar:

```python
import <módulo> as <minombre>
```

En este caso, el nombre importado será ***minombre***, y contendrá el módulo original. Para modificar el nombre de los elementos del módulo al importarlos:

```python
from <módulo> import <foo> as <mifun1>, <fee> as <mifun2>
```

Y así tantas como queramos.

Para recargar un módulo (ya que si no solo lo importa/ejecuta una vez):

```python
import importlib
importlib.reload(nombremodulo)
```

#### 6.1.1 Executing modules as scripts

Desde el *shell*:

```python
python m1.py <argumentos>
```

Cuando un módulo se está ejecutando en el nivel superior, el valor de ***\_\_name__*** es ***'__main\_\_'***. Este es el modo de saber si nuestro módulo está funcionando como un *script* invocado directamente (nivel superior), o está siendo importado para que otro módulo utilice sus funciones.

#### 6.1.2 The Module Search Path

Al importar un módulo (o elementos de un módulo), *Python* busca primero el nombre del mismo en los *built-in modules*, si no lo encuentra, en los lugares definidos en la variable ***sys.path*** (modificable), que, por defecto indica los valores siguientes:
- Directorio que contiene el *script*, o directorio desde el que se ha llamado al intérprete interactivo.
- Variable del sistema ***PYTHONPATH*** (mismo formato que ***PATH***).
- Otros (dependiendo de la instalación de *Python* concreta).

#### 6.1.3 "Compiled" Python files

Al importar un módulo ***m1***, *Python* lo «compila» (codifica, *platform-independent*) y guarda ese archivo compilado en el directorio ***\_\_pycache__*** (dentro del directorio donde se encuentra el módulo), con nombre ***m1.ver.pyc***, donde 'ver' es la versión de *Python* que lo compiló. Así, pueden coexistir varias implementaciones/versiones de *Python* en el sistema.

Cada vez que importamos un módulo, si el archivo ***.py*** es más nuevo que el ***.pyc*** (o el ***.pyc*** no existe), lo recompila; si no, lee el archivo compilado, que es más rápido de cargar que el archivo de texto, sin afectar nada en la velocidad de ejecución después de cargarse.

Al ejecutar un módulo como *script*, no compila en el cache. Se puede importar un módulo que exista solo en forma compilada, aunque solo si no existe el archivo fuente, y el archivo compilado está en el directorio fuente (*Python* no mira en el directorio *cache* si no está el archivo fuente).

### 6.2 Standard Modules

*Python* viene con una biblioteca de módulos estándar.

### 6.3 The dir() Function

Esta función *built-in* muestra los nombres definidos (funciones, variables, módulos,...) en un módulo concreto. Sin parámetros, muestra los nombres de la tabla del módulo actual. Con ***dir(\<módulo>)*** vemos los nombres definidos en el módulo específico (cuyo nombre debe estar en la tabla actual).

No muestra los nombres *built-in*. Para ello tendríamos que hacer:

```python
import builtins
dir(builtins)
```

### 6.4 Packages

Podemos estructurar el *namespace* de los módulos, agrupándolos jerárquicamente en *packages*. Para acceder a ellos prefijaremos los nombres de los módulos pertinentes separados por punto. Cada submódulo tendrá su propia tabla de nombres, independientemente de los demás módulos del *package*. Así, ***m1.m2*** indica el módulo ***m2*** del *package* ***m1***.

Así, un *package* se organiza en un directorio donde se incluirán todos sus módulos (archivos ***.py***) y/o *subpackages* (subdirectorios con más módulos). El nombre del directorio es el que define el nombre del *package* (o *subpackage*).

La búsqueda del *package* hace igualmente como se indica en ***sys.path***. Para que al importar *Python* trate a esa carpeta como un *package*, esta debe contener un archivo ***\_\_init__.py*** (o una versión compilada del mismo). Si no existe tal archivo, el directorio es una simple carpeta de archivos, nada que ver con los *packages*. Es suficiente la presencia de ***\_\_init.py__*** para que la carpeta sea considerada *package*, aunque el archivo esté en blanco.

Si incluimos código en ***\_\_init__.py***, al importar el paquete se ejecuta (código de inicialización del paquete).

Veamos una posible estructura de directorios definiendo un paquete de utilidades de sonido:

```
sound/                 (paquete nivel superior)
    __init__.py        (inic. paquete sonido)
    install.py
    unistall.py
    formats/           (subpaquete convers. formato)
        __init__.py    (inic. subpaquete)
        wavread.py
        wavwrite.py
        aiffread.py
        aiffwrite.py
    effects/           (subpaquete efectos)
        __init__.py    (inic. subpaquete)
        echo.py
        surround.py
        reverse.py
    filters/           (subpaquete filtros)
        __init__.py    (inic. subpaquete)
        equalizer.py
        vocoder.py
        karaoke.py
```

En este ejemplo, el paquete se llama 'sound' (***import sound***), y contiene dos módulos (***install.py***, ***uninstall.py***) y tres subpaquetes (***formats***, ***effects*** y ***filters***), los cuales contienen varios módulos cada uno.

Ejemplos:

```python
import sound.effects.echo    # importa el módulo 'echo.py'
```

Para llamar a una posible función ***foo()*** deberemos usar el nombre completo:

```python
sound.effects.echo.foo()
```

Podemos importar lo mismo así:

```python
from sound.effects import echo
```

Pero en este caso, aunque también importa el módulo ***echo.py***, se puede llamar a la función así:

```python
echo.foo()
```

También podemos hacer:

```python
from soud.effects.echo import foo
```

Y entonces podemos llamar así: `foo()`.

Al hacer: `from <package> import <item>`, ***item*** puede ser un *package*, un módulo, o cualquier nombre definido en el *package* (como una función, una variable o una clase). *Python* intenta primero averiguar si es un nombre definido en el código del *package* (en ***\_\_init__.py***); si no lo encuentra, asumirá que es un *subpackage* (o submódulo); si
tampoco, dará error. En cambio, al hacer ***import item.subitem.subsubitem***, todos los nombres deben ser *packages*, excepto el último que puede ser un *package* o un módulo.

### Notas personales

En los archivos ***\_\_init__.py*** y en los módulos que forman parte de un paquete se puede incluir cualquier código que estimemos oportuno. Allí se puede incluir código de inicialización del paquete o módulo, importar otros elementos, definir funciones, clases, variables, etc.

A la hora de importar elementos de un *package*, debemos hacerlo siguiendo la jerarquía de nombres, desde el paquete raíz, separándolos por puntos. El directorio donde está el paquete raíz debe estar referenciado en ***sys.path***. En nuestro caso, por ejemplo:
- ***sound*** (paquete raíz)
- ***sound.foo*** (función en paquete raíz)
- ***sound.uninstall*** (módulo)
- ***sound.uninstall.foo*** (función en módulo)
- ***sound.effects*** (paquete)
- ***sound.effects.foo*** (función en paquete)
- ***sound.effects.echo*** (módulo)
- ***sound.effects.echo.foo*** (función en módulo)

Podemos importar un módulo o paquete sin utilizar la sintaxis de jerarquía de nombres, o tomando cualquier otra carpeta como paquete raíz, por ejemplo ***import echo***, o ***import effects***, siempre y cuando el directorio del módulo o paquete en cuestión esté referenciado en las rutas de búsqueda estándar (***sys.path***).

#### Importar con *import*

Importaremos con ***import <nombre>***, donde ***nombre*** es una secuencia jerárquica de nombres separados por punto. Dichos nombres solo pueden ser nombres de paquetes (en orden jerárquico), a excepción del último que puede ser el nombre de un paquete o de un módulo.

Al importar, se van ejecutando todos los ***\_\_init__.py*** en el orden que los va encontrando, empezando por el del paquete raíz. Si el último nombre es de un módulo, lo ejecuta, después de los ***\_\_init__.py***.

El nombre que se añade a la tabla de nombres es únicamente el del *paquete raíz*. Luego, en el código podremos acceder, a través de ese nombre, a los demás paquetes de la ruta (y al módulo final, si lo hay), así como a todos los elementos (funciones, clases, variables, etc.) definidos en todos ellos.

Por lo tanto, cualquier referencia a un elemento importado mediante una sentencia ***import***, deberá incluir la ruta de nombres completa desde el paquete raíz.

#### Importar con *from-import*

Otro modo de importar es con ***from <nombre> import <elemento>***. Al igual que en el caso anterior, 'nombre' es la ruta de nombres separados por punto. Todos ellos deben ser nombres de paquetes excepto el último, que puede ser nombre de paquete o de módulo. Si el último es
el nombre de un paquete, 'elemento' puede ser el nombre de un subpaquete, módulo, o elemento (función, clase, variable, etc.). Si el último es un nombre de módulo, 'elemento' será forzosamente un nombre de elemento definido en el módulo en cuestión.

Igual que en el caso anterior, también se van ejecutando los ***\_\_init__.py*** (y el posible módulo final) en orden, desde el raíz, hasta el último de los nombres de 'nombre'. La única diferencia es que en lugar de añadirse a la tabla de nombres el nombre del paquete
raíz, se añade directamente el elemento indicado en 'elemento', ya sea este un subpaquete (cuyo ***\_\_init__.py*** se ejecuta en último lugar), un módulo (que se ejecuta en último lugar), o un elemento (definido en un paquete o módulo, cuyo código correspondiente se ejecuta en último lugar).

Si "elemento", que es el nombre que será añadido a la tabla de nombres, es un paquete o módulo, a través de dicho nombre podremos acceder a *todos* los elementos definidos en él. En cambio, si es un elemento (función, clase, etc.) solo podremos acceder a este (y sin
prefijo).

Haciendo la importación con ***from-import***, no podremos acceder a otros elementos de la jerarquía. Si el elemento importado fuese un subpaquete, no habría ningún tipo de acceso ni al paquete padre, ni a posibles subpaquetes, ni incluso módulos que estuvieran en el subpaquete. Los únicos elementos a los que se podría acceder serían los elementos (funciones, clases, etc.) definidos en el subpaquete, prefijándoles el nombre del mismo. Lo mismo sucedería si se tratara de un módulo.

Si quisiéramos que al importarse un paquete estuviesen accesibles otros elementos, como submódulos del paquete, o incluso otros subpaquetes, se deberían incluir esas importaciones en el código del ***\_\_init__.py*** del paquete.

En cuanto al elemento a importar, conviene tener en cuenta que este nunca puede ser una jerarquía de nombres, sino un nombre simple.

Por otro lado, en cuanto a la ejecución de los ***\_\_init__.py*** y módulos, se debe tener en cuenta que los que se hayan ejecutado ya no se vuelven a ejecutar.

#### 6.4.1 Importing * From a Package

Si hacemos ***from <pack> import \**** de un paquete, no se importan submódulos ni *subpackages*, sino únicamente los elementos que estén definidos en el código del paquete (en su ***\_\_init__.py***), aunque también se pueden importar otras cosas explícitamente desde allí.

Sin embargo, podemos definir fácilmente el contenido del *package* mediante la lista de *strings* ***\_\_all__***. Si está definida en ***\_\_init__.py***, se importará lo que la lista especifique, en el orden especificado, en el caso concreto de que hagamos un ***from
<pack> import \****:

```python
__all__ = ['install', 'uninstall', 'formats', 'foo']
```

Podemos incluir nombres de submódulos, subpaquetes y elementos definidos en el mismo paquete (en ***\_\_init__.py***). Cuando ***\_\_all__*** no está incluido, pues, solo se importarán los elementos definidos en ***\_\_init__.py***; sin embargo, si ***\_\_all__*** está definido, dichos elementos no se cargan si no están explícitamente incluidos, uno a uno, en ***\_\_all__*** (siempre hablando en relación a ***from <pack> import \****).

Solo se pueden incluir en ***\_\_all__*** cosas que pertenezcan directamente al paquete (sus submódulos, subpaquetes y elementos), no cosas de niveles inferiores (ni superiores, claro), por lo que todos los *strings* especificados son nombres simples, sin prefijos.

#### 6.4.2 Intra-package References

Podemos hacer referencia a otros paquetes o módulos de la jerarquía del paquete actual en la sentencia ***from-import***. Por ejemplo, desde el paquete ***effects***, podríamos escribir:

```python
from . import echo    # . se refiere al mismo 'effects'
from .. import formats    # .. es el paquete padre
from ..filters import equalizer    # subimos y bajamos
```

Un punto (***.***) es el paquete actual, dos (***..***) el superior, tres (***...***) el superior a este, etc. Tras los puntos, podemos bajar opcionalmente uno o más niveles, hasta ir a parar a un paquete concreto (o módulo). Por ejemplo, desde cualquier archivo de ***effects*** (ya sea desde uno de los módulos o desde dentro de ***\_\_init__.py***):

```python
from ..filters.equalizer import foo    # sube, luego baja 2
```

Este mecanismo no funciona desde los módulos que se estén ejecutando en el nivel superior, y su nombre sea ***\_\_main__***.

#### 6.4.3 Packages in Multiple Directories

Los packages tienen una variable tipo lista ***\_\_path__*** que especifica el directorio donde se encuentra su ***\_\_init__.py*** antes de ejecutarse. Al cambiarla, se cambia el lugar donde se buscarán los submódulos y subpaquetes del paquete actual.

## 7. INPUT AND OUTPUT

### 7.1 Fancier Output Formatting

Para formatear *strings* se puede hacer mediante *strings* con formato (llamadas también *f-strings*):

```python
>>> year = 2016
>>> event = 'Referendum'
>>> f'Results of the {year} {event}'
'Results of the 2016 Referendum'
```

Pueden ser del tipo ***f'...'*** o ***F'...'***, y se puede prefijar a comillas simples, dobles o *triple-quoted strings*. Dentro de las llaves puede ir cualquier expresión válida.

Otra opción es usando el método `format()` de los objetos *string*.

Y una tercera es haciéndolo todo manualmente, con *slices*, concatenaciones, etc.

Las funciones *built-in* `str()` y `repr()` retornan representaciones en *string* del valor que les pasamos, de forma *human-readable* la primera, y con forma de *string* literal comprensible al intérprete la segunda.

```python
>>> s = 'Hello, world.'
>>> str(s)
'Hello, world.'
>>> repr(s)
"'Hello, world.'"
>>> str(1/7)
'0.14285714285714285'
>>> x = 10 * 3.25
>>> y = 200 * 200
>>> s = 'x value is ' + repr(x) + ', and y is ' + repr(y) + '.'
>>> print(s)
x value is 32.5, and y is 40000.
>>> hello='hello, world\\n'
>>> print(hello)
hello, world
>>> print(repr(hello))
'hello, world\\n'
```

Muchos tipos de datos tienen la misma representación con
`str()` que con `repr()`.

#### 7.1.1 Formatted String Literals

Dentro de las llaves, y tras la expresión, se puede tener más control del formato mediante dos puntos (***:***) seguido del especificador de formato. Para más detalles, ver *format specification*.

#### 7.1.2 The String format() Method

Para una descripción detallada del método `format()` de la clase `str`, ver la documentación sobre la biblioteca (*Text Processing Services*).

#### 7.1.3 Manual String Formatting

`print()` muestra un espacio entre los argumentos imprimidos, y un *newline* al final; si especificamos el parámetro ***end***, sustituye ese *newline* por el *string* que especifiquemos.

Los *strings* disponen de los métodos `rjust()`, `ljust()` y `center()` que justifican a la derecha, izquierda o centro, al que debemos pasar el ancho (nunca trunca el *string* original). Los *strings* disponen también del método `zfill(<N>)` (*zero fill*) que rellena un número con ceros (respetando signos correctamente).

#### 7.1.4 Old string formatting

Se pueden formatear los *strings* mediante un método análogo al `sprintf()` de *C*, usando el operador `%`. Este estilo (*old string formatting*) está *deprecated*, y tiende a desaparecer.

### 7.2 Reading and Writing Files

La función `open(<filename> [,mode])` retorna un objeto archivo. El modo puede ser ***'r'*** (*readonly*, *default*), ***'w'*** (*write*, trunca), ***'a'*** (*append*) o ***'r+'*** (*read*/*write*). Si añadimos ***'b'*** al modo, abre en modo binario (no texto). En modo texto, los *platform-specific* *line endings* **leídos** (***\\n*** de *Unix*, ***\\r\\n*** de *Windows*) se convierten internamente a ***\\n***; esos ***\\n*** se convierten, al *escribir*, en *platform-specific line endings*.

Es buena idea usar `with` para manipular archivos, ya que lo cerrará automáticamente una vez haya terminado el trabajo, incluso si se produce una excepción:

```python
>>> with open('workfile') as f:
...     read_data = f.read()
>>> f.closed
True
```

Si lo usamos simplemente con `f=open(...)`, tendremos que cerrar manualmente cuando terminemos, con `f.close()`. El *garbage collector* lo cerraría y liberaría el objeto archivo igualmente, pero no sabemos cuándo.

#### 7.2.1 Methods of File Objects

El método `read()`, lee hasta el final del archivo (*string* o *bytes*, dependiendo del modo de apertura). Con `read(n)` se leen como máximo ***n*** *bytes*. Si ***n*** es negativo es como si no estuviera. La lectura se inicia donde se quedó la anterior. Si se ha llegado al fin de archivo, la siguiente lectura devuelve un *empty string*.

El método `readline()` devuelve la siguiente línea de texto, hasta el ***\\n*** (se conserva en el *string*) o el *end of file*. Si ya se llegó al *EOF* en la anterior lectura, devuelve *empty string*. Una línea en blanco es simplemente el carácter ***\\n***.

Para leer línea a línea un archivo, se puede iterar sobre el objeto archivo:

```python
>>> f=open('archivo.txt')
>>> for linea in f:
...     print(linea)
```

El método `readlines()` devuelve una lista de *strings* con todas las líneas del archivo de texto (también podemos usar `list(f)`).

El método `write(s)` solo acepta *strings* (o *bytes*) como argumento. Escribe ese *string* y devuelve el número de caracteres escrito.

El método `tell()` devuelve un entero con la posición actual dentro del archivo en *bytes* desde el principio (modo binario) o un número opaco, del que no conocemos sus características (modo texto). Para cambiar posición, método `seek(offset, whence=0)`, donde
***whence*** (opcional) es 0 (*beginning of file*, *default*), 1 (*current position*) o 2 (*end of file*). Retorna la posición final.

En los archivos de texto (no binarios) el *seek* sólo se puede hacer relativo al *beginning of file*, a excepción de `seek(0,2)`, que lo deja en el *EOF*, y solo se le pueden pasar valores que devuelve `ftell()`.

#### 7.2.2 Saving structured data with json

Como solo se pueden guardar datos en archivos de texto usando *strings*, un simple valor numérico implica la conversión a y desde *string*. Para guardar estructuras de datos más complejas y jerarquizadas hay que parsear a mano, lo cual se vuelve farragoso. Para ello está el módulo ***json***, que serializa (convierte una estructura de datos en una secuencia *string*) y deserializa (convierte un *string* a una estructura de datos) en el popular formato de intercambio *JSON* (*JavaScript Object Notation*).

También se puede usar el módulo ***pickle***, pero este serializa/deserializa en un formato específico de *Python*, no tan extendido.

## 8. ERRORS AND EXCEPTIONS

### 8.1 Syntax Errors

Errores reportados por el *parser*.

### 8.2 Exceptions

Errores en la ejecución (no en el *parsing*). Existen varios tipos de excepciones *built-in*, aunque se pueden definir nuevos tipos.

### 8.3 Handling Exceptions

Cláusula `try`:

```python
try:
    <sentencias>
except <error1>:
    <sentencias>
except <error2>:
    <sentencias>
```

Esta es la secuencia de acciones:
- Primero se ejecutan las sentencias de la cláusula `try` (de`try` a `except`).
  - Si no se levanta ninguna excepción en toda la cláusula (incluyendo excepciones no tratadas provenientes de funciones a las que hayamos llamado), se salta las cláusulas `except` y la ejecución sigue tras el bloque `try`.
  - Si se levanta excepción, se salta el resto de la cláusula `try` y se empieza a buscar en las cláusulas `except`, en orden de aparición, hasta encontrar la cláusula `except` pertinente.
    - Si la encuentra, la ejecuta entera (suponiendo que no se produzca otra excepción dentro de ella, que no será tratada, a no ser que dentro de esa cláusula `except` exista a su vez un bloque `try`), para luego saltarse el resto de cláusulas `except`.
    - Si no la encuentra, pasa el *handling* de la excepción a un posible bloque `try` más exterior (caso de bloques `try` anidados, o estamos en una función que ha sido llamada desde otro bloque `try`).
      - Si lo hay, se repite allí el proceso.
      - Si no lo hay, se trata de una *unhandled exception* y la ejecución se interrumpe.

La cláusula `except` puede tener como argumento un tipo de excepción, o varios en una tupla, que serán recogidos todos:

```python
except (<error1>, <error2>, <error3>, <error4>):
```

Dado que un tipo de excepción es una clase, una excepción es recogida por una cláusula `except` de ese tipo o de un tipo base. Por ejemplo, si ***ExBase*** es una clase excepción, y ***ExDeriv*** una clase derivada de esta, y definimos nuestras cláusulas `except` en este orden:

```python
except ExBase:
...
except ExDeriv:
...
```

Cada vez que se produzca una excepción del tipo ***ExBase*** o ***ExDeriv***, será recogida por la cláusula `except ExBase`, de tal modo que la cláusula `except ExDeriv` no se ejecutará nunca. Si queremos manejar diferentes excepciones con relaciones jerárquicas, debemos siempre incluirlas desde la más específica e ir ascendiendo por la jerarquía:

```python
except ExDeriv:
...
except ExBase:
...
```

La última cláusula `except` puede omitir el tipo de excepción (`except:`), sirviendo de comodín (resto de tipos de excepción). Se puede usar, por ejemplo, para informar de que no hemos podido manejar la excepción y la pasamos a un `try` exterior (con un `print()` y un `raise` para volver a levantarla).

Después de las cláusulas `except` puede haber opcionalmente una cláusula `else`. Esta se ejecuta solo si la cláusula `try` se ha ejecutado entera sin leventar ninguna excepción (si hay excepción no se ejecuta). Sería la continuación de las sentencias de la cláusula `try`, pero fuera de la protección de `try...except`. Para esto es útil, para que ese tramo no quede protegido por las cláusulas `except`, que serán más específicas para el tramo inicial de `try`. Pero al mismo tiempo, es un código que no queremos que se ejecute si se produce una excepción en el tramo inicial.

Si escribimos por ejemplo `except <error> as err:` podemos acceder al contenido del error mediante la variable ***err***, una instancia de la excepción, de tipo ***error***. Si la excepción se ha levantado con argumentos, podemos acceder a ellos mediante `err.args`. El objeto (instancia) de la excepción concreta también define ***\_\_str__*** que contiene los argumentos, con lo que se pueden imprimir estos sin pasar por ***args***:

```python
>>> try:
...     raise Exception('spam', 'eggs')
... except Exception as inst:
...     print(type(inst))    # la instancia
...     print(inst.args)    # argumentos en .args
...     print(inst)    # __str__ contiene los argumentos
...     x, y = inst.args    # unpack args
...     print('x =', x)
...     print('y =', y)
...
<class 'Exception'>
('spam', 'eggs')
('spam', 'eggs')
x = spam
y = eggs
```

### 8.4 Raising Exceptions

Se hace así:

```python
raise <exc>
```

El argumento de `raise` es una instancia de una excepción o una clase de excepción (clase derivada de la clase ***Exception***), en cuyo caso se creará la instancia usando el constructor sin argumentos. En caso de que se levante una excepción en un bloque `try`, y queremos hacer algo con ella, pero queremos dejarla *unhandled* (o pasarla a un `try` exterior), dentro de la cláusula `except` pertinente hacemos con ella lo que queramos, y al final la re-levantamos con `raise` sin argumentos. Ejemplos:

```python
raise Exception('spam', 'eggs', 10)    # excepción con argumentos
raise NameError    # equivale a raise NameError()
raise    # re-raise la excepción que estamos tratando
```

### 8.5 User-defined Exceptions

Podemos definir tipos de excepciones, derivando la clase ***Exception***. Podemos *override* cosas como ***\_\_str__***, o hacer que el inicializador defina otros atributos en lugar de ***args***, p.e. Sus nombres suelen terminar en ***Error***. Para tratar posibles excepciones en nuestro código concreto.

### 8.6 Defining Clean-up Actions

Otra cláusula opcional es `finally`, que lo que hace es que se ejecuta en cuanto abandonamos el bloque `try...except`, tanto si es porque hemos terminado el `try`, o un `except`, etc., como si es porque hemos salido con `break`, `continue` o `return`. Es decir, cuando hemos terminado, y tocaría ir saliendo del bloque `try`.

Si la excepción ha quedado *unhandled*, o se ha producido una excepción en una cláusula `else` o `except`, tocaría abortar el programa o pasar inmediatamente al `try` exterior; pero si hay cláusula `finally`, se captura momentáneamente la excepción, se ejecuta el `finally`, y después se vuelve a levantar la excepción, produciéndose entonces el *abort*, o el paso al *outer* `try`. Es bueno para liberar recursos (*clean-up actions*) que tengan que hacerse siempre, ya que la ejecución del `finally` está asegurada bajo cualquier circunstancia.

Si se produce una excepción en una sentencia `return`, y queda *unhandled*, normalmente se pasaría al bloque `try` del código que llamó a la función actual (si existe bloque). En este caso, si hay cláusula `finally`, lo que retornará la función a dicho bloque es el valor de la sentencia `return` que incluiremos dentro de la cláusula `finally`.

### 8.7 Predefined Clean-up Actions

Hay objetos que disponen de *predefined clean-up actions*, como los objetos archivo. Esto significa que se pueden usar con la sentencia `with`, de modo que ello nos asegura que los recursos son liberados después de su uso, pase lo que pase:

```python
with open('myfile.txt') as f:
    for line in f:
        print(line)
```

En este caso ***f*** queda cerrado después de su uso, incluso aunque se haya producido una excepción *unhandled* y el programa se interrumpa.

## 9. CLASSES

En *Python*, los miembros de una clase son normalmente públicos, y las funciones miembro son virtuales. Una clase define un nuevo tipo, pero al mismo tiempo, una clase es un objeto en sí (objeto clase, independientemente de sus instancias). Se pueden derivar (extender) los tipos *built-in* también. Los operadores *built-in* pueden redefinirse para las nuevas clases.

### 9.1 A Word About Names and Objects

Aquí se explica el modelo que usa Python en cuanto a las variables: el hecho de que se comporten como apuntadores.

### 9.2 Python Scopes and Namespaces

Un *namespace* es un *mapping* de nombres a objetos, mapeo que se produce en la tabla de nombres: nombres *built-in*, nombres globales del módulo, nombres locales en una función, atributos de un objeto, etc. A su vez, cada *namespace* puede tener su propia tabla, con otros *namespaces* dentro.

Un nombre detrás de un punto es un atributo del objeto «padre»; en ***modname.funcname*** el módulo 'modname' tiene una función ***funcname()***. Se dice que ***funcname*** es un atributo del objeto 'modname'. Los atributos pueden ser de lectura o de lectura/escritura (estos últimos pueden hasta ser borrados con `del`).

El *namespace* de los *built-ins* se crea al iniciarse el intérprete, y permanece hasta que este se cierra. El *namespace* global de cada módulo se crea al cargar el módulo, y persiste hasta que se cierra el intérprete. Esto incluye el módulo principal (***\_\_main__***). El *namespace* de una función se crea en cada llamada a esta, y se elimina tras su ejecución.

El *scope* es la región textual en la que un *namespace* es directamente accesible, es decir, mediante *unqualified names* (sin prefijar el nombre del *namespace*). *Scopes* existentes (en orden de búsqueda, desde *innermost* a *outermost*):
- *Scope* local de la función o clase, el que se busca primero (nombres locales).
- *Scope(s)* de la(s) posible(s) *enclosing function(s)*. Se buscan desde más adentro a más afuera. Sus nombres no son locales, pero tampoco globales.
- Penúltimo *enclosing scope*, con los nombres globales del módulo actual.
- *Outermost scope*: donde están los *built-in names*.

Si una variable está declarada `global` en cualquier *scope*, todas las referencias y asignaciones se hacen en el *scope global del módulo*. Si se declara `nonlocal`, se refiere a una variable de un *scope* exterior al actual (lo más interior posible) exceptuando el global. Si no se especifica, las referencias se buscan de dentro para afuera, pero las asignaciones (y borrados, con `del`) se hacen en el *scope* local, enmascarando posibles variables exteriores con el mismo nombre.

La definición de una clase introduce un nuevo *namespace* dentro del *scope* actual.

Para una función, su *scope* global es el módulo donde está definida, independientemente del alias que se use para invocarla.

#### 9.2.1 Scopes and Namespaces Example

Un ejemplo.

### 9.3 A First Look at Classes

#### 9.3.1 Class Definition Syntax

La definición de una clase crea un *objeto clase*. Los nombres definidos (funciones, variables, etc.) están en el *scope* de esta definición (pertenecen al *namespace* de este objeto clase). El nombre de la clase definida queda en el *namespace* del bloque que contiene la definición.

#### 9.3.2 Class Objects

Para acceder a los miembros de la clase definida, se utiliza la sintaxis ***objeto.atributo***.

```python
class MyClass:
    """A simple example class"""
    i = 12345
    def f(self):
        return 'hello world'
```

En este caso, ***MyClass.f*** es un objeto función que ya puede llamarse; también podemos acceder al atributo ***MyClass.i***, que es un atributo de la clase. En este caso, el atributo ***MyClass.\_\_doc__*** devuelve el *docstring* de la clase.

La instanciación usa notación de función: `x = MyClass()`. Esto crea un *objeto instancia* (de la clase 'MyClass') y lo asocia a la variable 'x'.

Si queremos inicializar con argumentos cuando instanciemos una clase, debemos definir el método (constructor) ***\_\_init__()***, que recogerá esos argumentos.

#### 9.3.3 Instance Objects

Las instancias tienen métodos y/o datos. Si una clase define un *objeto función*, eso es un *objeto método* de cualquier instancia de esa clase. Un objeto instancia puede tener dos tipos de atributos: (1) atributos de datos (*data attributes*) y/o (2) métodos.

#### 9.3.4 Method Objects

Al llamar a un método, se le pasa automáticamente como primer argumento la instancia desde la que se llama al método. Es por ello que todas las funciones definidas en una clase deben incluir ese primer parámetro obligatoriamente.

La diferencia entre un objeto de tipo función y un objeto de tipo método, es que el objeto método está asociado al objeto función definido en la clase, y a la instancia concreta que recibe la llamada.

En definitiva, sin instancia no hay método. Cuando llamamos a un método, internamente se inserta el objeto instancia como primer argumento, y todo ello se pasa al objeto función correspondiente.

Si ***x*** es una instancia de ***MyClass***, que define una función ***foo(self)***:

```python
x.foo()  # llamada a un método
```

equivale a:

```python
MyClass.foo(x)    # llamada a una función
```

#### 9.3.5 Class and Instance Variables

Las variables *definidas* en una clase (o *añadidas posteriormente*) son compartidas por todas las instancias. Las variables *añadidas a la instancia* (en el constructor, en cualquier otra parte del código, incluso fuera de la definición de la clase), son solamente para esa instancia.

### Notas personales

```python
class MiClase:
    peso = 70    # atributo de datos (de la clase)
    def __init__(self):    # constructor
        self.nombre = 'Pepe'  # atrib. de datos (de la instancia)
        peso = 80    # simple variable local de la función
```

Veamos en primer lugar, que para definir atributos de datos desde el código de un método, hay que utilizar el argumento ***self***. De lo contrario sería una simple variable local del método.

Como vemos, en ***MiClase*** se define un atributo de datos ***peso***, que será compartido por todas las instancias. Si ***x*** e ***y*** son instancias de ***MiClase***, podemos acceder a ***x.peso*** y ***y.peso***, además de ***MiClase.peso***. Además, al instanciar ***x*** e ***y***, se ha creado *para cada instancia* el atributo ***nombre***, al que puede accederse con ***x.nombre*** y ***y.nombre***, sin embargo no se trata de un valor compartido: cada instancia almacena su propio valor. Cambiando ***x.nombre*** no estamos cambiando ***y.nombre*** (además, no existe ***MiClase.nombre***).

Por otro lado, si cambiamos ***MiClase.peso***, sí cambiará ***x.peso*** y ***y.peso***. Sin embargo, si hacemos `x.peso=50`, estamos creando un atributo ***peso*** para ***x*** que enmascara (*overrides*) el atributo ***peso*** de ***MiClase***. Cambiándolo, cambiará solo para ***x***. Si ahora cambiamos ***MiClase.peso***, cambiará para ***y*** también, pero no para ***x*** que tiene su propia versión de este atributo. Sin embargo, si ahora hacemos `del x.peso`, el atributo ***peso*** de ***x*** pasará a tener el valor (compartido) que tiene en ***MiClase***.

Si hacemos ahora `del MiClase.peso`, las instancias dejarán de tener atributo ***peso***, a excepción de aquellas que lo hayan definido en sí mismas (como por ejemplo mediante `x.peso=50`).

Se pueden ir creando y borrando atributos de datos en instancias y clases sobre la marcha, en cualquier punto del código, mediante asignaciones (`x.peso=50`, `MiClase.peso=0`, etc.) o borrados mediante `del`. Los nombres añadidos pueden enmascarar o cambiar los de la clase, o ser completamente nuevos (`x.speed = 30`, `MiClase.speed = 0`, etc.).

Se puede también añadir/quitar funciones (métodos) en clases e instancias. Supongamos que ***foo()*** es una función cualquiera, se podrían hacer cosas como `x.fun=foo`, `MiClase.f=foo`, `del MiClase.fun`, etc. Se pueden incluso eliminar de la clase funciones incluidas en la definición de la misma.

*Desde dentro de un método*, se puede acceder a los miembros (variables y métodos) *de la instancia* mediante el primer parámetro, así: ***self.atributo*** (preferentemente le llamaremos ***self***, por convenio, aunque le podríamos dar cualquier nombre). Para acceder a las variables *de la clase*, lo haremos usando el nombre de la clase: ***MiClase.atributo***. Si no queremos escribir explícitamente el nombre de la clase, se podría hacer mediante `type(self).atributo` (también, aunque menos elegante, ***self.\_\_class__.atributo***). Para acceder a variables globales (módulo donde está definido el método), se hace igual que en funciones ordinarias (mediante `global`).

A una función, que es también un objeto, se le pueden añadir atributos. Por ejemplo `foo.n=3` le añade una variable ***n*** a la función ***foo()***. Se comportaría como una variable *static* de una función en *C*. Mantendría su valor entre llamadas, lógicamente. Para acceder a esa variable desde dentro de la función, se haría mediante ***foo.n***, ya que ***n*** a secas sería una simple variable local. También podría accederse a ***foo.n*** desde fuera de la función.

### 9.4 Random Remarks

Si una instancia define una variable con el mismo nombre que una variable de la clase, la de la instancia *overrides* el de la clase.

Las funciones se pueden *definir* fuera de la definición de la clase también:

```python
def f1(self, x, y):
    return min(x, x+y)
class C:
    f = f1
    def g(self):
        return 'hello world'
    h = g
```

En este caso tenemos 3 métodos: ***f***, ***g*** y ***h***.

*Cualquier valor en Python* es un objeto, por lo que tiene una clase (también llamada "tipo"), que está almacenada en el atributo ***objeto.\_\_class__***. Equivale, como hemos visto, a llamar a la función *bult-in* `type(objeto)`.

### 9.5 Inheritance

Se crea una clase derivada así:

```python
class Deriv(Base):
    ...
```

El nombre de la clase base tiene que estar en el *scope* de la definición de la derivada, naturalmente. Si la clase base está, p. e., en otro módulo ***m1***:

```python
class Deriv(m1.Base):
    ...
```

Cuando se referencia un atributo, primero se busca en la clase; si no se encuentra, en la base; y así recursivamente hasta el final de la jerarquía. Así, una derivada *overrides* los atributos (*data attributes* y *methods*) de la base.

Si un método, aunque sea uno definido en la clase base, llama a otro método de la instancia, se empieza a buscar ese método llamado de la forma habitual: empezando por la clase actual y subiendo.

Una forma de llamar a un método de la clase base es hacerlo directamente como función (como haríamos desde fuera):

```python
BaseClassName.methodname(self, arguments)
```

`isinstance(<obj>,<class>)` devuelve verdadero si ***obj*** es una instancia de la clase ***class*** o de una clase derivada de esta.

`issubclass(<class1>,<class2>)` devuelve verdadero si ***class1*** es una clase derivada de ***class2***.

#### 9.5.1 Multiple Inheritance

```python
class Deriv(Base1, Base2, Base3):
    ...

```

Para la resolución de nombres de atributos, es del tipo *depth-first*, *left-to-right*. Así, si un atributo no se encuentra en ***Deriv***, se busca en ***Base1***, y luego recursivamente en las clases base de ***Base1***; si tampoco lo encuentra allí, se busca en ***Base2***, luego en sus bases, etc.

De hecho, el orden de búsqueda (*MRO*) es algo más complejo (*dynamic ordering*), ya que asegura que las estructuras de diamante serán resueltas sin buscar dos veces en la misma clase. Una estructura de diamante se produce cuando hay más de un camino para llegar a una clase, jerarquía arriba; y si partimos de que en *Python* *todas las clases* (y por consiguiente, todos los tipos) son descendientes de la clase ***object***, tenemos como mínimo un diamante asegurado cuando hay una herencia múltiple.

A parte de evitar múltiples búsquedas en la misma clase, el *dynamic ordering* gestiona el problema de múltiples llamadas a `super()` (llamada al constructor de la clase base). Imaginemos que todas las clases realizan esta llamada desde su propio constructor. La clase a la que se puede llegar por más de una vía sería llamada más de una vez. El *dynamic ordering* lo evita.

Sin embargo, este *dynamic ordering* de *Python* **asegura la búsqueda *left-to-right***, con lo que es posible diseñar clases extensibles y confiables con herencia múltiple.

### 9.6 Private Variables

No existen variables de un objeto que sean accesibles únicamente desde los métodos de la instancia. No hay pues, variables privadas. Se suele indicar que un miembro debe ser tratado como privado haciendo que su nombre empiece por ***\_***.

Sin embargo hay un pequeño mecanismo llamado *name mangling* que intenta evitar accidentes: un miembro (*data member or method*) cuyo nombre sea del tipo ***\_\_spam*** (mínimo 2 *underscores* al principio, y máximo 1 al final), durante la ejecución queda reemplazado internamente por ***\_nombreclase\_\_spam*** (***nombreclase*** es el nombre de la clase, sin guiones bajos ***\_*** iniciales, si los tuviera).

Si en una clase base ***B*** definimos el atributo ***\_\_atrib***, y luego creamos una clase derivada ***D***, las referencias que se hagan a ***\_\_atrib*** desde métodos de ***B*** se sustituyen por ***\_B\_\_atrib***, mientras que las que se hagan desde métodos definidos en ***D*** se referirán a ***\_D\_\_atrib***.

Así, por ejemplo, los métodos heredados de ***B*** que hagan referencia a ***\_\_atrib***, serán referencias a ***\_B\_\_atrib***, mientras que desde código definido en ***D*** (incluidos los métodos que *override* los de ***B***) serán referencias a ***\_D\_\_atrib***.

En la práctica, podríamos sustituir todas las ocurrencias de ***\_\_atrib*** por ***\_B\_\_atrib*** dentro del bloque `class` que define ***B***, y por ***\_D\_\_atrib*** dentro del bloque `class` que define ***D***.

Así, cualquier método o atributo de datos con este formato (en este caso ***\_\_atrib***) no se puede *override* en la clase derivada, a no ser que en la derivada definamos explícitamente el atributo ***\_B\_\_atrib***, que sería un error porque al hacerlo ya pierde todo el sentido este mecanismo.

### 9.7 Odds and Ends

Si queremos algo así como un *struct* de *C*, podemos crear una clase vacía, con una simple sentencia `pass` (no nos interesan datos compartidos por las instancias), e ir añadiendo (o borrando) a cada instancia los valores (atributos) que queramos, sobre la marcha.

Supongamos un objeto método ***m***: este tiene un atributo ***m.\_\_self__*** que es el objeto al que pertenece el método, y uno ***m.\_\_func__*** que es la función en sí. De hecho, un método no es más que una función asociada (*bound*) a un objeto instanciado.

### 9.8 Iterators

Los objetos contenedores son *iterables*, es decir, se pueden iterar mediante `for` (listas, tuplas, diccionarios, *strings*, files,...). Lo que hace `for` es llamar a `iter()` sobre el objeto contenedor, lo cual devuelve un iterador: un objeto que tiene el método ***\_\_next__()***, que va devolviendo el siguiente elemento, y por último levanta una excepción ***StopIteration*** para que pare el `for`. Se puede llamar al método ***\_\_next__()*** del iterador usando la función builtin `next()`.

```python
>>> s = 'abc'
>>> it = iter(s)
>>> it
<iterator object at 0x00A1DB50>
>>> next(it)
'a'
>>> next(it)
'b'
>>> next(it)
'c'
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
    next(it)
StopIteration
```

Viendo como funcionan los iteradores, es fácil dotar de funcionalidad de iterador a nuestra clase: solo hay que definir un método ***\_\_iter__()***, que devolverá un objeto que posea un método ***\_\_next__()*** (normalmente ***\_\_iter__()*** devuelve ***self*** y listos); y un método ***\_\_next__()*** que vaya devolviendo elementos secuencialmente al ser llamado, y tras el último levante la excepción ***StopIteration***.

Todos los iteradores son iterables, aunque no todos los iterables son iteradores.

### 9.9 Generators

Los generadores son una herramienta simple y poderosa para *crear iteradores*. Se escriben como una función normal, y usan la sentencia `yield`:

```python
def generador(n1,n2,n3):
    yield n1 * 5
    yield n2 * 9
    yield n3 * 14
for n in generador(2, 2, 2): print(n)
10
18
28
```

***generador*** es un objeto función, y ***generador(2, 2, 2)*** es un objeto generador, que tiene todas las características de un iterador. Un generador crea automáticamente los métodos ***\_\_iter__()*** y ***\_\_next__()***, y levanta la excepción ***StopIteration*** cuando hace falta.

Lo que hace el generador cuando se itera en él es ejecutarse hasta el primer `yield`, devolviendo ese primer valor; al volver (`next()`) para buscar el segundo valor seguirá la ejecución donde la había dejado la primera vez (guarda el estado de ejecución completo), y acabará devolviendo el segundo valor; y así sucesivamente hasta el final. Tras devolver el último `yield`, al volver a llamarse `next()` ejecutará todo el código tras ese último `yield` hasta el final (fin de código o `return`) y levantará ***StopIteration***.

### 9.10 Generator Expressions

Las *generator expressions* son sintácticamente como las *list comprehensions* pero entre paréntesis en lugar de entre corchetes. Crean un iterador. Se suele usar como argumento de una función que recoja un iterable.

```python
(i*i for i in range(10))    # objeto generador
```

## 16. APPENDIX

Los *scripts Python* se pueden hacer directamente ejecutables en entornos *Unix*, añadiendo por ejemplo, como primera línea (línea *shebang*):

```python
#!/usr/bin/python3.9
```

O si no sabemos el *path* del ejecutable:

```python
#!/usr/bin/env python3.9
```

Teniendo en cuenta que el comando de *Python* (en este caso, `python3.9`) debe estar efectivamente incluido en ese *path*.

En todo caso, el archivo *script* debe tener permisos de ejecución.
