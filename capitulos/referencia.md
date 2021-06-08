# Referencia

El presente documento es un resumen de los aspectos más relevantes de la referencia oficial de *Python* 3.9. Los títulos se han mantenido en inglés, que es el idioma del documento que se ha utilizado como base para este resumen.

## 2. LEXICAL ANALYSIS

El archivo de código se pasa a una lista de *tokens*. La conversión la hace el analizador sintáctico, que pasa luego los *tokens* al *parser*. Veamos cómo.

### 2.1 Line structure

Un programa *Python* está formado por **líneas lógicas**.

#### 2.1.1 Logical lines

Una línea lógica termina con el *token NEWLINE*. Corresponde a una o más líneas físicas.

#### 2.1.2 Physical lines

Secuencias de caracteres terminadas con una secuencia de fin de línea (en *Unix LF*, en *Windows CR-LF*, en antiguos *Mac CR*; cualquiera de ellas vale en cualquier plataforma). En los literales *string*, ***\\n*** representa fin de línea.

#### 2.1.3 Comments

Empieza por un ***#*** (que no forme parte de un literal *string*), y termina al final de la línea física. No puede estar en medio de una línea lógica, con lo que su presencia marca el fin de una línea lógica. Los comentarios se ignoran (no generan *tokens*).

#### 2.1.4 Encoding declarations

Codificación del archivo fuente por defecto es *UTF-8*. Si es otra, especificar comentario (***#***) en 1ª o 2ª línea, que contenga coincidencia con la expresión regular:

```
coding[=:]\s*([-\w.]+)
```

Ejemplo, reconocido por *emacs*:

```
# -*- coding: utf-16 -*-
```

Reconocido por *Vim*:

```
# vim:fileencoding=latin-1
```

La codificación concreta indicada tiene que ser reconocida por *Python*.

#### 2.1.5 Explicit line joining

Línea física terminada con *backslash* (***\\***), que no forma parte de un literal *string* o comentario, seguido de *newline*, se junta a la siguiente (desaparece el backslash y el *newline*). No se puede usar para partir *tokens* (excepto literales *string*). No se puede usar en un línea con comentario.

#### 2.1.6 Implicit line joining

Las expresiones entre paréntesis, corchetes o llaves pueden escribirse en varias líneas, siempre que no rompamos *tokens*. Las líneas físicas así partidas sí pueden tener su comentario. Se aceptan líneas en blanco, y la indentación de las líneas (excepto la primera) es irrelevante. Los *triple-quoted strings* también se pueden romper en líneas
implícitamente, pero no pueden llevar comentarios.

#### 2.1.7 Blank lines

Una línea en blanco (espacios, tabs seguidos de *newline*) en el código fuente no generan un nuevo *token NEWLINE*. Se ignora.

#### 2.1.8 Indentation

La indentación (espacios y tabs) marca el nivel de indentación de una **línea lógica**: si hay varias líneas físicas, es la primera de ellas la que marca el nivel de indentación. La indentación no se puede partir en líneas físicas: el nivel lo marca el número de espacios antes del primer carácter no blanco (incluido un posible *backslash* de *explicit line joining*). Se van generando *tokens* *INDENT* y *DEDENT* para marcar los cambios de indentación.

#### 2.1.9 Whitespace between tokens

El espacio blanco (espacios, tabs) no tiene significado en sí, excepto para separar *tokens*; a excepción del espacio de indentación al principio de las líneas lógicas, y dentro de literales *string*.

### 2.2 Other tokens

A parte de *NEWLINE*, *INDENT* y *DEDENT*, existen varias categorías de *tokens*: *identifiers*, *keywords*, *literals*, *operators*, y *delimiters*.

### 2.3 Identifiers and keywords

Los nombres de identificadores dentro de los caracteres *ASCII*, son como en *C* ('\_', 'a'-'z', 'A'-'Z', y, a excepción del primer carácter, '0'-'9'). Si añadimos caracteres fuera del estándar *ASCII*, hay otras reglas: véase *PEP 3131*.

La longitud de los identificadores es ilimitada. *Case sensitive*.

#### 2.3.1 Keywords

`False`, `None`, `True`, `and`, `as`, `assert`, `async`, `await`, `break`, `class`, `continue`, `def`, `del`, `elif`, `else`, `except`, `finally`, `for`, `from`, `global`, `if`, `import`, `in`, `is`, `lambda`, `nonlocal`, `not`, `or`, `pass`, `raise`, `return`, `try`, `while`, `with`, `yield`.

#### 2.3.2 Reserved classes of identifiers

***\_\**** no se importan en `from <modulo> import *`.

***\_\_\*\_\_*** son *system-defined names*; no usar este tipo de nombre.

***\_\_\**** son reemplazados por la forma *mangled* dentro de la definición de una clase.

### 2.4 Literals

#### 2.4.1 String and Bytes literals

La codificación del *source character set* (caracteres del archivo fuente) es el especificado en la codificación del archivo fuente (2.1.4).

Los *strings* y *bytes* puede ir entre comillas dobles o simples. Pero entonces no pueden incluir ese tipo de comilla (si no es *escaped*).

También entre grupos de 3 comillas simples o dobles (se pueden incluir *unescaped newlines* y ambos tipos de comillas).

El *backslash* se usa como carácter escape. Si queremos un *backslash* en sí, hay que incluirlo *escaped* (***\\\\***).

Un literal *bytes* tiene como prefijo ***b*** o ***B***. Solo admite caracteres *ASCII*. Para valores superiores a 127, *escaped*.

Tanto *bytes* como *strings* pueden tener prefijo ***r*** o ***R*** (*raw string*), en cuyo caso el *backslash* se toma literalmente.

El prefijo ***u*** o ***U*** se sigue manteniendo en *strings*, pero es *legacy*, para compatibilidad con *Python* 2 (lo ignoraremos).

El prefijo ***f*** o ***F*** es para *formatted string literals* (lo veremos más adelante). Solo para *strings* (no *bytes*).

El orden (y *case*) de dos prefijos, en caso de coincidir, es indiferente. En el caso de *bytes*, pueden coincidir ***r*** y ***b***. En *strings*, pueden coincidir ***r*** y ***f***.

*Escape sequences*: ***\\newline*** (se ignoran los dos caracteres), ***\\\\***, ***\\'***, ***\\"***, ***\\a*** (*bell*), ***\\b*** (*backspace*), ***\\f*** (*FF*), ***\\n*** (*LF*), ***\\r*** (*CR*), ***\\t*** (*TAB*), ***\\v*** (*TAB* vertical), ***\\⁠ooo*** (hasta 3 caracteres octales), ***\\xhh*** (2 caracteres hexadecimales).

En los literales *string*, las secuencias *escape* octal y hexa denotan un carácter *Unicode*; en un literal *bytes*, el valor del *byte*.

En *strings* (no en *bytes*) se acepta: ***\\N{nombre}*** (carácter con ese nombre en la base de datos *Unicode*), ***\\uhhhh*** (hexa, 16 bits, se pueden poner *surrogate pairs*), ***\\Uhhhhhhhh*** (hexa, 32 bits, cualquier carácter *Unicode* se puede especificar así).

Las *escape sequences* no reconocidas, se dejan tal cual. Un *raw string* no puede terminar en un número impar de backslashes (aunque igualmente no los procesa). Si una línea de un *raw string* termina en ***\\newline***, se mantendrá tal cual en el *string*.

#### 2.4.2 String literal concatenation

Varios literales *string* o *bytes* consecutivos separados únicamente por *white space* (incluyendo comentarios) son concatenados automáticamente, si están en la misma línea lógica (pueden estar en distintas líneas físicas). Esto es así aunque sean de distinto tipo (*raws*, *formatted*, *triple-quoted*, etc.).

Ello se realiza en *compile time*; para concatenar *strings* en *run time* (evaluación de expresiones), utilizar el operador de concatenación '***+***'.

#### 2.4.3 Formatted string literals

Contienen campos (expresiones entre llaves) que se reemplazan en *run time*. Si queremos incluir un signo llave en sí, se debe escribir doblado (***{{*** o ***}}***).

Tras la expresión, dentro de las llaves, se puede añadir un campo de conversión, tras un signo de exclamación (***!***), o un campo de formato, tras dos puntos (***:***). Con ***!s*** el resultado es pasado a `str()`, con ***!r*** a `repr()` y con ***!a*** a `ascii()`. Finalmente el valor obtenido es pasado al método `__format__()` del objeto resultante, junto con el especificador de formato (tras los dos puntos); si no se especifica, se pasa un *string* vacío como especificador de formato.

En cuanto a los valores incluidos en el especificador de formato, pueden ser a su vez campos entre llaves que serán remplazado en *run time*. Estos *nested fields* pueden tener sus propios especificadores de formato, que ya no pueden ser campos, sino valores concretos.

En la biblioteca se hablará del mini-lenguaje de especificadores de formato.

No se pueden incluir expresiones vacías. Una expresión lambda o una expresión asignación (`:=`) se deben encerrar entre paréntesis.

Cada una de las expresiones se evalúa de izquierda a derecha.

Los *f-strings* se pueden concatenar con cualquier tipo de *string*, pero los campos no se pueden partir entre dos literales *string*.

Se debe tener cuidado que las expresiones no contengan posibles comillas que entren en conflicto con las comillas delimitadoras del *string*.

Las expresiones de los campos no pueden contener *backslashes*. Si necesitamos un valor que tenga, por ejemplo, un carácter *escaped*, crear una variable temporal con ese valor y usarla en lugar del literal con el *backslash* directamente.

Los *docstrings* pueden ser de cualquier tipo de *string*, excepto *f-string*.

#### 2.4.4 Numeric literals

Tipos de literal numérico: entero, punto flotante e imaginario.

Los literales numéricos no incluyen signo: ***-5*** es el operador `-` y el literal entero `5`.

#### 2.4.5 Integer literals

No hay límite de tamaño.

- Decimal: secuencia de números de ***0*** a ***9***. No puede empezar por ***0***. El cero puede representarse mediante uno o más ceros.
- Binario: ***0b*** (o ***0B***) seguido de uno o más dígitos binarios (***0*** o ***1***).
- Octal: ***0o*** (o ***0O***) seguido de uno o más dígitos octales (***0*** a ***7***).
- Hexadecimal: ***0x*** (o ***0X***) seguido de uno o más dígitos hexadecimales (***0*** a ***F*** o ***f***).

Para aumentar legibilidad podemos incluir guiones bajos en los literales, entre los dígitos, y/o entre el prefijo de base y el número en sí (***0x\_00ff\_0050***). Para la evaluación del número son ignorados.

#### 2.4.6 Floating point literals

Un número con punto decimal. En este caso, se puede omitir la parte entera o la fraccionaria, pero no ambas. Opcionalmente puede seguir la parte del exponente: ***e*** o ***E***, seguido por signo opcional y los dígitos (sin punto decimal) del mismo.

En el caso de que hayamos incluido la parte del exponente, el número inicial puede ir sin punto decimal.

Tanto el número como el exponente son decimales, y pueden empezar por ***0*** si quieren.

Se pueden incluir guiones bajos entre los números, tanto en la parte entera, como en la fraccionaria, como en el número del exponente.

#### 2.4.7 Imaginary literals

Literal *float*, o solo la parte entera (sin punto), seguido por ***j*** o ***J***. Un número complejo se almacena como un *float* + un *imaginary*: ***5+3j***.

### 2.5 Operators

`+`, `-`, `*`, `**`, `/`, `//`, `%`, `@`, `<<`, `>>`, `&`, `|`, `^`, `~`, `:=`, `<`, `>`, `<=`, `>=`, `==`, `!=`.

### 2.6 Delimiters

Poca utilidad práctica.

## 3. DATA MODEL

### 3.1 Objects, values and types

Los datos de *Python* se almacenan como objetos. Todo objeto tiene una *identidad* única que nunca cambia (`id()`; `is` compara la identidad de dos objetos), un *tipo*, que tampoco cambia (`type()`;
marca qué valores puede tener y qué operaciones pueden hacerse en el objeto; un tipo, es de hecho un objeto también, de tipo ***type***), y un *valor* (que puede cambiar si el objeto es mutable, aunque un contenedor inmutable puede contener objetos mutables). La mutabilidad depende del tipo del objeto. En la implementación *CPython*, `id()` es la dirección de memoria del objeto.

Existe *garbage collection*, de objetos no referenciables. Los recursos "externos" que tiene un objeto son liberados cuando es *collected*, pero como la *garbage collection* no está garantizada, mejor liberar recursos explícitamente (normalmente métodos llamados `close()`). Un buen mecanismo para asegurar que no quedan recursos abiertos es mediante
`try ... finally`, o con `with`.

Cuando hablamos del valor de un contenedor (cualquier objeto que tenga referencias a otros objetos), nos referimos a los valores, no las identidades de sus elementos. Cuando hablamos de la mutabilidad del contenedor, entonces sí nos referimos a las identidades de sus elementos. Si un contenedor inmutable, como una tupla, contiene elementos mutables, al cambiar el valor de alguno de esos elementos, cambia el valor de esa tupla.

Si a dos identificadores les asignamos un valor inmutable, estos pueden tener o no tener el mismo `id()`; pero si lo hacemos con valores mutables, los `id()` serán siempre distintos: si hacemos `a=1` y `b=1`, entonces `a is b` puede ser ***True*** o ***False*** (depende de la implementación), pero si hacemos `a = [1, 2, 3]` y `b = [1, 2, 3]`, entonces `a is b` es siempre ***False*** (sin embargo, si hacemos `a = b = [1, 2, 3]`, tendremos que `a is b` es
***True***).

### 3.2 The standard type hierarchy

Los tipos inmutables pueden ser hashables, los mutables no.

Valores especiales:

- ***None*** - sin valor; p.e., lo retornan las funciones que no retornan nada explícitamente; *truth value is false*.
- ***NotImplemented*** - algunos métodos pueden retornarlo si la operación no está definida para los operandos facilitados; en cuanto a su *truth value*, no se debe evaluar en entornos booleanos.
- ***Ellipsis*** - también se escribe `...`; *truth value is true*.

Cada uno de ellos es de un tipo especial que solo puede tomar ese valor.

**Números**: son inmutables.

- `int` - enteros, positivos y negativos. No tienen límite.
  - `bool` - ***True*** o ***False***. Al convertirse a número, se convierten, respectivamente, a 0 y 1 (o 0.0 y 1.0). A `string`, ***'True'*** y ***'False'***.
- `float` - números de punto flotante de doble precisión.
- `complex` - almacenado como un par de `float`. Atributos `real` e `imag`.

**Secuencias**: conjuntos finitos indexados por enteros positivos y cero (el primero). `len()` devuelve el número de elementos. Selección con `a[i]`. *Slice* `a[i:j]` es una lista del mismo tipo, con los elementos ***i \<= j \< k***. Un *slice* crea una copia del mismo tipo, que a su vez se puede indexar empezando por 0. Algunas secuencias admiten *extended slice*: `a[i:j:k]`, siendo ***k*** el *\"step"*.

- **Secuencias inmutables**: contienen siempre los mismos objetos (aunque sean objetos mutables).
  - ***Strings***: secuencias de caracteres *Unicode* (***U+0000*** a ***U+10FFFF***). No existe el tipo carácter: el *string* no es más que una sucesión de *strings* de longitud 1. `ord()` convierte un elemento de un *string* a entero (índice *Unicode*). `chr()` hace la operación inversa hacia un *string* de longitud 1. `str.encode()` pasa de *string* a *bytes*, y `bytes.decode()` hace la operación inversa.
  - **Tuplas**: *comma-separated* expressions; una sola expresión seguida de coma (*singleton*), o tupla vacía mediante ***()***.
  - ***Bytes***: *array* de *bytes* (8 bits). Se crean con literales *byte*, o con la *built-in function* `bytes()`. Decodificables con método `decode()`.
- **Secuencias mutables**: mediante índice o *slicing* se puede asignar valor a los elementos, o borrarlos (con `del`).
  - **Listas**: *comma-separated expressions* entre corchetes.
  - ***Byte arrays***: creados con el constructor `bytearray()`. Se comportan exactamente igual que un elemento `bytes`, pero es mutable.

***Set types***: conjuntos no ordenados (no indexables) de objetos inmutables y únicos. Se pueden iterar; disponen de `len()`. Aceptan operadores de conjuntos matemáticos.

- ***Sets***: mutables. Constructor `set()`.
- ***Frozen sets***: set inmutable, constructor `frozenset()`.

***Mappings***: conjunto de objetos indexados por un conjunto de índices del tipo ***a[k]***, donde ***k*** es una clave, y se puede usar para acceder o asignar valor a los elementos, o borrarlos (con ***del***). Dispone de `len()`.

- **Diccionarios**: son *mappings* mutables. Las *keys* deben ser inmutables, y no pueden contener elementos mutables (para que los elementos tengan un *hash* constante durante todo su tiempo de vida). Los elementos del diccionario conservan siempre el orden de inserción de los elementos. La notación se realiza mediante llaves (***{}***).

***Callable types***: se puede realizar la llamada a función con ellos.

- ***User-defined functions***: algunos atributos especiales: ***\_\_doc\_\_*** (*docstring*), ***\_\_name\_\_***, ***\_\_module\_\_*** (donde fue definida), ***\_\_defaults\_\_*** (tupla con los valores por defecto de los parámetros), ***\_\_code\_\_*** (objeto código con el cuerpo de la función compilado), ***\_\_globals\_\_*** (diccionario con las variables globales de la función, es decir, los atributos del módulo donde fue definida; *read-only*), ***\_\_dict\_\_*** (diccionario donde se ven los atributos arbitrarios que hemos definido al objeto función), ***\_\_annotations\_\_*** (diccionario con las anotaciones de los parámetros; las claves son los nombres de parámetro, y ***return*** si hay anotación para el valor de retorno), ***\_\_kwdefaults\_\_*** (diccionario con los *default values* de los *keyword arguments*). En el caso de que una variable de estas no tenga elementos, no será un diccionario sino ***None***. Solo se pueden añadir atributos arbitrarios (del tipo `foo.pepe = 5`) a las funciones definidas por el usuario.
- ***Instance methods***: atributos especiales: ***\_\_self\_\_*** (instancia asociada), ***\_\_func\_\_*** (objeto función), ***\_\_doc\_\_*** (*docstring* de la función), ***\_\_name\_\_***, ***\_\_module\_\_***. Los métodos pueden acceder (pero no modificar) a los atributos arbitrarios de la función a la que están vinculados.
- ***Generator functions***
- ***Coroutine functions***
- ***Asynchronous generator functions***
- ***Built-in functions***: tienen ***\_\_doc\_\_***, ***\_\_name\_\_***, ***\_\_self\_\_*** (es ***None***) y ***\_\_module\_\_***.
- ***Built-in methods***: como una *built-in function*, pero además tienen un objeto pasado implícitamente a la función. En este caso, el atributo ***\_\_self\_\_*** (*read-only*) devuelve ese objeto.
- **Clases**: son llamables. Normalmente crean instancias de sí mismas, a no ser que definan el método `__new__()`. Inicialización es típicamente con `__init__()`.
- **Instancias de una clase**: las podemos hacer llamables si definen el método `__call__()`.

**Módulos**: el atributo ***\_\_dict\_\_*** (*read-only*) es la definición de los elementos del *namespace* del módulo, donde están las referencias a sus atributos; las referencias a un atributo de un módulo ***m***, del tipo `m.x` equivalen a `m.__dict__['x']`. El atributo ***\_\_globals\_\_*** de las funciones definidas en el módulo es una referencia a este atributo.

Otros atributos son ***\_\_name\_\_***, ***\_\_doc\_\_*** (*docstring* del módulo) o ***\_\_file\_\_*** (*path* del archivo desde el que se leyó el módulo). ***\_\_annotations\_\_*** es un diccionario con las anotaciones de variables del módulo que se han ido produciendo en la ejecución del mismo.

***Custom classes***: el *namespace* está en ***\_\_dict\_\_*** (atributos de la clase); otros atributos pueden ser ***\_\_name\_\_***, ***\_\_module\_\_***, ***\_\_bases\_\_*** (tupla con las base clases, en orden), ***\_\_doc\_\_*** o ***\_\_annotations\_\_***.

***Class instances***: tienen su propio ***\_\_dict\_\_***. Si un *lookup* no encuentra el nombre aquí, lo busca en el diccionario de la clase; si no, sube por la jerarquía; si aun y así no lo encuentra, y la clase tiene definido el método `__getattr__()`, lo llama. Asignaciones y borrados de atributos modifican el diccionario de la instancia, no de la clase. Si la clase tiene definidos `__setattr__()` o `__delattr__()`, los llama para añadir o borrar atributos respectivamente, sin modificar el diccionario directamente. El atributo
***\_\_class\_\_*** es la clase de la instancia.

***I/O objects*** (*file objects*): representan archivos. Destacar ***sys.stdin***, ***sys.stdout*** y ***sys.stderr*** (input, output y error streams).

**Tipos internos**: utilizados internamente por el intérprete. ***Code objects***, ***frame objects***, ***traceback objects*** y ***slice objects***.

***Slice objects***.

***Static method objects***: constructor `staticmethod()`.

***Class method objects***: constructor `classmethod()`.

### 3.3 Special method names

Métodos con funcionalidades especiales.

#### 3.3.1 Basic customization

Métodos que puede implementar una clase.

`__new__(clase [,args])`

Es un *static method* (aunque en este caso no hace falta declararlo como tal). Se llama a la hora de crear una nueva instancia de ***clase*** si queremos más control sobre esta creación. Los argumentos opcionales son los que pasamos al constructor al llamar a la clase para crear la instancia. Este método devuelve normalmente la nueva instancia (de clase ***clase***).

Típicamente, `__new__()` llamará al `__new__()` de la clase base, por ajemplo así:

```python
super().__new__(clase [,args])
```
o

```python
Base.__new__(clase [,args])
```

Obsérvese que hay que pasarle la clase. Nosotros la recibimos automáticamente, pero al llamar manualmente al de la clase padre, debemos especificar el tipo (clase) de la instancia a crear. Este método de la clase padre retornará el objeto creado, y la clase derivada lo recibe, pudiéndolo modificar antes de retornarlo a su vez.

`__new__()` retorna la instancia creada. Si, efectivamente, retorna un objeto de tipo ***clase*** (el tipo de clase que ha recibido el `__new__()` primero), entonces el método `__init__()` de esa nueva instancia es llamado, pasándole como argumentos los mismos que se
le pasaron a `__new__()`. Si, en cambio, no devuelve una instancia de ***clase***, no se llama a `__init__()`.

Si `__new__()` no está definido, se crea la instancia de forma automática y se llama a `__init__()` (si está definido). Siempre habrá un `__new__()`, aunque sea heredado (como mínimo de ***object***).

`__init__(self [,args])`

Inicializador. Los argumentos son los que pasamos al constructor al llamar a la clase para crear la instancia. Es llamado después de la creación de la instancia (con `__new__()`), y antes de volver de la *constructor expression* del *caller*. Si lo definimos y la clase base también, hay que llamar también al de la clase base (`Base.__init__(self [,args]`), puesto que lo *overrides* (a no ser que no queramos que se ejecute el inicializador de la base). No debe retornar ningún valor (***None***).

`__del__(self)`

Destructor (mejor llamarle finalizador). Si la instancia tiene varias referencias (apuntadores) a ella y hacemos `del x`, se decrementa el contador de referencias; el destructor sólo se ejecuta cuando el contador llega a 0. Si la definimos y la clase base también, deberíamos llamarla para asegurar el borrado de la parte base. Debido a lo precario de las circunstancias en que se llama al destructor, las excepciones que ocurren durante su ejecución son ignoradas; solo se imprime un *warning* en ***sys.stderr***.

`__repr__(self)`

Se usa su valor de retorno para la función `repr()`. Debe ser una representación "oficial" del objeto mediante un *string*. Debería parecer una **expresión válida** que permita recrear el objeto utilizando esa expresión; si no es posible, debería ser una descripción útil (se usa básicamente para *debugging*). Si `__str__()` no está definida y `__repr__()` sí, se usará el valor de esta última como valor de retorno para `str()` también.

`__str__(self)`

Representación "informal" (*nicely printable*) del objeto en un *string*, usada por `format()`, `print()` y `str()`.

`__bytes__(self)`

Representación del objeto en un string de tipo `bytes`. Llamado por `bytes()`.

`__format__(self, format_spec)`

Retorna la representación, en un *string*, del objeto, según el formato dado por ***format_spec***. Es llamado cuando el objeto aparece en la función `format()` o en el método `format()` de un *string*.

`__lt__(self, other)`

Compara el objeto (*self*) con otro objeto (*other*). Cuando hacemos `x < y`, se llamará a `x.__lt__(y)`. En teoría, debería devolver ***True*** si ***self*** es menor que ***other***, y ***False*** en caso contrario, pero de hecho puede devolver cualquier tipo de valor. En caso de que Python necesite un booleano y la función devuelva otro tipo, convertirá ese valor a booleano con `bool()`. Si la operación no está implementada para esos tipos de operando, puede devolver ***NotImplemented***.

`__gt__(self, other)` - idem para `>`.

`__le__(self, other)` - idem para `<=`.

`__ge__(self, other)` - idem para `>=`.

`__eq__(self, other)` - idem para `==`.

`__ne__(self, other)` - idem para `!=`. Por defecto delega en *`__eq__()` e invierte el resultado.

`__hash__(self)`

Retorna el *hash* del objeto; este valor debe ser un entero. El valor se usa para incluir el objeto en *hashed collections* (no ordenadas: diccionarios y *sets*), o como valor de retorno de la función `hash()`. Esta trunca el valor devuelto por `__hash__()` (típicamente a 8 *bytes* en
sistemas de 64 *bits* y 4 *bytes* en sistemas de 32 *bits*).

Dos objetos que se comparan iguales deben tener el mismo *hash*.

Es buena idea coger todos los componentes del objeto que intervengan en las comparaciones de objetos, empaquetarlos en un una tupla y devolver el *hash* de esa tupla como *hash* del objeto:

```python
def __hash__(self):
    return hash((self.name, self.nick, self.color))
```

Si una clase no define `__eq__()`, no debería definir tampoco `__hash__()`.

Si una clase no define `__hash__()`, no es *hashable*, con lo que sus instancias no se pueden usar como elementos de colecciones *hashables*.

Si una clase define elementos mutables, no debe definir `__hash__()` (los elementos *hashables* deben ser inmutables para que no varíe nunca su *hash*).

Las clases definidas por el usuario tienen métodos `__hash__()` y `__eq__()` por defecto: todos los objetos se comparan distintos excepto con ellos mismos, es decir, `x == y` es verdadero si y solo si `x is y` lo es, y `hash(x) == hash(y)`.

Una clase que *overrides* `__eq__()` y no define `__hash__()`, tendrá su `__hash__()` ímplicitamente a ***None*** y levantará un ***TypeError*** si se pasa una instancia a la función `hash()`; además será identificada como no *hashable*.

Una clase cuyo `__hash__()` no sea igual a ***None*** es identificada como *hashable*, aunque levante explícitamente un ***TypeError***. Si se quiere que una clase sea no *hashable*, se debe definir en ella `__hash__ = None`.

Si una clase quiere retener la funcionalidad *hash* de una clase base, debe indicarlo explícitamente mediante `__hash__ = BaseClass.__hash__`

Los objetos *string* y *bytes* tienen valores de *hash* aleatorios.

`__bool__(self)`

Retorna lo que devuelve la builtin `bool()` sobre el objeto. Debe retornar ***False*** o ***True***. Si no está definido, se llama a `__len__()` que devolverá ***True*** si contiene más de 0 elementos. Si tampoco está definida, será siempre ***True***.

#### 3.3.2 Customizing attribute access

`__getattr__(self,name)`

Se llama cuando no se ha encontrado el atributo ***name*** por vías normales (ni en la instancia ni en la jerarquía de clases). Debe devolver el valor del atributo especificado en el *string* ***name***, o indicar que tampoco se ha encontrado ese atributo, levantando
***AttributeError***.

`__getattribute__(self,name)`

Todos los accesos a los atributos (métodos y datos) se hacen llamando a este método, obligatoriamente (toda clase lo tiene, porque lo hereda de ***object***, como mínimo). Si `__getattr__()` también está definido (puede no estarlo, porque ***object*** no lo tiene), no será llamado a no ser que `__getattribute__()` lo llame explícitamente o genere una excepción ***AttributeError***.

Si necesitamos hacer referencia a un atributo desde esta función, normalmente será a un atributo existente de la clase o instancia; si lo hacemos directamente (***self.x***) se genera un bucle infinito (el acceso volverá a llamar a `__getattribute__()`, y así recursivamente). Para evitarlo, se debe obtener el valor del atributo accediendo a él a través del `__gettatribute__()` de una clase base (o desde cualquier otra clase u otro lado), pasándole la instancia actual como parámetro, que lo único que hará será devolver el valor de ese atributo existente:

```python
Base.__getattribute__(self, nombre)
```

Si no tiene clases base, se puede hacer desde la clase ***object***:

```python
object.__getattribute__(self, nombre)
```

Recordemos que aunque el atributo no esté definido en la clase base, el valor será bien devuelto, porque lo extrae de ***self***, que se le pasa también por parámetro: estamos llamando a través de la clase, es decir, directamente a la función (*unbound*), no como método.

Importante: `__getattr__()` sufre el mismo problema cuando intentamos acceder a un atributo inexistente, con la diferencia de que al no disponer ***object*** de este método, deberemos definir esa función (pasándole una referencia a la instancia y el nombre del atributo
deseado) en algún lado (en la clase base de nuestra clase, por ejemplo).

`__setattr__(self,name,valor)`

Para establecer o cambiar valores. El comportamiento por defecto es cambiar el diccionario de la instancia. En el caso de que quiera asignar un valor a un atributo, para evitar recursión infinita debe hacerlo a través del `__setattr__()` de la clase base (u ***object***, o cualquier otro lado).

`__delattr__(self,name)`

Como `__setattr__()` pero para eliminación.

`__dir__(self)`

Retorna una secuencia, normalmente con el diccionario de nombres de la instancia. Es lo que retorna `dir()`. Esta función convierte la secuencia en lista y la ordena.

#### Customizing module attribute access

Se puede definir la función `__getattr__()` dentro de un módulo, que se ejecuta cuando no se encuentra un atributo del módulo de la forma habitual. La función acepta un *string* con el nombre del atributo, y debe devolver su valor, o levantar ***AttributeError***.

También se puede definir `__dir__()` de un módulo, que *overrides* completamente el comportamiento de `dir()` sobre el módulo. Si implementamos la función, no acepta argumentos, y debe devolver una lista de *strings* con los nombres de la tabla de nombres del módulo.

#### Implementing Descriptors

Un descriptor es una instancia de una clase que define por lo menos uno de los métodos siguientes: `__get__()`, `__set__()` o `__delete__()`. Un descriptor se usa dentro de otra clase (*owner*) como atributo. Para que una clase *owner* tenga un descriptor, debe definir un atributo de tipo descriptor o debe definirlo una de sus clases base. En resumen, la clase propietaria debe tener una instancia del descriptor.

`__get__(self, instance, owner)`

***self*** es el objeto (instancia) descriptor, ***instance*** es la instancia de la clase *owner*, y ***owner*** es la clase *owner*. Si ***instance*** es ***None***, es que estamos accediendo al atributo a través de la clase (***owner***) y no a través de una instancia de la misma. El método devuelve el valor del atributo solicitado, es decir, el valor de la instancia del descriptor.

Si p.e. ***Descrip*** es una clase que implementa `__get__()`, y diseñamos otra clase ***Own*** que tiene un data attribute ***a*** de tipo ***Descrip***, entonces ***Own.a*** invocará a dicha función.

`__set__(self, instance, valor)`

Cambia el valor del atributo (la instancia del descriptor) al que se refiere el descriptor.

`__delete__(self, instance)`

Se invoca cuando eliminamos el atributo (instancia del descriptor) de la instancia de la clase propietaria.

#### Invoking Descriptors

Normalmente, el acceso a un atributo de una instancia se hace con un *lookup* al diccionario de la instancia. Si ***a*** es una instancia de la clase ***A***, con un atributo ***x***, entonces `a.x` empezará buscando en `a.__dict__['x']`; si no lo encuentra, seguirá por `type(a).__dict__['x']` (la clase), y seguirá por las clases base. Pero si el atributo es un descriptor, entonces se accede directamente al objeto descriptor:

- `x.__get__(a)` - invocación directa.
-  `a.x` - *instance binding*; se transforma automáticamente en `type(a).__dict__['x'].__get__(a, type(a))`; se hace desde `type(a)` porque este atributo está en la tabla de nombres de la clase, no de la instancia.
- `A.x` - *class binding*; pasa a `A.__dict__['x'].__get__(None, A)`.

Si se accede al atributo `a.x` y el descriptor no define `__get__()`, lo que devolverá es la instancia del objeto descriptor, a no ser que exista un atributo con ese nombre en el diccionario de la instancia.

Del mismo modo, si el atributo descriptor está definido en la instancia y no en la clase, no funciona: al hacer `a.x` devuelve el objeto descriptor tal cual.

Si un descriptor define `__set__()` y/o `__delete__()`, es un *data descriptor*, mientras que si no, es un *non-data descriptor*.

Los *data descriptors* suelen definir `__get__()` y `__set__()`, mientras que los *non-data descriptors* solo suelen definir `__get__()`. Los *non-data descriptors* pueden ser *overriden* por la instancia, mientras que los *data descriptors* no.

#### \_\_slots\_\_

Antes, una pequeña explicación: los lenguajes con *garbage collection* suelen implementar el concepto de *weak references*. Una variable que referencie a un objeto es una *strong reference*. Mientras exista por lo menos una *strong reference*, el objeto sigue vivo. Cuando no quedan *strong references*, el objeto puede ser *garbage collected*, pero mientras tanto sigue accesible (vivo) a través de posibles *weak references* (si las hemos creado). Se pueden crear y gestionar *weak references* a objetos mediante el módulo ***weakref***. Cuando ha sido *collected*, las *weak references* ya están *cleared*. Las *strong references* de un objeto se guardan en el diccionario ***\_\_dict\_\_***, y las *weak references* en otro llamado ***\_\_weakref\_\_***. Son diccionarios que enlazan cada nombre al objeto que referencian.

Así, las instancias tienen su diccionario donde se guardan sus atributos (***\_\_dict\_\_***). Si tenemos *muchas* instancias con pocos atributos, esto consume muchísima memoria de forma ineficiente, con tanto diccionario. En cambio si la clase define la variable (atributo) ***\_\_slots\_\_***, este puede contener una secuencia (cualquier iterable) de *strings* con los nombres de las variables (atributos) que podrán tener las instancias de esa clase, y en esas instancias se reservará internamente el mínimo espacio necesario para almacenar sus valores de forma extremadamente eficiente, en lugar de utilizar los diccionarios ***\_\_dict\_\_*** o ***\_\_weakref\_\_***, que simplemente no serán creados.

***\_\_slots\_\_*** nos dice qué atributos se pueden añadir a la instancia. Solo podremos añadirle esos atributos, pero no es obligatorio definirlos todos. Sin embargo, a la clase en sí se le pueden añadir todos los atributos que queramos, aunque la clase tampoco tiene diccionarios ***\_\_dict\_\_*** ni ***\_\_weakref\_\_***.

No se pueden añadir, pues, atributos no listados en *slots* a una instancia; si necesitamos hacerlo, deberemos añadir ***\_\_dict\_\_*** a la lista de *slots*.

No se pueden crear *weak references* a un objeto con slots. Si se necesita crearlas, hay que añadir manualmente ***\_\_weakref\_\_*** a la lista de *slots*.

Si una subclase hereda de una clase sin *slots*, sí tendrá diccionarios ***\_\_dict\_\_*** y ***\_\_weakref\_\_***, aunque en estos diccionarios no se incluirán los atributos definidos en ***\_\_slots\_\_***.

En la definición de *slots*, los nombres definidos allí crean descriptores, con lo que la clase no puede definir a su vez atributos con esos nombres, puesto que esas definiciones sobrescribirían los descriptores creados por el efecto de ***\_\_slots\_\_***.

Si una subclase hereda de una clase con *slots*, tendrá diccionario igualmente, a no ser que también defina ***\_\_slots\_\_***; en ese caso, esa nueva *slots* deberá definir solo los elementos *adicionales*, porque ya hereda los de la clase base (si repite nombres, los de la base son inalcanzables).

En herencia múltiple, como mucho una de las clases base puede definir *slots*.

Si se utiliza un iterador para definir *slots*, se crearán los descriptores correspondientes, pero la variable ***\_\_slots\_\_*** será un iterador vacío.

#### 3.3.3 Customizing class creation

Es una técnica bastante avanzada, y no se aplica a la mayoría de casos de uso. Solo una pequeña pincelada:

Si un objeto instancia es creado según se especifica en el objeto clase, ¿en qué se basa para crear un objeto clase? Pues a su vez se especifica mediante un objeto metaclase. Por defecto, la metaclase de las clases definidas por el usuario es ***type***. Es posible crear nuestras propias metaclases.

#### 3.3.4 Customizing instance and subclass checks

Métodos usados para override el comportamiento de `isinstance()` y `issubclass()`:

`__instancecheck__(self, object)` debe retornar ***True*** si ***object*** debe ser considerado instancia de la clase que define el método. ***False*** si no.

`__subclasscheck__(self, class)` debe retornar ***True*** si ***class*** debe ser considerada subclase de la clase que define el método. ***False*** si no.

Hay que decir que estos métodos no se definen en la clase en sí, sino en la metaclase de la clase que deseamos chequear.

#### 3.3.5 Emulating generic types

Innecesario también en la mayoría de casos de uso.

#### 3.3.6 Emulating callable objects

Para que se pueda llamar a la instancia, como si fuera una función:

`__call__(self [,argumentos])` - si llamamos una instancia ***x*** así:

```python
x(a1,a2,...)
```
Será equivalente a:

```python
x.__call__(a1,a2,...)
```

#### 3.3.7 Emulating container types

Los contenedores pueden ser secuencias (como listas o tuplas) o *mappings* (como los diccionarios), aunque puede haber otros tipos. La diferencia entre secuencias y diccionarios es que en las primeras, las claves (*keys*) son índices enteros de 0 a N-1 (N elementos) u objetos *slice*.

Los *mappings* deberían definir los métodos `keys()`, `values()`, `items()`, `get()`, `clear()`, `setdefault()`, `pop()`, `popitem()`, `copy()` y `update()`, y comportarse de forma análoga a como lo hacen en los diccionarios de *Python*.

Las **secuencias mutables** deberían definir los métodos `append()`, `count()`, `index()`, `extend()`, `insert()`, `pop()`, `remove()`, `reverse()` y `sort()`, y que se comporten de forma análoga a como lo hacen en las listas de *Python*.

**Todas las secuencias** deberían definir la adición (concatenación) y multiplicación (repetición) mediante los métodos `__add__()`, `__radd__()`, `__iadd__()`, `__mul__()`, `__rmul__()` y `__imul__()`, y no definir otros operadores aritméticos.

Todos los contenedores, en general, deberían implementar `__contains__()` para *membership tests* (`in`, que en el caso de diccionarios, debería buscar en las claves), e `__iter__()` para iteración de sus elementos (en *mappings* debería iterar por todas las claves).

`__len__(self)`

Para la función *builtin* `len()`. Devuelve un entero no negativo; normalmente, el número de elementos del contenedor. Un objeto cuyo `len()` es 0 y no define `__bool__()`, se considera ***False***.

`__length_hint__(self)`

Estimación del *expected size*; se usa para *presize* *containers*, para optimizar el funcionamiento interno, y no es obligatorio. Puede devolver ***NotImplemented***, que es como si no estuviese definido.

Nota: el *slicing* utiliza únicamente los siguientes 3 métodos. `a[1:2] = b` se transforma automáticamente en `a[slice(1, 2, None)] = b`. Los argumentos que faltan en la llamada se rellenan siempre con ***None***.

`__getitem__(self, key)`

Para evaluar `self[key]`. Para secuencias, las claves son enteros o *slices*. La interpretación de los índices negativos depende totalmente de esta función. Si ***key*** es de un tipo inapropiado, se puede levantar ***TypeError***. En el caso de *mappings*, si ***key*** no existe, levantar ***KeyError***. Para índices fuera de rango, se levanta ***IndexError*** (los bucles `for` lo usan para detectar el final).

`__missing__(self,key)`

Es llamado por `__getitem__()` para implementar `self[key]` cuando la clave no está en el diccionario.

`__setitem__(self,key,value)`

Todo lo dicho para índices en `__getitem__()` se aplica aquí, pero para escribir en lugar de leer. Debe ser posible ese cambio de valores, así como añadir valores nuevos. Mismas excepciones que `__getitem__()`.

`__delitem__(self, key)`

Para borrar `self[key]`. Se aplica lo dicho en `__getitem__()`. Debe ser posible la eliminación. Mismas excepciones que `__getitem__()`.

`__missing__(self,key)`

Para tratar claves inexistentes en el contenedor.

`__iter__(self)`

Debe devolver un iterador que itere sobre los elementos del contenedor. En *mappings*, debe iterar por las claves. Los iteradores también implementan este método para retornarse a sí mismos.

`__reversed__(self)`

Devuelve también un iterador, pero que itera en el orden inverso. Es lo que devuelve la *built-in function* `reversed()` sobre el objeto. Si `__reversed__()` no está definido, `reversed()` lo implementa usando `__len__()` y `__getitem__()`, por lo que sólo deberíamos definir este método si va a ser más eficiente que esta implementación por defecto.

`__contains__(self,item)`

Para membership tests (`in`, `not in`). Devuelve ***True*** o ***False***. En *mappings* debería centrarse en las *keys*. Si no está definido, se hace automáticamente, iterando por los elementos, por lo que sólo deberíamos definir este método si va a ser más eficiente.

#### 3.3.8 Emulating numeric types

Habría que definir solo las operaciones que pueden realizarse sobre el objeto.

Métodos:

`__add__(self, otro)`, `__sub__(self, otro)`, `__mul__(self, otro)`, `__matmul__(self, otro)`, `__truediv__(self, otro)`, `__floordiv__(self, otro)`, `__mod__(self, otro)`, `__divmod__(self, otro)`, `__pow__(self, otro [,modulo])`, `__lshift__(self, otro)`, `__rshift__(self, otro)`, `__and__(self, otro)`, `__xor__(self, otro)` y `__or__(self, otro)`.

Corresponden respectivamente a los operadores:

`+`, `-`, `\*`, `@`, `/`, `//`, `%`, `divmod()`, `pow()` (y `**`), `<<`, `>>`, `&`, `^` y `|`.

`A ** B` equivale a `pow(A, B)`. Y `pow(A, B, C)` debería equivaler a `A ** B % C`. Aunque nosotros podemos definirlo como queramos para nuestras clases.

Se debería definir `pow()` para 3 argumentos. `divmod(A, B)` debería devolver una tupla (`A // B`, `A % B`). `@` es multiplicación de matrices.

Si la función no implementa la operación con el argumento que se le ha pasado, debería devolver ***NotImplemented*** (o simplemente no definirla).

Si ***x*** es una instancia de una clase que tiene `__add__()` definido, entonces `x + y` llamaría a `x.__add__(y)`.

Cuando el operando de la izquierda no soporta la operación, es decir, cuando ese método no está definido o retorna ***NotImplemented***, hay dos opciones. Si el operando de la derecha es del mismo tipo, hemos terminado: la operación no está definida. Pero si es de un tipo distinto, podemos mirar si esa clase define la operación en esos casos, es decir, si define lo que se llama la operación reflejada, con los operandos intercambiados. Estos métodos especiales del operando de la derecha son:

`__radd__(self, otro)`,`__rsub__(self, otro)`, `__rmul__(self, otro)`, `__rmatmul__(self, otro)`, `__rtruediv__(self, otro)`, `__rfloordiv__(self, otro)`, `__rmod__(self, otro)`, `__rdivmod__(self, otro)`, `__rpow__(self, otro)`, `__rlshift__(self, otro)`, `__rrshift__(self, otro)`, `__rand__(self, otro)`, `__rxor__(self, otro)` y `__ror__(self, otro)`.

Estos métodos se corresponden a los anteriores, solo en caso de operaciones reflejadas.

En este caso concreto, `pow()` con 3 argumentos no intentará llamar a `__rpow__()` (muy complejo implementar la operación reflejada).

En el caso específico de que el segundo operando sea de una subclase del tipo del primer operando, la versión reflejada del segundo se llamará prioritariamente sobre la versión normal del primero. Ello se hace para que las subclases puedan *override* las operaciones de sus bases.

Si los operandos son de la misma clase y la operación normal no está definida, no se llamará a la reflejada, en caso de existir, ya que se asume que la operación no está definida.

*Augmented assignments*:

`__iadd__(self, otro)`, `__isub__(self, otro)`, `__imul__(self, otro)`, `__imatmul__(self, otro)`, `__itruediv__(self, otro)`, `__ifloordiv__(self, otro)`, `__imod__(self, otro)`, `__ipow__(self, otro, [modulo])`, `__ilshift__(self, otro)`, `__irshift__(self, otro)`, `__iand__(self, otro)`, `__ixor__(self, otro)` y `__ior__(self, otro)`.

Estos métodos corresponden respectivamente a `+=`, `-=`, `*=`, `@=`, `/=`, `//=`, `%=`, `**=`, `<<=`, `>>=`, `&=`, `^=` y `|=`. Deberían modificar ***self*** directamente y luego devolver el resultado (que por lo general será ***self***, aunque no tiene por qué). Al encontrar uno de estos operadores, si no está definido el método correspondiente, *Python* lo intentará con la forma normal. Si tampoco, intentará con el reflejado.

`__neg__(self)`, `__pos__(self)`, `__abs__(self)` y `__invert__(self)` responden respectivamente a los operadores unarios `-`, `+`, `abs()` y `~`.

`__complex__(self)`, `__int__(self)`, `__float__(self)` y `__round__(self [,n])` responden respectivamente a las *built-in functions* `complex()`, `int()`, `float()` y `round()`. Deben retornar un valor del tipo adecuado.

`__round__(self [,digits])`, `__trunc__(self)`, `__floor__(self)` y `__ceil__(self)` responden a la *bult-in function* `round()`, y a las funciones del módulo ***math*** `trunc()`, `floor()` y `ceil()`. Todas ellas deberían retornar un número entero, a excepción de `round()` en caso de especificar un número de dígitos decimales superior a cero. Si `__int__()` no está definida, `int()` *falls back to* `__trunc__()`.

Truncar es eliminar la parte decimal, con lo que equivale a *floor* en positivos, y a *ceil* en negativos.

#### 3.3.9 With Statement Context Managers

Usados con la sentencia `with`, algunos objetos realizan algunas acciones al entrar en esa sentencia, y otras al salir. Normalmente se usa para inicializar recursos y luego liberarlos, guardar y restaurar un estado global, etc. Son dos métodos que se invocan al ejecutarse la sentencia `with`, pero también pueden ser llamados directamente. Por ejemplo, para files:

```python
with open('/tmp/workfile', 'r') as f:
    # hacer cosas con el objeto f
```

Cuando termina la sentencia `with`, cierra el archivo automáticamente, con lo que no hay que hacer `f.close()`.

`__enter__(self)`

Lo que ejecutará la sentencia `with`, si la usamos con nuestra clase. Lo que retorne este método lo asignará `with` a la variable después de `as`.

`__exit__(self, exc_type, exc_val, traceback)`

Lo que se ejecutará al terminar el código del `with`. Los parámetros extra reciben la causa de salida de `with`. Si no se produjo excepción, `__exit__()` será llamada con tres ***None***. Si se produjo excepción durante la ejecución del bloque `with`, se llamará con tres valores correspondientes a la excepción, su valor y el objeto de tipo *traceback* (que tiene la información de dónde se produjo el error). Si deseamos que no se propague la excepción después de la cláusula `with`, `__exit__()` debería devolver un valor verdadero (p.e. ***True***), de lo contrario la excepción se recogerá tras `__exit__()`.

#### 3.3.10 Special method lookup

Los métodos especiales que hemos estado describiendo deben definirse en el diccionario de la clase, no de la instancia, ya que de lo contrario no se encontrarán al hacer el *lookup*.

### 3.4 Coroutines

Para tareas asíncronas. Muy relacionado con el módulo ***asyncio***. No es *threading* ni *multiprocessing*: es *multitasking* en un mismo hilo, aprovechando las esperas de las tareas durante, sobre todo, operaciones *I/O*.

**No vamos a cubrir lo relacionado con tareas asíncronas en este resumen.**

## 4. EXECUTION MODEL

### 4.2 Naming and binding

#### 4.2.1 Binding of names

Los nombres están vinculados (*bound*) a objetos. Si un nombre se vincula dentro de un bloque de código (módulo, función, clase, etc.), es una variable local de ese bloque, a no ser que se declare como `nonlocal` o `global`. Si se vincula a nivel de módulo, es una variable global.

#### 4.2.2 Resolution of names

El ámbito (*scope*) define la visibilidad de un objeto dentro de un bloque. Para resolver nombres se usa el *nearest enclosing scope*.

Si un nombre referenciado no se encuentra, se levanta ***NameError***. Si está en un *scope* local y no se encuentra pero está definido más adelante en el *scope*, se levanta ***UnboundLocalError*** (subclase de ***NameError***).

## 6. EXPRESSIONS

### 6.1 Arithmetic conversions

El comportamiento normal de los operadores aritméticos con los tipos *built-in* es convertir a un tipo común los operandos cuando estos son de tipo distinto: si uno de los dos es `complex`, el otro es convertido a `complex`. Si no, si uno es de punto flotante, el otro se convierte a punto flotante. Si no, es que son los dos enteros.

Puede haber reglas adicionales para algunos operadores.

### 6.2 Atoms

Los elementos más básicos de una expresión. Identificadores y literales.

#### 6.2.3 Parenthesized forms

Para agrupar expresiones. Retorna lo que retorna la expresión que contiene. Si contiene comas, retorna una tupla; si no, el valor de la expresión que engloba. Así, los paréntesis no definen una tupla, sino las comas, a excepción de un par de paréntesis vacíos, que retornan una tupla vacía.

#### 6.2.4 Displays for lists, sets and dictionaries

En las *comprehensions*, la expresión inicial es evaluada en el *enclosing scope* (bloque de código que define la *comprehension*), y es pasada como argumento al *scope* anidado que forma la *comprehension*. El resto de expresiones (en los `if` y los `for`) pertenecen a ese
*scope* anidado.

#### 6.2.5 List displays

`[lista de elementos]` o `[comprehension]`.

#### 6.2.6 Set displays

`{lista de elementos}` o `{comprehension}`.

#### 6.2.7 Dictionary displays

`{lista de elementos key:value}` o `{dict comprehension}`.

Si uno de los elementos es del tipo ***\*\*m*** (*dictionary unpacking*), y ***m*** es un *mapping*, entonces se insertan también los elementos de ***m*** en el diccionario .

En el caso de una *comprehension*, la expresión inicial debe estar formada por un par de expresiones separadas por dos puntos (***:***).

Al añadir elementos a un diccionario, la clave se evalúa antes que el valor.

Las claves deben ser de un tipo *hashable*.

#### 6.2.8 Generator expressions

`(comprehension)`

No está permitido usar *yield* en el *nested scope*.

#### 6.2.9 Yield expressions

Un generador devuelve valores mediante `yield`, o `yield from <expr>`, donde ***expr*** actúa como un subiterador del que va tomando valores para realizar los `yield`.

El método `__next__()` de los iteradores y generadores es llamado implícitamente por una cláusula `for`, o directamente con la *built-in function* `next()`.

### 6.10 Comparisons

En *Python*, a parte de asignaciones múltiples (`a = b = 3`), se pueden hacer comparaciones encadenadas (`a < b < c == d`). Se encadenan con `and`, y no se evalúan las expresiones dos veces, a parte de que también cortocircuitan.

Los operadores de comparación comparan valores, no tipos ni identidades, por lo que se pueden aplicar entre operandos de tipos distintos.

Sin embargo, los operadores `==` y `!=` se basan, por defecto, en la identidad (`id()`).

Los tipos numéricos pueden compararse entre sí. Aunque los complejos no tienen comparación de orden (`>`, `<`, `<=`, `>=`).

El valor ***NaN*** es diferente a todo, incluso a sí mismo. Los *singletons* ***None*** y ***NotImplemented*** deberían ser comparados solo mediante `is` e `is not`.

Las secuencias *bytes* (`bytes`, `bytearray`) y *string* (`str`) se pueden comparar con secuencias de su tipo, usándose el valor numérico de sus elementos (*bytes* o *Unicode code points*). No puede compararse una secuencia de uno de estos tipos con otra del otro tipo.

Las *secuencias* (`tuple`, `list`, `range`) pueden compararse con objetos de su mismo tipo, aunque los *ranges* no tienen comparación de orden. Las secuencias se comparan comparando elemento por elemento. Si dos secuencias son iguales hasta el final de la más corta, la más corta es la menor.

Los *mappings* (`dict`) solo se comparan iguales si tienen los mismos pares ***clave:valor***. No pueden compararse por orden.

Los *sets* (`set`, `frozenset`) se pueden comparar con su mismo tipo. Las comparaciones de orden se interpretan mediante la relación subconjunto-superconjunto.

#### 6.10.2 Membership test operations

Para comprobaciones del tipo `<obj> in <seq>`. Los *strings*, *bytes*, secuencias, *mappings* y *sets built-in* implementan esta comprobación (en diccionarios se comprueba si existe tal clave).

#### 6.10.3 Identity comparisons

`x is y` es verdadero solo si ***x*** e ***y*** son el mismo objeto; `x is not y` es verdadero en caso contrario.

### 6.13 Conditional expressions

También llamado *ternary operator*. Es del tipo:

```python
<expr1> if <cond> else <expr2>
```

Retorna ***expr1*** si ***cond*** es verdadero, o ***expr2*** en caso contrario.

### 6.16 Evaluation order

*Python* evalúa las expresiones de izquierda a derecha. En el ejemplo, el número indica el orden:

```python
expr1, expr2, expr3, expr4
(expr1, expr2, expr3, expr4)
{expr1: expr2, expr3: expr4}
expr1 + expr2 * (expr3 - expr4)
expr1(expr2, expr3, *expr4, **expr5)
expr3, expr4 = expr1, expr2
```

### 6.17 Operator precedence

Prioridad de los operadores en *Python*, de menor a mayor:

+----------------------------------+----------------------------------+
| `:=`                             | Assignment expression            |
+----------------------------------+----------------------------------+
| `lambda`                         | Lambda expression                |
+----------------------------------+----------------------------------+
| `if` - `else`                    | Conditional expression           |
+----------------------------------+----------------------------------+
| `or`                             | Boolean OR                       |
+----------------------------------+----------------------------------+
| `and`                            | Boolean AND                      |
+----------------------------------+----------------------------------+
| `not`                            | Boolean NOT                      |
+----------------------------------+----------------------------------+
| `in`, `not in`, `is`, `is not`,  | Comparisons, including           |
| `<`, `<=`, `>`, `>=`, `!=`, `==` | membership tests and identity    |
|                                  | tests                            |
+----------------------------------+----------------------------------+
| `|`                              | Bitwise OR                       |
+----------------------------------+----------------------------------+
| `^`                              | Bitwise XOR                      |
+----------------------------------+----------------------------------+
| `&`                              | Bitwise AND                      |
+----------------------------------+----------------------------------+
| `<<`, `>>`                       | Shifts                           |
+----------------------------------+----------------------------------+
| `+`, `-`                         | Addition and subtraction         |
+----------------------------------+----------------------------------+
| `*`, `@`, `/`, `//`, `%`         | Multiplication, matrix           |
|                                  | multiplication,                  |
|                                  | division, floor division,        |
|                                  | remainder                        |
+----------------------------------+----------------------------------+
| `+x`, `-x`, `~x`                 | Positive, negative, bitwise NOT  |
+----------------------------------+----------------------------------+
| `**`                             | Exponentiation                   |
+----------------------------------+----------------------------------+
| `await x`                        | Await expression                 |
+----------------------------------+----------------------------------+
| `x[index]`, `x[index:index]`,    | Subscription, slicing, call,     |
| `x(arguments...)`, `x.attribute` | attribute reference              |
+----------------------------------+----------------------------------+
| `(expressions...)`,              | Binding or parenthesized         |
| `[expressions...]`,              | expression, list display,        |
| `{key: value\...}`,              | dictionary display, set display  |
| `{expressions...}`               |                                  |
+----------------------------------+----------------------------------+

En cuanto al operador de exponente, su prioridad es distinta en operadores unarios de uno y otro lado:

```python
>>> n = -1 ** 2
>>> n
-1
>>> n = 2 ** -1
>>> n
0.5
```

## 7. SIMPLE STATEMENTS

### 7.3 The assert statement

La sentencia:

```python
assert <expr>
```

equivale a:

```python
if __debug__:
    if not <expr>: raise AssertionError
```

En cuanto a la versión extendida:

```python
assert <expr1>, <expr2>
```

equivale a:

```python
if __debug__:
    if not <expr1>: raise AssertionError(<expr2>)
```

***\_\_debug\_\_*** es ***True*** en circunstancias normales. Será ***False*** cuando indiquemos que la compilación se haga con optimización (*command line option* ***-O***).

### 7.4 The pass statement

Operación nula.

### 7.5 The del statement

```python
del <lista de objetos>
```

Elimina el nombre (o nombres) especificados en la lista, de la tabla de nombres local o global, dependiendo de si están declarados como `global`, `nonlocal` o nada. Si el nombre está *unbound*, se levanta ***NameError***. Los elimina recursivamente, de izquierda a derecha.

### 7.6 The return statement

```python
return <lista de expresiones>
```

Retorna una expresión o lista de expresiones, o ***None*** si no se especifica. Si una función no termina en `return` también retorna ***None***.

Si `return` abandona una cláusula `try` que contiene `finally`, antes de retornar se ejecuta el código de `finally`.

En un iterador, indica que este ha terminado las iteraciones y levanta ***StopIteration***. Si devuelve valor, este quedará en ***StopIteration.value***.

### 7.7 The yield statement

En un generador:

```python
yield <expr>
```
o:

```python
yield from <expr>
```

donde la expresión es un sub-iterador, de donde el generador irá extrayendo los sucesivos valores para irlos *yielding*.

### 7.8 The raise statement

```python
raise
```

*re-raises* la última excepción que estaba activa en el *scope* actual. Si no había ninguna, se levanta ***RuntimeError***.

```python
raise <excep>
```

levanta la excepción ***excep***, que debe ser una subclase o una instancia de ***BaseException***. Si es una clase, se construirá internamente, cuando sea necesario, la instancia a partir de esa clase, sin argumentos.

Al levantarse una excepción se genera un objeto *traceback* (sucesión de llamadas hasta el punto del código donde se produce la excepción), que se añade a la instancia de la excepción levantada, en su atributo ***\_\_traceback\_\_***. Este atributo es *writable*, a través del método `with_traceback()`:

```python
raise <excep>('Texto de error').with_traceback(<obj_traceback>)
```

En este caso, hemos levantado una excepción del tipo ***excep***, pasándole el texto del error al constructor, y aportando nuestro propio objeto *traceback*. Lo podemos hacer así porque el método `with_traceback()` retorna el objeto (instancia) excepción.

```python
raise <excep1> from <excep2>
```

En este caso, estamos indicando que levantamos la excepción de tipo ***excep1***, que se ha producido a causa de otra excepción ***excep2***. Si la excepción (***excep1***, que es la que debemos tratar) queda sin tratar, se imprimirá en el mensaje de error las dos excepciones, y que una de ellas es causa de la otra. Al levantarse así, internamente se adjuntará ***excep2*** (que también puede ser instancia o clase) en el atributo ***\_\_cause\_\_*** de la excepción levantada (***excep1***).

De forma similar, si durante el tratamiento de una excepción se produce otra, que no queda tratada, en el mensaje de error se informará de este hecho. Internamente se hace añadiendo a la nueva excepción una referencia a la que estábamos tratando, en el atributo ***\_\_context\_\_***.

### 7.9 The break statement

Sale del *nearest enclosing* `for` o `while`, saltándose el posible `else` que pueda tener el bucle.

Si la sentencia implica salir de una cláusula `try` que tiene una sección `finally`, el código de esta sección se ejecutará antes de salir del bucle.

### 7.10 The continue statement

Produce una nueva iteración del *nearest enclosing* `for` o `while`.

Si la sentencia implica salir de una cláusula `try` que tiene una sección `finally`, el código de esta sección se ejecutará antes de iniciar la nueva iteración.

### 7.11 The import statement

Para importar módulos.

```python
import A as C, B
```

equivale a:

```python
import A as C
import B
```

### 7.12 The global statement

Para *bind* un nombre al *scope* global.

### 7.13 The nonlocal statement

Para *bind* un nombre al *scope* exterior más cercano en el que aparece.

## 8. COMPOUND STATEMENTS

Las sentencias simples se pueden agrupar en la misma línea separándolas por punto y coma (***;***), y opcionalmente terminando la línea en punto y coma.

Una sentencia compuesta está formada por una o varias cláusulas. Una cláusula está formada por un encabezado y una *suite* (una sentencia o conjunto de sentencias simples o compuestas).

El encabezado de una cláusula termina en dos puntos (***:***).

Una *suite* es una o más sentencias agrupadas en una de estas dos formas:

- En la misma línea del encabezado. Si hay más de una, habrá que separarlas por punto y coma (***;***). Como siempre, opcionalmente se puede terminar la línea en punto y coma.
- Debajo del encabezado, una debajo de otra con una indentación superior al encabezado (podemos también agrupar varias sentencias simples en una o más de las líneas indentadas).

Las sentencias compuestas no pueden incluirse en una línea con sentencias separadas por punto y coma.

Sentencias compuestas: `if`, `while`, `for`, `try`, `with`, `def`, `class`.

### 8.6 Function definitions

Aquí hablaremos, sobre todo, de function *decorators*. Un *decorator* es una función que acepta una función como argumento, y que retorna a su vez una función. Es decir, ajusta o manipula la función que le pasamos. La sintaxis de *decorators* es:

```python
@deco
def fun:
    pass
```

Esto equivale a:

```python
def fun:
    pass
fun = deco(fun)
```

¿Cómo se construye un *decorator*? En primer lugar, sabemos que es una función (***deco***) que acepta una función como entrada (***fun***), y retorna otra función (que llamaremos ***wrapper***), que es una modificación de la de entrada. Así, ***deco*** acepta ***fun***, la introduce dentro de ***wrapper*** y devuelve ***wrapper***. La llamaremos ***wrapper***, porque envuelve ***fun***.

Vamos a hacer un *decorator* que duplica las acciones de una función:

```python
def deco_duplica(f):
    def wrapper_duplica():
        f()
        f()
    return wrapper_duplica

@deco_duplica
def fun():
    print('¡Buenos días!')

fun()
```

Resultado:

```
¡Buenos días!
¡Buenos días!
```

¿Y si queremos decorar una función ***fun*** que acepta argumentos? Para que no nos dé error y el *decorator* acepte todo tipo de listas de parámetros, haremos:

```python
def deco_duplica(f):
    def wrapper_duplica(*args, **kwargs):
        f(*args, **kwargs)
        f(*args, **kwargs)
    return wrapper_duplica

@deco_duplica
def fun(nombre):
    print(f'¡Buenos días, {nombre}!')

fun('Pepe')
```

Resultado:

```
¡Buenos días, Pepe!
¡Buenos días, Pepe!
```

Esto es así, porque si nos fijamos, ***deco_duplica*** retorna el objeto función ***wrapper_duplica***, y este objeto función se asigna a ***fun***. Es decir, ***fun*** pasa a ser la función ***wrapper_duplica***. Por lo tanto, al hacer la llamada `fun('Pepe')` estamos, de hecho, invocando al objeto función `wrapper_duplica('Pepe')`.

¿Qué pasa al encadenar *decorators*?

```python
@deco1
@deco2
def foo: pass
```

Esto equivale a `foo = deco1(deco2(foo))`.

A parte de aceptar argumentos en la función que va a ser decorada, en ocasiones podemos querer darle argumentos a nuestro *decorator* en sí. Imaginemos que queremos pasarle el número de veces que queremos repetir la función original. Para definir un *decorator* con argumentos se hace así:

```python
def deco_duplica_n(n):
    def deco_duplica(f):
        def wrapper_duplica(*args, **kwargs):
            for i in range(n):
                f(*args, **kwargs)
        return wrapper_duplica
    return deco_duplica

@deco_duplica_n(4)
def fun(nombre):
    print(f'¡Buenos días, {nombre}!')

fun('Pepe')
```

Resultado:

```
¡Buenos días, Pepe!
¡Buenos días, Pepe!
¡Buenos días, Pepe!
¡Buenos días, Pepe!
```

Vamos a estudiarlo: `deco_duplica_n(4)`, de hecho, nos está retornando esta función:

```python
def deco_duplica(f):
    def wrapper_duplica(*args, **kwargs):
        for i in range(4):
            f(*args, **kwargs)
    return wrapper_duplica
```

Que, de hecho, un simple *decorator* que, en este caso, cuatriplica la acción. Esta función retornada es la que se usa como decorador de la función a decorar, es decir:

```python
fun = deco_duplica_n(4)(fun)
```

Y dado que ***deco_duplica_n(4)()*** retorna una función ***deco_duplica*** que cuatriplica la acción (utiliza un ***n*** con valor ***4***), esto equivale a:

```python
fun = deco_duplica(fun)  # en su versión con n=4
```

Generalizando, tenemos que, suponiendo dos decoradores que acepten argumentos:

```python
@deco1(N)
@deco2(A,B)
def foo(...): pass
```

Esto equivale a:

```python
foo = deco1(N)(deco2(A, B)(foo))
```

Para finalizar, hay que tener en cuenta que si queremos que nuestra función decorada por el *decorator* siga retornando el mismo valor que antes de pasarla por el *decorator*, es decir, si queremos que la decoración no afecte al valor de retorno de la función, entonces la función *wrapper* debe terminar con:

```python
return f(*args,**kwargs)
```

En caso contrario, se puede modificar ese valor de retorno a voluntad.

Un *decorator* puede ser cualquier tipo de expresión válida, siempre y cuando tal expresión retorne una función de tipo *decorator*. Por ejemplo, si tenemos una lista ***lst***, en los que sus elementos tienen un atributo (de tipo función) ***decfoo***, se puede hacer:

```python
@lst[0].decfoo
def fooA():
    ...

@lst[1].decfoo
def fooB():
    ...
```

### 8.7 Class definitions

Comentar simplemente que también se pueden definir *decorators* para los métodos de una clase.

Pero además se pueden utilizar *decorators* de clases, que se utilizan en la clase entera. En este caso, la diferencia estriba en que el *decorator* acepta como argumento una clase, que retorna decorada. Por lo demás, el mecanismo es análogo a los *decorators* de funciones.

```python
@deco
class MiClase:
    pass
```

Equivale a:

```python
MiClase = deco(MiClase)
```

Un *decorator* de un método es, en la práctica, lo mismo que vimos para funciones. En cambio, un *decorator* de clase es una función que recibe como parámetro un objeto clase, y que retorna esa clase modificada.

Veamos un ejemplo: una clase simple que solo dice 'hola', y un *decorator* que añade un método para decir adiós. Además, el *decorator* admite un parámetro que indica cuántas veces se dirá '¡Adiós!' al despedirse:

```python
def complicaN(n):
    def complica(cls):
        cls_complic = cls

        def adios(self):
            print('¡Adiós!' * n)

        cls_complic.adios = adios
        return cls_complic
    return complica

@complicaN(4)
class Simple:
    def hola(self):
        print('¡Hola!')

a = Simple()
a.hola()
a.adios()
```

Resultado:

```
¡Hola!
¡Adiós!¡Adiós!¡Adiós!¡Adiós!
```
