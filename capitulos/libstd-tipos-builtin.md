# Biblioteca estándar: tipos *builtin*

El presente capítulo es un resumen de la parte específica de la biblioteca estándar dedicada a los tipos incorporados en *Python* 3.11.

## 4. BUILT-IN TYPES

(Tipos incorporados.)

### 4.1 Truth Value Testing

(Comprobación del valor de verdad.)

Por defecto, un objeto se considera ***True*** (verdadero), a no ser que defina `__bool__()` y este retorne ***False*** (falso), o que en su defecto defina `__len__()` y este retorne 0. Por otro lado se consideran ***False*** las constantes ***None*** y ***False***, así como cualquier objeto numérico con valor 0.

### 4.2 Boolean Operations - and, or, not

(Operaciones booleanas - `and`, `or`, `not`.)

Los operadores `and` y `or` cortocircuitan. El operador `not` tiene menos prioridad que los operadores no booleanos, con lo que `not a == b` equivale a `not (a == b)`, mientras que `a == not b` es un error sintáctico.

### 4.3 Comparisons

(Comparaciones.)

Las comparaciones `is` e `is not` trabajan con la identidad de los objetos y no pueden ser redefinidas con métodos.

`in` y `not in` solo funcionan con iterables o con objetos que implementen `__contains__()`.

### 4.4 Numeric Types - int, float, complex

(Tipos numéricos - `int`, `float`, `complex`.)

A parte de estos tipos, `bool` es una subclase de `int`.

#### 4.4.1 Bitwise Operations on Integer Types

(Operaciones bit a bit en tipos enteros.)

Las operaciones *bitwise* solo tienen sentido en enteros. Se realizan como si fuesen números en complemento a 2 con infinitos dígitos.

#### 4.4.2 Additional Methods on Integer Types

(Métodos adicionales en tipos enteros.)

```python
int.bit_count()
```

Retorna el número de unos de la representación binaria del valor absoluto del entero.

```python
int.bit_length()
```

Retorna el número de bits necesarios para representar el valor absoluto del valor actual del entero. Si su valor es 0, retorna 0.

```python
int.to_bytes(length=1, byteorder='big', *, signed=False)
```

Retorna un *array* de *bytes* representando el valor del entero. El objeto *bytes* retornado tiene una longitud total de ***length*** bytes. Si esa longitud no es suficiente para su representación, se levanta la excepción ***OverflowError***.

Si ***byteorder*** (*string*) es ***big***, será con representación *big endian* (*bytes* más significativos primero). Si es ***little***, será *little endian*. Para que se use la representación nativa del sistema, se indicará el valor de ***sys.byteorder***.

En cuanto a ***signed***, indica si se va a utilizar complemento a 2 para codificar el entero. Si es que no (por defecto) y el valor es negativo, se levantará ***OverflowError***.

```python
classmethod int.from_bytes(bytes, byteorder=’big’, *,
                           signed=False)
```

Es la operación inversa a `to_bytes()`, aunque en este caso se trata de un *class method*. Retorna un entero.

***byteorder*** tiene el mismo uso que en el caso de `to_bytes()`.

***bytes*** debe ser un objeto *bytes* o un iterable que produzca una serie de *bytes*. ***signed*** indica si se ha utilizado complemento a 2 en la representación.

```python
int.as_integer_ratio()
```

Retorna una tupla con un ratio igual al valor del entero. Esa tupla lo forman dos enteros: el primero es igual al entero original, y el segundo es 1.

#### 4.4.3 Additional Methods on Float

(Métodos adicionales en flotantes.)

```python
float.as_integer_ratio()
```

Retorna una tupla con un ratio de enteros que iguala el valor del `float`. El denominador (segundo elemento) es siempre positivo.

Si el `float` es un infinito, levanta ***OverflowError***. Si es ***NaN***, ***ValueError***.

```python
float.is_integer()
```

Retorna ***True*** si el valor es entero (parte decimal a 0), o ***False*** si no.

```python
float.hex()
```

Retorna una representación hexadecimal del `float`, en un *string*, que empezará por ***0x***, e incluirá un sufijo ***p*** con el exponente (decimal, sobre una base 2).

```python
classmethod float.fromhex(s)
```

A partir del *string*, que representa un `float`, retorna ese `float`. El formato del *string* debe ser (puede ir entre espacio en blanco):

```
[signo] ['0x'] número ['p' exponente]
```

El número es hexadecimal y puede incluir punto decimal o no. Si lo incluye, se puede omitir la parte entera o la fraccionaria, pero no las dos.

El exponente es un número decimal (con o sin signo) que se calculará utilizando una base 2.

El uso de mayúsculas o minúsculas es indiferente.

Un ejemplo: ***3.a82p5*** se calcula así:

```python
(3 + 10 / 16 + 8 / (16 ** 2) + 2 / (16 ** 3)) * (2 ** 5)
```

#### 4.4.4 Hashing of numeric types

(*Hash* de tipos numéricos.)

Dos números ***x*** e ***y*** deben tener el mismo *hash* (`hash(x) == hash(y)`) siempre que `x == y`. *Python* aplica una función que otorga un *hash* único a cada número racional.

### 4.5 Iterator Types

(Tipos iterador.)

Python facilita la iteración de todo tipo de contenedores (secuencias, *sets*, diccionarios, etc.), es decir, de objetos iterables.

```python
container.__iter__()
```

Retorna un objeto iterador basado en el contenedor. Es el método utilizado por `for` para crear el iterador.

```python
iterator.__iter__()
```

Normalmente retorna una referencia a sí mismo. Esto permite que `for` se pueda aplicar no solo en contenedores, sino también en iteradores.

```python
iterator.__next__()
```

Retorna el valor de la siguiente iteración o levanta la excepción ***StopIteration*** si ya ha terminado.

### 4.6 Sequence Types - list, tuple, range

(Tipos secuencia: lista, tupla, rango.)

Además de las listas, tuplas y los rangos, los *strings* y *bytes* (y *bytearrays*) también son secuencias.

#### 4.6.1 Common Sequence Operations

(Operaciones comunes en secuencias.)

Dadas las secuencias ***s*** y ***t***, un posible elemento ***x***, y los enteros ***n***, ***i***, ***j*** y ***k***, las siguientes operaciones son comunes a todas las secuencias:

- `x in s` o `x not in s` (pertenencia).
- `s + t` (concatenación).
- `s * n` o `n * s` (repetición).
- `s[i]` (indexación).
- `s[i:j]` y `s[i:j:k]` (*slicing*).
- `len(s)` (longitud).
- `min(s)` y `max(s)` (mínimo/máximo).
- `s.index(x[, i[, j]])` (índice de la primera ocurrencia de ***x***, opcionalmente restringido a los elementos entre ***i*** y ***j***).
- `s.count(x)` (ocurrencias de ***x*** en ***s***).

Las secuencias del mismo tipo se pueden comparar entre sí. Si son de tipo distinto se pueden comparar por igualdad, pero se evalúa como ***False***.

Algunas secuencias pueden utilizar el operador `in` para hallar subsecuencias:

```python
>>> "gg" in "eggs"
True
```

En la repetición (`n * s`), cualquier entero menor a 1 retorna una secuencia vacía. Los elementos duplicados son referencias a los originales:

```python
>>> lists = [[]] * 3    # lista con una lista vacía dentro
>>> lists
[[], [], []]    # lista con 3 listas vacías
>>> lists[0].append(3)
>>> lists
[[3], [3], [3]]
```

Al concatenar secuencias inmutables, se crea un nuevo objeto cada vez, con lo que si tenemos varias concatenaciones consecutivas (`s + t + u + v,...`), en cada ***+*** se vuelve a crear un objeto. Alternativas: para *strings* o *bytes*, se puede usar el método `join()` de estos tipos; usar *bytearrays* en lugar de *bytes*; extender en lugar de concatenar; en tuplas, ir extendiendo una lista auxiliar; etc.

Los *ranges* no aceptan concatenación ni repetición.

#### 4.6.2 Immutable Sequence Types

(Tipos de secuencia inmutable.)

Las únicas operaciones específicas de las secuencias inmutables son las relacionadas con el *hashing*, de tal modo que un objeto de estos tipos puede usarse en la función `hash()` como argumento.

#### 4.6.3 Mutable Sequence Types

(Tipos de secuencia mutable.)

Dada la secuencia mutable ***s***, el iterable ***t***, un posible elemento ***x***, y los enteros ***n***, ***i***, ***j*** y ***k***, las siguientes operaciones son comunes a todas las secuencias mutables:

- `s[i] = x` reemplaza el elemento ***i*** de ***s*** por ***x***.
- `s[i:j] = t` modifica un *slice* de ***s*** con el contenido de ***t***.
- `del s[i:j]` equivale a `s[i:j] = []`.
- `s[i:j:k] = t` reemplaza los elementos del *slice* indicado por los de ***t***, el cual debe contener el mismo número de elementos que el *slice*.
- `del s[i:j:k]` elimina los elementos del *slice* indicado de la secuencia.
- `s.append(x)` añade ***x*** al final de la secuencia. Equivale a `s[len(s):len(s)] = [x]`.
- `s.clear()` elimina todos los elementos de la secuencia (equivale a `del s[:]`). Útil en secuencias que no admiten *slicing*.
- `s.copy()` crea una *shallow copy* de ***s*** (equivale a `s[:]`). Útil en secuencias que no admiten *slicing*.
- `s.extend(t)` o `s += t` amplía ***s*** con el contenido de ***t*** (equivalente a `s[len(s):len(s)] = t`).
- `s *= n` actualiza ***s*** con su contenido repetido ***n*** veces. Recordemos que las repeticiones son referencias a los objetos originales. Con ***n*** menor a 1, resulta una secuencia vacía.
- `s.insert(i, x)` inserta ***x*** en ***s*** en la posición indicada por ***i*** (equivale a `s[i:i] = [x]`).
- `s.pop(i=-1)` retorna el elemento en la posición ***i*** y lo elimina de ***s***. Si no se indica argumento, lo hace con el último elemento.
- `s.remove(x)` elimina el primer elemento de ***s*** tal que `s[i] == x`. Si no lo encuentra se levanta ***ValueError***.
- `s.reverse()` invierte los elementos de la secuencia. Lo hace in situ, con lo que actúa por *side effect*, no retorna nada.

#### 4.6.4 Lists

(Listas.)

Se puede construir una lista (secuencia mutable) mediante corchetes (con o sin elementos, separados por comas), *list comprehensions*, o mediante el constructor:

```python
list([iterable])
```

La lista creada mediante el constructor contendrá referencias a los elementos del iterable. Por ejemplo, si este es en sí una lista, retorna una *shallow copy* de la misma.

Las listas tienen un método adicional:

```python
list.sort(*, key=None, reverse=False)
```

Este método ordena la lista in situ (no retorna nada). Por defecto solo compara el valor de los elementos tal cual, a no ser que le indiquemos en el parámetro ***key*** una función que, tomando un argumento, retorne el valor que se usará en la ordenación (solo se evaluará dicha función una vez por cada elemento a ordenar).

Si el argumento ***reverse*** es verdadero, la ordenación se hará en orden inverso.

Dos elementos que se evalúen igual, quedarán en el mismo orden relativo que tenían al principio.

Si la función levanta una excepción, la lista puede quedar a medio ordenar.

#### 4.6.5 Tuples

(Tuplas.)

Secuencias inmutables. Se pueden crear mediante un par de paréntesis sin contenido (tupla vacía); un elemento seguido de coma (*singleton*), entre paréntesis o no; varios elementos separados por comas, entre paréntesis o no; o mediante el constructor `tuple([iterable])`.

La tupla se creará con referencias a los elementos del iterable.

#### 4.6.6 Ranges

(Rangos.)

Secuencia inmutable de números. Puede contener números negativos. Ocupa poca memoria, ya que no guarda todos los números de la secuencia, sino únicamente sus parámetros (***start***, ***stop*** y ***step***).

Los *ranges* admiten indexado, *slicing*, etc.

```python
>>> r = range(0, 20, 2)
>>> r
range(0, 20, 2)
>>> 11 in r
False
>>> 10 in r
True
>>> r.index(10)
5
>>> r[5]
10
>>> r[:5]
range(0, 10, 2)
>>> r[-1]
18
```

Los rangos se pueden comparar entre sí, y resultan iguales si generan la misma secuencia de números. En este sentido `range(0) == range(2,1,3)` y `range(0,3,2) == range(0,4,2)`.

### 4.7 Text Sequence Type - str

(Tipo secuencia de texto - ***str***.)

Secuencia inmutable de caracteres. Se puede construir a través de literales o mediante el constructor `str()`.

```python
class str(object='')
class str(object=b'', encoding='utf-8', errors='strict')
```

Si no se le da argumento, construye un *string* vacío. Si le damos un objeto cualquiera como argumento, creará un *string* en base a ese objeto: si es posible, será lo que retorne el método `__str__()` del objeto. Si no lo tiene, se usará `__repr__()`.

Si se le pasa el argumento ***encoding*** y/o ***errors*** al constructor, el objeto debe ser de datos binarios (*bytes* o *bytearray*).

Si ***object*** es un objeto **binario** que representa una codificación concreta de un *string* (por ejemplo, un *string* codificado en *UTF-8* en un objeto *bytes*) y no se proporciona el argumento ***encoding***, el resultado será normalmente (según el método `__str__()` o `__repr__()` del objeto binario) una conversión literal donde cada byte será convertido a un carácter (en todo caso, nada tendrá que ver con la codificación concreta). En cambio, si le indicamos ***encoding***, descodificará los datos binarios de acuerdo con la codificación especificada.

Si indicamos ***encoding***, podemos indicar también ***errors***. Véase la descripción de `open()` para más detalles acerca de estos argumentos.

#### 4.7.1 String methods

(Métodos de *string*.)

Veremos aquí los métodos específicos de los *strings*.

Existen dos técnicas para dar formato a los *strings*: el del método `str.format()`, y el basado en la función `printf()` del lenguaje *C* (estilo antiguo). El primero es el aconsejado.

```python
str.capitalize()
```

Retorna una copia del *string*, con el primer carácter en mayúsculas y el resto en minúsculas.

```python
str.casefold()
```

Como `str.lower()`, pero extendido a otras conversiones en algunos caracteres *Unicode*.

```python
str.center(width[, fillchar])
```

Retorna un *string* de longitud ***width*** con el *string* original centrado en él. Se rellena con el carácter ***fillchar*** (por defecto, espacio). Si el *string* original tiene longitud de ***width*** o más caracteres, se retorna una copia del original.

```python
str.count(sub[, start[, end]])
```

Retorna el número de veces (no solapadas) que el *substring* ***sub*** aparece en el *string* original. Podemos limitar la búsqueda si especificamos ***start*** y ***end*** (que se interpretan como en un *slice*).

```python
str.encode(encoding='utf-8', errors='strict')
```

Retorna la codificación del *string* en un objeto *bytes*. ***encoding*** y ***errors*** tienen el significado habitual (véase función `open()`).

```python
str.endswith(suffix[, start[, end]])
```

Retorna ***True*** si el *string* termina en el *string* ***suffix***, o, en caso de que este argumento sea una tupla de *strings*, si termina en alguno de sus elementos.

Los argumentos opcionales (interpretados como en un slice) ***start*** y ***end*** sirven para limitar la búsqueda dentro del *string*.

```python
str.expandtabs(tabsize=8)
```

Retorna una copia del *string* con los tabuladores sustituidos por espacios. El número de espacios por el que será sustituido cada tabulador vendrá dado por ***tabsize*** y por la columna actual (se expandirá hasta el siguiente *tab stop*, que será un múltiplo de ***tabsize***).

```python
str.find(sub[, start[, end]])
```

Retorna el índice más bajo donde el *substring* ***sub*** se encuentra dentro del *string* actual, el cual se puede opcionalmente acotar al *slice* ***[start:end]***. Si no se encuentra, retorna -1. Si solo queremos saber si el *substring* aparece, sin importarnos la posición, es mejor usar el operador `in`.

```python
str.format(*args, **kwargs)
```

Formatea un *string*, dando valor a los campos delimitados por llaves ***{}***. Véase el apartado de especificación de formato.

```python
str.format_map(mapping)
```

Similar a utilizar `str.format(**mapping)`, con la diferencia que este método recibe (en lugar de *keyword arguments*) un solo argumento, de tipo *mapping* (normalmente un diccionario). Véase el apartado de especificación de formato.

```python
str.index(sub[, start[, end]])
```

Como `str.find()`, pero si no lo encuentra, levanta excepción ***ValueError***.

```python
str.isalnum()
```

Retorna ***True*** si todos los caracteres del *string* son alfanuméricos, y el *string* no está vacío. ***False*** en caso contrario.

```python
str.isalpha()
```

Retorna ***True*** si todos los caracteres del *string* son alfabéticos, y el *string* no está vacío. ***False*** en caso contrario. Estos caracteres son los que están definidos como *Letter* en la tabla *Unicode*.

```python
str.isascii()
```

Retorna ***True*** si todos los caracteres del *string* son *ASCII*, o el *string* está vacío. ***False*** en caso contrario. Los caracteres *ASCII* son los *codepoints* ***U+0000*** a ***U+007F***.

```python
str.isdecimal()
```

Retorna ***True*** si todos los caracteres del *string* son números (***0*** a ***9***), y el *string* no está vacío. ***False*** en caso contrario.

```python
str.isdigit()
```

Como `str.isdecimal()`, pero extensivo a otros caracteres *Unicode*.

```python
str.isidentifier()
```

Retorna ***True*** si el *string* forma un nombre válido de identificador. Si no, ***False***.

```python
str.islower()
```

Retorna ***True*** si todos los caracteres del *string* que tienen una versión minúscula están efectivamente en minúsculas, y el *string* contiene por lo menos un carácter con versión minúscula. Si no, ***False***.

```python
str.isnumeric()
```

Como `str.isdecimal()`, pero extensivo a otros caracteres *Unicode*.

```python
str.isprintable()
```

Retorna ***True*** si todos los caracteres del *string* son imprimibles, o el *string* está vacío. ***False*** en caso contrario. Los caracteres no imprimibles son aquellos que hay que indicar *escaped*.

```python
str.isspace()
```

Retorna ***True*** si todos los caracteres del *string* pertenecen a los distintos tipos de espacio en blanco, y el *string* no está vacío. Si no, ***False***.

```python
str.istitle()
```

Retorna ***True*** si el *string* está *title-cased* (todas las palabras empiezan en mayúscula y siguen en minúscula), y el *string* no está vacío. Si no, ***False***.

```python
str.isupper()
```

Retorna ***True*** si todos los caracteres del *string* que tienen una versión mayúscula están efectivamente en mayúsculas, y el *string* contiene por lo menos un carácter con versión mayúscula. Si no, ***False***.

```python
str.join(iterable)
```

Retorna un *string* que es la concatenación de todos los *strings* del iterable, y como separador, el contenido del *string* llamante. El iterable solo puede contener *strings*.

```python
str.ljust(width[, fillchar])
```

Como `str.center()` pero a la izquierda.

```python
str.lower()
```

Retorna una copia del *string* con todos los caracteres que tienen versión minúscula convertidos en minúscula.

```python
str.lstrip([chars])
```

Como `str.strip()` pero solo en el principio del *string*.

```python
static str.maketrans(x[, y[, z]])
```

Retorna una tabla (diccionario) que se podrá utilizar en `str.translate()`.

Si hay un solo argumento, debe ser un diccionario que mapee ordinales *Unicode* (números enteros) o caracteres (*strings* de longitud 1) a ordinales *Unicode*, *strings* (de cualquier tamaño) o ***None***.

Si hay dos argumentos, deben ser dos *strings* de la misma longitud, y en la tabla retornada cada uno de los caracteres del primero se mapearán al carácter correspondiente del segundo. Si hay un tercer argumento, debe ser otro *string*, cuyos caracteres se mapearán todos a ***None***.

En todos los casos, en el diccionario resultante todas las claves serán ordinales *Unicode*.

```python
str.partition(sep)
```

Busca la primera ocurrencia del *substring* indicado en ***sep*** dentro del *string* y retorna una tupla con 3 *strings*: el fragmento anterior a ***sep***, ***sep*** en sí y el fragmento posterior. Si no encuentra ***sep***, el primer *string* de la tupla será una copia del original, y los otros dos serán *strings* vacíos.

```python
str.removeprefix(prefix, /)
```

Si el *string* empieza por ***prefix***, retorna un *string* sin este prefijo. De lo contrario, retorna el *string* original.

```python
str.removesuffix(suffix, /)
```

Si el *string* acaba con ***suffix***, retorna un *string* sin este sufijo. De lo contrario, retorna el *string* original.

```python
str.replace(old, new[, count])
```

Retorna una copia del *string*, en la que cada ocurrencia del *substring* ***old*** es reemplazada por ***new***. Si se especifica ***count***, será el número máximo de reemplazos (en orden de lectura).

```python
str.rfind(sub[, start[, end]])
```

Como `str.find()` pero empezando a buscar por la derecha (índice más alto).

```python
str.rindex(sub[, start[, end]])
```

Como `str.rfind()` pero levanta ***ValueError*** si no encuentra ***sub***.

```python
str.rjust(width[, fillchar])
```

Como `str.ljust()` pero justificando a la derecha.

```python
str.rpartition(sep)
```

Como `str.partition()`, pero empezando a buscar por la derecha.

```python
str.rsplit(sep=None,maxsplit=-1)
```

Como `str.split()`, pero empezando los *splits* por la derecha (si no se especifica ***maxsplit*** no hay diferencia).

```python
str.rstrip([chars])
```

Como `str.strip()` pero solo en el final del *string*.

```python
str.split(sep=None, maxsplit=-1)
```

Retorna una lista con fragmentos del *string* original. Los fragmentos se hacen en base al *substring* separador ***sep***, que puede tener cualquier longitud, y no aparece en la lista retornada. Si se indica ***maxsplit***, ese será el número máximo de fragmentaciones, es decir, se retornará un máximo de ***maxsplit***+1 *strings*.

Si hay ocurrencias consecutivas de ***sep*** dentro del *string* original, se interpretan como si estuviesen separando *strings* vacíos.

Si no se especifica el separador, cualquier secuencia de caracteres de espacio es considerada un separador.

Si el *string* original está vacío y especificamos separador, el resultado será una lista con un *string* vacío. En cambio, si no especificamos separador, será una lista vacía. Si el *string* solo contiene espacios y no indicamos separador, también obtendremos una lista vacía.

```python
str.splitlines([keepends])
```

Retorna una lista con las líneas del *string*. Los caracteres *newline* no se incluyen, a no ser que ***keepends*** (opcional) sea ***True***.

```python
str.startswith(prefix[, start[, end]])
```

Igual que `str.endswith()` pero en el principio del *string*.

```python
str.strip([chars])
```

Retorna un *string* que elimina del principio y final del *string* original todos los caracteres incluidos en el *string* ***chars***, que por defecto son los caracteres de espacio.

```python
str.swapcase()
```

Retorna un *string* en el que las minúsculas pasan a ser mayúsculas y viceversa.

```python
str.title()
```

Retorna una versión *title cased* del *string* (cada palabra empieza en mayúscula y el resto son minúsculas).

```python
str.translate(table)
```

Retorna un *string* con sus caracteres mapeados según la tabla ***table*** (se puede utilizar `str.maketrans()` para construirla).

El objeto tabla mapeará según se ha explicado en `str.maketrans()`. El método `__getitem__()` de la tabla levantará ***LookupError*** para que un carácter sea mapeado a sí mismo.

```python
str.upper()
```

Como `str.lower()` pero para mayúsculas.

```python
str.zfill(width)
```

Retorna un *string* relleno de ceros por la izquierda hasta completar la anchura ***width***. Si el *string* original representa un número válido y tiene signo al principio, el signo se mantendrá también al principio. Si ***width*** es igual o inferior al tamaño del *string* original, se retorna una copia de este.

#### 4.7.2 printf-style String Formatting

(Formateo de *strings* con estilo `printf`.)

Aunque esta modalidad es considerada antigua y se prefiera usar `str.format()` (y *f-strings*), no parece que haya planes de marcar esta característica como obsoleta de momento.

Este tipo de formato se realiza mediante el operador módulo:

```
formato % valores
```

Aquí ***formato*** es el *string* a rellenar y ***valores*** es una tupla con todos los valores necesarios para rellenarlo.  Si solo es necesario un valor puede no ser una tupla (bastará un simple valor).

Dentro del *string*, cada especificador se compone del carácter ***%*** y el carácter de tipo de conversión, que será:

- ***d*** - entero decimal con signo.
- ***i*** - igual que ***d***.
- ***o*** - entero octal con signo.
- ***u*** - obsoleto.
- ***x*** - entero hexadecimal con signo (minúsculas).
- ***X*** - entero hexadecimal con signo (mayúsculas).
- ***e*** - punto flotante con formato exponencial (minúsculas).
- ***E*** - punto flotante con formato exponencial (mayúsculas).
- ***f*** - punto flotante con formato decimal.
- ***F*** - igual que ***f***.
- ***g*** - punto flotante. Formato exponencial (minúsculas) si el exponente es menor a -4 o no es menor que la precisión indicada. Si no, formato decimal.
- ***G*** - punto flotante. Formato exponencial (mayúsculas) si el exponente es menor a -4 o no es menor que la precisión indicada. Si no, formato decimal.
- ***c*** - carácter (*string* de longitud 1 o un entero).
- ***r*** - *string* (lo convierte usando `repr()`).
- ***s*** - *string* (lo convierte usando `str()`).
- ***a*** - *string* (lo convierte usando `ascii()`).

Estas conversiones tienen una forma alternativa que se utiliza si así se especifica. Son estas:

- En el caso de ***o***, se inserta ***0o*** como prefijo.
- En ***x*** y ***X***, se incluye el prefijo ***0x*** o ***0X***.
- En ***e***, ***E***, ***f*** y ***F***, se incluye siempre el punto decimal.
- En ***g*** y ***G***, se incluye siempre punto decimal y no se eliminan los ceros finales.
- En ***r***, ***s*** y ***a***, si se especifica precisión, el resultado se trunca a esta.

Para imprimir un carácter ***%*** hay que usar ***%%*** en el *string*.

Entre el carácter % y el carácter de tipo puede haber varios modificadores opcionales, que hay que incluir **en el orden indicado**:

1. Cuando el parámetro de valores es un *mapping*, hay que incluir en cada especificador la clave del elemento entre paréntesis:

```python
>>> print('%(language)s has %(number)03d quote types.' %
...     {'language': "Python", "number": 2})
Python has 002 quote types.
```

2. Flags de conversión:
    - ***#*** - la conversión usará la forma alternativa.
    - ***0*** - los números serán rellenados con ceros a la izquierda (según anchura especificada).
    - ***-*** - la conversión se ajustará a la izquierda (overrides ***0***).
    - ***\<espacio>*** - si es una conversión con signo y el número resultante es positivo, se deja un espacio en blanco justo antes del número.
    - ***+*** - en números, se incluye siempre signo (***+*** o ***-***) antes del número (*overrides* espacio).
3. Anchura mínima: se especifica en un número entero, o un ***\****. En este caso, dicho número se lee del siguiente objeto de la tupla de valores.
4. Precisión: indicado mediante un punto (***.***) seguido de un número entero, o un ***\****, en cuyo caso se lee tal número del siguiente objeto en la tupla de valores.\
En el caso de ***E***, ***e***, ***F*** y ***f***, indica el número de dígitos decimales (si no se indica, es 6). En el caso de ***g*** y ***G***, indica el número de dígitos significativos, incluyendo antes y después del punto decimal (si no se indica, es 6).
5. Longitud: puede ser ***h***, ***l*** o ***L***, pero *Python* no lo usa para nada.

### 4.8 Binary Sequence Types - bytes, bytearray, memoryview

(Tipos de secuancia binaria - ***bytes***, ***bytearray***, ***memoryview***.)

Estos tipos de datos son los utilizados para acceder a datos binarios (*bytes* en lugar de caracteres).

#### 4.8.1 Bytes Objects

(Objetos *bytes*.)

Solo aceptan caracteres *ASCII*. Lo que supere el valor 127 se debe indicar *escaped*. Internamente, son secuencias inmutables de enteros entre 0 y 255.

A parte de con literales, podemos construir un objeto *bytes* a través de su constructor:

```python
class bytes([source[, encoding[, errors]]])
```

Si le pasamos un entero (***source***), construye un objeto *bytes* relleno de ceros, con la longitud especificada: `bytes(10)` retornará un objeto *bytes* con 10 *bytes* a cero. Si en su lugar le pasamos un iterable, este debe contener los valores de los *bytes* con los que se rellenará el nuevo objeto *bytes*. Si le pasamos datos binarios directamente, los copia utilizando el protocolo *buffer* (se verá más adelante).

Si le pasamos un *string*, es obligatorio indicar la codificación (***encoding***) para que codifique los caracteres adecuadamente. En ese caso también podremos indicar ***errors*** (véase `open()`).

¿Qué es el **protocolo** ***buffer***? Se trata de un modo de acceder rápidamente y sin sobrecoste (*overhead*) a los datos binarios, directamente en memoria. Este acceso se realiza a bajo nivel (a nivel de *C*). Es el protocolo utilizado por los objetos *memoryview*, que acceden a las direcciones de memoria donde están los datos, directamente, sin crear objetos intermedios para el acceso a esos datos.

```python
classmethod bytes.fromhex(string)
```

Este método retorna un objeto *bytes* a partir de un *string* que contiene una ristra de valores hexadecimales. Los espacios son ignorados. Debe contener un número par de dígitos, ya que cada dos dígitos hexadecimales generarán un *byte*.

```python
bytes.hex([sep[, bytes_per_sep]])
```

Retorna un *string* con un número hexadecimal fruto de la codificación del objeto *bytes*. Cada *byte* generará dos dígitos en el *string*. Opcionalmente, podemos indicar un carácter separador ***sep*** para hacerlo más legible. En ese caso podemos agrupar un número de *bytes* determinado entre cada separador, indicando dicho número en ***bytes_per_sep***: un número positivo empieza a agrupar por la derecha, y negativo por la izquierda.

Al contrario de lo que ocurría con los *strings*, si ***b*** es un objeto *bytes*, `b[0]` es de tipo entero (entre 0 y 255), mientras que `b[0:1]` es un objeto *bytes* de longitud 1.

#### 4.8.2 Bytearray Objects

(Objetos *bytearray*.)

Contraparte mutable de *bytes*. Usa los mismos literales que *bytes*, pero **soporta los métodos para secuencias mutables**. Por lo demás, funciona del mismo modo que *bytes*.

```python
class bytearray([source[, encoding[, errors]]])
```

Igual que en el caso de *bytes*, solo que retorna un objeto *bytearray*.

```python
classmethod bytearray.fromhex(string)
```

Igual que en el caso de *bytes*, solo que retorna un objeto *bytearray*.

```python
bytearray.hex([sep[, bytes_per_sep]])
```

Igual que en el caso de *bytes*.

#### 4.8.3 Bytes and Bytearray Operations

(Operaciones con *bytes* y *bytearray*.)

Tanto *bytes* como *bytearray* soportan los métodos comunes a las secuencias.

Los métodos de estos tipos aceptan argumentos de cualquiera de los dos tipos, es decir, se pueden intercambiar objetos *bytes* y *bytearray* en estas llamadas. Debido a ellos, nos referiremos a cualquiera de los dos tipos con el término *BLO* (*bytes-like object*).

Los siguientes métodos están disponibles tanto para *bytes* como para *bytearray*. Cuando se dice que retornan *BLOs* o toman *BLOs* como argumento, se entiende que estamos hablando del tipo del objeto o clase a través del que se realiza la llamada.

```python
bytes.count(sub[, start[, end]])
bytearray.count(sub[, start[, end]])
```

Retorna el número de veces (no solapadas) que la *subsecuencia* ***sub*** aparece en la secuencia original. Podemos limitar la búsqueda si especificamos ***start*** y ***end*** (que se interpretan como en un *slice*).

***sub*** puede ser un *BLO* o un entero (entre 0 y 255).

```python
bytes.decode(encoding='utf-8', errors='strict')
bytearray.decode(encoding='utf-8', errors='strict')
```

Retorna un *string* con la decodificación del *BLO* actual. ***encoding*** y ***errors*** tienen el significado habitual (véase `open()`).

```python
bytes.endswith(suffix[, start[, end]])
bytearray.endswith(suffix[, start[, end]])
```

Retorna ***True*** si el *BLO* termina en ***suffix***, o, en caso de que este argumento sea una tupla de *BLOs*, si termina en alguno de estos *BLOs*.

Los argumentos opcionales (interpretados como en un slice) ***start*** y ***end*** sirven para limitar la búsqueda.

```python
bytes.find(sub[, start[, end]])
bytearray.find(sub[, start[, end]])
```

Retorna el índice más bajo donde la subsecuencia ***sub*** se encuentra dentro del *BLO* actual, el cual se puede opcionalmente acotar al *slice* ***[start:end]***. Si no se encuentra, retorna -1. Si solo queremos saber si la subsecuencia aparece en la original, sin importarnos la posición, es mejor usar el operador `in`.

La subsecuencia puede ser también un entero (entre 0 y 255).

```python
bytes.index(sub[, start[, end]])
bytearray.index(sub[, start[, end]])
```

Como `bytes.find()` (o `bytearray.find()`), pero si no lo encuentra, levanta ***ValueError***.

```python
bytes.join(iterable)
bytearray.join(iterable)
```

Retorna un *bytes* o *bytearray* (dependiendo del tipo del objeto llamante) que es la concatenación de todos los *BLOs* del iterable, y como separador, el contenido del *BLO* llamante. El iterable solo puede contener *BLOs*.

```python
static bytes.maketrans(from, to)
static bytearray.maketrans(from, to)
```

Retorna una tabla (diccionario) que se podrá utilizar en `bytes.translate()` (o `bytearray.translate()`).

Los argumentos son dos *BLO* de la misma longitud, y la tabla se generará de tal modo que cada *byte* de ***from*** se mapeará al correspondiente *byte* en ***to***.

```python
bytes.partition(sep)
bytearray.partition(sep)
```

Busca la primera ocurrencia del *BLO* indicado en ***sep*** dentro del *BLO* actual y retorna una tupla con 3 *BLOs*: el fragmento anterior a ***sep***, ***sep*** en sí y el fragmento posterior. Si no encuentra ***sep***, el primer *BLO* de la tupla será una copia del original, y los otros dos serán *BLOs* vacíos.

```python
bytes.removeprefix(prefix, /)
bytearray.removeprefix(prefix, /)
```

Si el *BLO* empieza por ***prefix***, retorna un *BLO* sin este prefijo. De lo contrario, retorna el *BLO* original.

```python
bytes.removesuffix(suffix, /)
bytearray.removesuffix(suffix, /)
```

Si el *BLO* acaba con ***suffix***, retorna un *BLO* sin este sufijo. De lo contrario, retorna el *BLO* original.

```python
bytes.replace(old, new[, count])
bytearray.replace(old, new[, count])
```

Retorna una copia del *BLO*, en la que cada ocurrencia de la subsecuencia ***old*** es reemplazada por ***new***. Si se especifica ***count***, será el número máximo de reemplazos (en orden de lectura).

```python
bytes.rfind(sub[, start[, end]])
bytearray.rfind(sub[, start[, end]])
```

Como `bytes.find()` o `bytearray.find()` pero empezando a buscar por la derecha (índice más alto).

```python
bytes.rindex(sub[, start[, end]])
bytearray.rindex(sub[, start[, end]])
```

Como `bytes.index()` o `bytearray.index()` pero empezando a buscar por la derecha (índice más alto).

```python
bytes.rpartition(sep)
bytearray.rpartition(sep)
```

Como `bytes.partition()` o `bytearray.partition()`, pero empezando a buscar por la derecha.

```python
bytes.startswith(prefix[, start[, end]])
bytearray.startswith(prefix[, start[, end]])
```

Igual que `bytes.endswith()` o `bytearray.endswith()` pero en el principio del *BLO*.

```python
bytes.translate(table,/,delete=b'')
bytearray.translate(table,/,delete=b'')
```

Retorna un *BLO* con sus caracteres mapeados según la tabla ***table*** (se puede utilizar `bytes.maketrans()` o `bytearray.maketrans()` para construirla).

La secuencia ***delete*** nos indica qué bytes serán eliminados en la secuencia resultante. Si solo queremos eliminar elementos, podemos indicar ***None*** en ***table***.

##### Métodos para ASCII

Los métodos que veremos a continuación están pensados para *BLOs* que contengan caracteres *ASCII*, aunque se pueden utilizar para cualquier contenido:

```python
bytes.center(width[, fillbyte])
bytearray.center(width[, fillbyte])
```

Retorna un *BLO* de longitud ***width*** con el *BLO* original centrado en él. Se rellena con el *byte* ***fillbyte*** (por defecto, espacio *ASCII*). Si el *BLO* original tiene una longitud de ***width*** o más *bytes*, se retorna una copia del original.

```python
bytes.ljust(width[, fillbyte])
bytearray.ljust(width[, fillbyte])
```

Como `bytes.center()` o `bytearray.center()` pero alineando la izquierda.

```python
bytes.lstrip([chars])
bytearray.lstrip([chars])
```

Como `bytes.strip()` o `bytearray.strip()` pero solo en el principio del *BLO*.

```python
bytes.rjust(width[, fillbyte])
bytearray.rjust(width[, fillbyte])
```

Como `bytes.center()` o `bytearray.center()` pero alineando la derecha.

```python
bytes.rsplit(sep=None,maxsplit=-1)
bytearray.rsplit(sep=None,maxsplit=-1)
```

Como `bytes.split()` o `bytearray.split()`, pero empezando los *splits* por la derecha (si no se especifica ***maxsplit*** no hay diferencia).

```python
bytes.rstrip([chars])
bytearray.rstrip([chars])
```

Como `bytes.strip()` o `bytearray.strip()`, pero solo en el final del *string*.

```python
bytes.split(sep=None,maxsplit=-1)
bytearray.split(sep=None,maxsplit=-1)
```

Retorna una lista con fragmentos del *BLO* original. Los fragmentos se hacen en base a la subsecuencia separador ***sep***, que puede tener cualquier longitud, y no aparece en la lista retornada. Si se indica ***maxsplit***, ese será el número máximo de fragmentaciones, es decir, se retornará un máximo de ***maxsplit***+1 *BLOs*.

Si hay ocurrencias consecutivas de ***sep*** dentro del *BLO* original, se interpretan como separando *BLOs* vacíos.

Si no se especifica el separador, cualquier sucesión de espacio blanco es considerado un separador.

Si el *BLO* original está vacío y especificamos separador, el resultado será una lista con un *BLO* vacío. En cambio, si no especificamos separador, será una lista vacía. Si el *BLO* solo contiene espacios y no indicamos separador, también obtendremos una lista vacía.

```python
bytes.strip([chars])
bytearray.strip([chars])
```

Retorna un *BLO* igual al *BLO* original, pero eliminando del principio y final del mismo todos los caracteres incluidos en el *BLO* ***chars***, que por defecto son los caracteres *whitespace*.

##### Métodos exclusivos ASCII

Los siguientes métodos no deberían ser usados en *BLOs* con bytes con valor superior a 127.

```python
bytes.capitalize()
bytearray.capitalize()
```

Retorna una copia del *BLO*, con el primer carácter en mayúsculas, y el resto en minúsculas.

```python
bytes.expandtabs(tabsize=8)
bytearray.expandtabs(tabsize=8)
```

Retorna una copia del *BLO* con los tabuladores sustituidos por espacios. El número de espacios por el que será sustituido cada tabulador vendrá dado por ***tabsize*** y por la columna actual (se expande hasta el siguiente *tab stop*, múltiplo de ***tabsize***).

```python
bytes.isalnum()
bytearray.isalnum()
```

Retorna ***True*** si todos los caracteres del *BLO* son alfanuméricos, y el *BLO* no está vacío. ***False*** en caso contrario.

```python
bytes.isalpha()
bytearray.isalpha()
```

Retorna ***True*** si todos los caracteres del *BLO* son alfabéticos, y el *BLO* no está vacío. ***False*** en caso contrario.

```python
bytes.isascii()
bytearray.isascii()
```

Retorna ***True*** si todos los caracteres del *BLO* son *ASCII* (0 a 127), o el *BLO* está vacío. ***False*** en caso contrario.

```python
bytes.isdigit()
bytearray.isdigit()
```

Retorna ***True*** si todos los caracteres del *BLO* son números (***0*** a ***9***), y el *BLO* no está vacío. ***False*** en caso contrario.

```python
bytes.islower()
bytearray.islower()
```

Retorna ***True*** si algún carácter en la secuencia está en minúscula, y no existe ningún carácter en mayúscula. Si no, ***False***.

```python
bytes.isspace()
bytearray.isspace()
```

Retorna ***True*** si todos los caracteres del *BLO* pertenecen a los distintos tipos de espacio en blanco (espacio, tabulador, salto de línea), y el *BLO* no está vacío. Si no, ***False***.

```python
bytes.istitle()
bytearray.istitle()
```

Retorna ***True*** si el *BLO* está *title-cased* (todas las palabras empiezan en mayúscula y continúan en minúscula), y el *BLO* no está vacío. Si no, ***False***.

```python
bytes.isupper()
bytearray.isupper()
```

Retorna ***True*** si algún carácter en la secuencia está en mayúscula, y no existe ningún carácter en minúscula. Si no, ***False***.

```python
bytes.lower()
bytearray.lower()
```

Retorna una copia del *BLO* con todos los caracteres convertidos a minúscula.

```python
bytes.splitlines(keepends=False)
bytearray.splitlines(keepends=False)
```

Retorna una lista con las líneas del *string*. Los caracteres *newline* no se incluyen, a no ser que ***keepends*** sea ***True***.

```python
bytes.swapcase()
bytearray.swapcase()
```

Retorna un *BLO* en el que las minúsculas pasan a ser mayúsculas y viceversa. Si ***b*** es un objeto *bytes*, `b.swapcase().swapcase()` es siempre igual al objeto original (no sucede así con los *strings* y sus complicados *casings Unicode*).

```python
bytes.title()
bytearray.title()
```

Retorna una versión *title cased* del *BLO* (cada palabra empieza en mayúscula y el resto son minúsculas).

```python
bytes.upper()
bytearray.upper()
```

Como `bytes.lower()` o `bytearray.lower()` pero para mayúsculas.

```python
bytes.zfill(width)
bytearray.zfill(width)
```

Retorna un *BLO* relleno de ceros (***b'0'***) por la izquierda hasta completar la anchura ***width***. Si el *BLO* original representa un número válido y tiene signo al principio, el signo se mantendrá también al principio. Si ***width*** es igual o inferior al tamaño del *BLO* original, se retorna una copia de este.

#### 4.8.4 printf-style Bytes Formatting

(Formateo de *bytes* con estilo `printf`.)

Es igual que en el caso de *strings*. Solo hay algunas diferencias:

Existe el tipo de conversión ***%b***, para objetos de tipo *bytes*.

El tipo de conversión ***%a*** realiza la conversión mediante:

```python
repr(obj).encode('ascii', 'backslashreplace')
```

Los tipos de conversión ***%r*** y ***%s*** no existen.

Si se define precisión, la salida de ***%b*** y ***%a*** se trunca a los caracteres indicados.

#### 4.8.5 Memory Views

(Vistas de memoria.)

Los objetos de tipo *memoriview* permiten acceder a los datos de objetos que admitan el protocolo *buffer*. Los objetos *bytes* y *bytearray* soportan ese protocolo.

```python
class memoryview(obj)
```

Crea el objeto *memoryview* a partir del argumento. Resulta útil para acceder a estructuras definidas en otros módulos (como ***array*** o ***struct***).

### 4.9 Set Types - set, frozenset

(Tipos conjunto - `set`, `frozenset`.)

Un *set* es un tipo de objeto mutable, mientras que un *frozenset* es inmutable.

Tanto uno como otro son colecciones, sin orden, de objetos *hashables* (inmutables). No admiten indexación ni *slicing*. El acceso se realiza a través del *hash*, no de su posición en la lista, por lo que no puede haber dos elementos con el mismo valor.

Se pueden construir mediante una lista de elementos entre llaves, a través de una *set comprehension*, o usando el constructor:

```python
class set([iterable])
class frozenset([iterable])
```

Construyen y retornan respectivamente un nuevo *set* o *frozenset* con los elementos del iterable del argumento. Si no se especifica este, construye un conjunto vacío.

Para los ejemplos consideraremos que ***x*** es un objeto inmutable, ***s*** y ***t*** son indistintamente *sets* o *frozensets*, ***it*** es un iterable que contiene solamente elementos *hashables* (recursivamente) e ***\*it*** es una serie de iterables que contienen solamente elementos *hashables* (recursivamente). Si existen varios elementos de la misma categoría se les añadirá un sufijo numérico (***it1***, ***it2***,...).

Podemos realizar las siguientes operaciones con los conjuntos:

```python
x in s
x not in s
```

Para comprobar pertenencia.

```python
len(s)
```

Retorna la cantidad de elementos del conjunto.

```python
s.isdisjoint(it)
```

Retorna ***True*** si ***s*** y ***it*** son disjuntos, y ***False*** en caso contrario.

```python
s.issubset(it)
s <= t
```

Retorna ***True*** si todos los elementos de ***s*** están también en ***it*** (***s*** es subconjunto de ***it***). Si no, ***False***.

```python
s < t
```

Retorna ***True*** si ***s*** es subconjunto de ***t*** y además ***s*** es distinto de ***t***. En caso contrario, retorna ***False***.

```python
s.issuperset(it)
s >= t
```

Retorna ***True*** si todos los elementos de ***it*** están también en ***s*** (***s*** es superconjunto de ***it***). Si no, ***False***.

```python
s > t
```

Retorna ***True*** si ***s*** es superconjunto de ***t*** y además ***s*** es distinto de ***t***. En caso contrario, retorna ***False***.

```python
s.union(*it)
s | t1 | t2 | ...
```

Retorna un *set* o *frozenset* (dependiendo del tipo de ***s***) con la unión de ***s*** y todos los iterables de los argumentos.

```python
s.intersection(*it)
s & t1 & t2 & ...
```

Retorna un *set* o *frozenset* (dependiendo del tipo de ***s***) con la intersección entre ***s*** y todos los iterables de los argumentos.

```python
s.difference(*it)
s - t1 - t2 - ...
```

Retorna un *set* o *frozenset* (dependiendo del tipo de ***s***) con la diferencia entre ***s*** y todos los iterables de los argumentos, es decir, con los elementos de ***s*** que no pertenezcan a ninguno de dichos iterables.

```python
s.symmetric_difference(it)
s ^ t
```

Retorna un *set* o *frozenset* (dependiendo del tipo de ***s***) con los elementos que estén en ***s*** o en ***it***, pero no en ambos a la vez.

> Observemos que los métodos suelen aceptar iterables con elementos *hashables*, pero los operadores binarios solo aceptan iterables que sean *sets* o *frozensets*.

```python
s.copy()
```

Retorna una *shallow copy* del *set* o *frozenset*.

```python
s == t
s != t
```

Dos *sets*/*supersets* se comparan igual si ambos son un subconjunto del otro. Un *set* puede ser igual a un *frozenset*: se miran los elementos, no el tipo de contenedor.

Si dos conjuntos disjuntos no vacíos se comparan, se evaluarán distintos, y ninguno de ellos será mayor o menor que el otro.

> Un operador binario que realice una operación con un *set* y un *frozenset* retornará un objeto del tipo del primer operando.

#### Métodos para *sets*

Los siguientes métodos solo son aceptados por *set* (no *frozenset*). En este caso, ***s*** es estrictamente un *set*:

```python
s.update(*it)
s |= it1 | it2 | ...
```

Actualiza el contenido del *set*, añadiendo los elementos de los iterables pasados como argumento. Como siempre, los iterables solo pueden contener elementos inmutables.

```python
s.intersection_update(*it)
s &= it1 & it2 & ...
```

Actualiza el *set* eliminando de él los elementos que no pertenezcan a todos los iterables.

```python
s.difference_update(*it)
s -= it1 - it2 - ...
```

Actualiza el *set* eliminando de él los elementos que pertenezcan a alguno de los iterables.

```python
s.symmetric_difference_update(it)
s ^= it
```

Actualiza el *set* de tal modo que el nuevo contenido serán los elementos que pertenezcan a ***s*** o al iterable, pero no a los dos.

```python
s.add(x)
```

Añade el elemento ***x*** al *set* (recordemos, ***x*** es *hashable*).

```python
s.remove(x)
```

Elimina ***x*** del *set*. Levanta la excepción ***KeyError*** si no lo encuentra.

```python
s.discard(x)
```

Como `remove()`, pero si no encuentra ***x***, simplemente no hace nada.

```python
s.pop()
```

Elimina y retorna un elemento arbitrario del *set*. Si este está vacío, levanta ***KeyError***.

```python
s.clear()
```

Elimina todos lo elementos del *set*.

### 4.10 Mapping Types - dict

(Tipos *mapping* - `dict`)

Un objeto *mapping* mapea objetos *hashables* (claves) a cualquier tipo de objetos. Los *mappings* (de momento solo diccionarios) son mutables.

En cuanto a los números, dos objetos numéricos que se evalúen igual (como 1 y 1.0) pueden usarse para indexar la misma entrada de diccionario. Sin embargo, el formato de punto flotante tiene sus limitaciones a la hora de almacenar con precisión exacta muchos de los números reales, con lo que no son un buen candidato para utilizar como tipo de la clave.

Un diccionario se puede construir mediante una lista de pares ***clave:valor*** separados por comas, encerrada entre llaves. Un par de llaves sin nada dentro genera un diccionario vacío.

También se puede construir con una *dict comprehension*, o usando el constructor:

```python
class dict(**kwargs)
class dict(map[, **kwargs])
class dict(it[, **kwargs])
```

Sin argumentos, crea un diccionario vacío. Puede tener un argumento posicional al principio de todo. El resto serán *keyword arguments*.

Si el argumento posicional es un iterable, este debe contener elementos que a su vez contengan 2 elementos cada uno: el primero para la clave (debe ser inmutable) y el segundo para el valor. El argumento posicional también puede ser un *mapping*, y entonces se tomarán todos los elementos de este como base para el nuevo diccionario.

Los elementos creados a partir de *keyword arguments* se crean con una clave *string* con el nombre del argumento, y un valor correspondiente al valor de dicho argumento.

Si una clave se proporciona más de una vez, la última ocurrencia tiene preferencia.

Si ***d*** es un diccionario, estas son operaciones válidas:

```python
list(d)
```

Retorna una lista con las claves del diccionario.

```python
len(d)
```

Retorna el número de elementos del diccionario.

```python
d[key]
```

Los elementos del diccionario son accesibles a través de los valores de la clave. Así, `d[key]` retornará el valor asociado a la clave ***key***. Si esta no existe, se levantará la excepción ***KeyError***.

Si hacemos este acceso a través de una subclase de diccionario que implemente `__missing__()`, este será llamado con ***key*** como argumento, y será este método el que decidirá qué hacer con ese acceso: puede retornar un valor o levantar una excepción. Logicamente, habrá que definir este método con dos argumentos (***self*** y ***key***).

```python
d[key] = value
```

Asigna el valor ***value*** a la clave ***key***. Si no existe tal clave, crea el elemento.

```python
del d[key]
```

Elimina el elemento con clave ***key*** del diccionario. Si no existe tal clave levanta ***KeyError***.

```python
key in d
key not in d
```

Comprobación de la existencia o no existencia de la clave indicada.

```python
iter(d)
```

Crea y retorna un iterador sobre las claves del diccionario (en el orden de inserción). Equivale a `iter(d.keys())`.

```python
reversed(d)
```

Crea y retorna un iterador sobre las claves del diccionario en el orden inverso de inserción. Equivale a `reversed(d.keys())`.

A continuación veremos los métodos del tipo diccionario:

```python
dict.clear()
```

Elimina todos los elementos del diccionario.

```python
dict.copy()
```

Retorna una *shallow copy* del diccionario.

```python
classmethod dict.fromkeys(iterable[, value])
```

Crea y retorna un diccionario cuyas claves son las del iterable, y todos sus valores son ***value***, que si no se indica es ***None***.

```python
dict.get(key[, default])
```

Retorna el valor asociado a la clave ***key***. Si no existe tal clave, retorna el valor ***default***, que si no se especifica es ***None***. No levanta excepción en caso de no encontrar la clave.

```python
dict.items()
```

Retorna una nueva vista del diccionario, compuesta por elementos (clave:valor). Véase el apartado de objetos vista de diccionario.

```python
dict.keys()
```

Retorna una nueva vista del diccionario, compuesta por las claves del mismo. Véase el apartado de objetos vista de diccionario.

```python
dict.pop(key[, default])
```

Retorna el valor asociado a la clave ***key*** y elimina el elemento del diccionario. Si la clave no existe, simplemente retorna ***default***. Si no especificamos ***default*** y además no existe la clave, se levanta la excepción ***KeyError***.

```python
dict.popitem()
```

Retorna el último elemento añadido al diccionario como una tupla (clave, valor), y elimina el elemento del diccionario. Si el diccionario está vacío, se levanta ***KeyError***.

```python
dict.setdefault(key[, default])
```

Retorna el valor asociado a la clave ***key***. Si no existe dicha clave, crea un nuevo elemento con la misma y el valor definido en ***default***, y retorna ***default*** (que por defecto es ***None***).

```python
dict.update([other])
```

Actualiza los elementos del diccionario con el contenido de ***other***. Los argumentos son los mismos que en el caso del constructor. Para claves existentes, las actualiza; en caso de claves inexistentes, crea nuevos elementos.

```python
dict.values()
```

Retorna una nueva vista del diccionario, compuesta por los valores del mismo. La comparación entre dos vistas `values()` retornará siempre ***False*** (incluso comparándose consigo mismo). Véase el apartado de objetos vista de diccionario.

```python
d | other
```

Crea (retorna) un nuevo diccionario con los elementos combinados de ambos diccionarios (***d*** y ***other***). En caso de repetición de claves, ***other*** tiene preferencia.

```python
d |= other
```

Actualiza los elementos del diccionario ***d*** con los de ***other***, que puede ser un *mapping*, o un iterable con pares clave/valor. En caso de repetición de claves, ***other*** tiene preferencia.

Dos diccionarios se comparan iguales (***==***) si y solo si tienen los mismos pares clave:valor; de lo contrario se comparan diferentes (***!=***). Los demás tipos de comparación (***>***, ***\<***, ***>=***, ***\<=***) levantan la excepción ***TypeError***.

Los diccionarios preservan el orden de inserción. Actualizar un elemento no altera su orden. Tras el borrado de un elemento, el próximo elemento se añade al final.

#### 4.10.1 Dictionary view objects

(Objetos vista de diccionario.)

Los objetos retornados por los métodos `keys()`, `items()` y `values()` retornan un objeto vista de diccionario (*dictionary view object*). Este tipo de objeto proporciona una vista dinámica del diccionario, es decir, cuando el diccionario cambia, estos objetos reflejan los cambios producidos.

Los métodos mencionados aseguran el mismo orden, que coincide con el orden de inserción en el diccionario.

Si ***v*** es un objeto vista de un diccionario, estas operaciones son posibles:

```python
len(v)
```

Retorna el número de objetos de ***v***.

```python
iter(v)
```

Retorna un iterador sobre los elementos de la vista, en orden de inserción. No se debe modificar el contenido del diccionario principal mientras se itera en estos objetos, para no obtener resultados inesperados o levantamiento de excepciones (***RuntimeError***).

```python
reversed(v)
```

Igual que `iter()` pero en sentido inverso.

```python
x in v
x not in v
```

Comprobación de pertenencia o no. En el caso de una vista hecha con `items()`, ***x*** debería ser una tupla de 2 elementos (clave y valor).

### 4.11 Context Manager Types

(Tipos gestor de contexto.)

La sentencia `with` permite establecer un contexto de ejecución definido por un objeto del tipo gestor de contexto (*context manager*). Este tipo de objeto posee dos métodos: uno se ejecuta antes de entrar a la sentencia `with` (prepara el contexto), y el otro se ejecuta una vez ha terminado todo el código de dicho `with` (cierra el contexto).

```python
contextmanager.__enter__()
```

Es el método de entrada, que se ejecuta antes del `with`. El objeto retornado por este método queda ligado al identificador detrás de la cláusula `as` de la sentencia `with`.

```python
contextmanager.__exit__(exc_type, exc_val, exc_tb)
```

Define el código que se ejecutará al finalizar la sentencia `with`. Si se ha levantado una excepción durante la ejecución del cuerpo del `with`, ***exc_type*** contiene el tipo de excepción, ***exc_val*** el valor de la misma, y ***exc_tb*** su *traceback*. En caso contrario, los tres reciben ***None***.

Si este método retorna ***True***, la sentencia `with` suprimirá tal excepción y la ejecución seguirá normalmente. Si retorna ***False***, la excepción, en caso de haberla, se propagará fuera del `with`.

Si durante la ejecución de este método se levanta una excepción, anulará la anterior y se propagará esta.

### 4.12 Generic Alias Type

(Tipo alias genérico.)

Es posible crear alias de tipos genéricos. La forma general es:

```python
T[X, Y, ...]
```

En este caso representa un contenedor de tipo ***T*** que tiene elementos de tipo ***X***, ***Y***, etc. Se suele usar para anotaciones de tipos.

```python
t = list[str]
```

Aquí ***t*** define un tipo genérico (de tipo ***types.GenericAlias***) de una lista con elementos *string*. Ahora se puede construir un elemento usando el constructor de ese tipo genérico:

```python
lista = t('uno', 'dos', 'tres')
```

Al construir el objeto, no se comprueba el tipo, con lo que podemos hacer:

```python
lista = t(1, 2, 3)
```

En este caso, el código funcionará sin errores (aunque no sería lo más correcto). Es por ello que en la práctica no es muy útil. Su utilidad es más evidente a la hora de hacer anotaciones:

```python
def average(values: list[float]) -> float:
    return sum(values) / len(values)
```

Esto define una función que toma por parámetro una lista de *floats* y retorna un `float`.

Un *generic alias type* tiene algunos atributos de solo lectura, como ***\_\_origin\_\_*** (tipo de la clase genérica sin parametrizar), o ***\_\_args\_\_*** (tupla con los tipos pasados al contenedor original). Por ejemplo, si el tipo genérico es `list[int, float]`, ***\_\_origin\_\_*** es el tipo `list` y ***\_\_args\_\_*** una tupla con dos elementos: el tipo `int` y el tipo `float`.

### 4.13 Other Built-in Types

(Otros tipos incorporados.)

#### 4.13.1 Modules

(Módulos.)

Es posible acceder directamente a los nombres de un módulo. Si ***m*** es un módulo, podemos leer el valor de ***m.nombre***, asignarle valor, o añadir atributos del mismo modo, así como eliminarlos mediante `del(m.nombre)`.

Lo que no se puede hacer es asignarle directamente el diccionario. No podemos hacer `m.__dict__={}`.

#### 4.13.3 Functions

(Funciones.)

La única operación que se puede hacer sobre un objeto función es la llamada.

#### 4.13.4 Methods

(Métodos.)

Los métodos ligados (*bound*) a una instancia tienen dos atributos específicos de solo lectura: Si ***m*** es un método, ***m.\_\_self\_\_*** es el objeto al que está ligado, y ***m.\_\_func\_\_*** es la función que ejecuta el método.

A esa función, como es habitual, se le pueden añadir o quitar atributos. Sin embargo no puede hacerse lo mismo en los métodos *bound*, sino únicamente al objeto función ligado a este. Si por ejemplo la clase ***C*** define el método ***met***, y ***c*** es una instancia de esa clase:

```python
C.met.atrib = 10    # correcto: C.met es un objeto función
c.met.atrib = 10    # error: c.met es un objeto método
c.met.__func__.atrib = 10    # correcto: met.__func__
                             # es un objeto función
```

#### 4.13.6 Type Objects

(Objetos tipo.)

Son los objetos que representan un tipo. El tipo de un objeto es retornado por `type(ob)`, que de hecho retorna ese **objeto tipo**.

### 4.14 Special Attributes

(Atributos especiales.)

Algunos tipos de objeto tienen una serie de atributos de solo lectura adicionales. Algunos de ellos no son reportados por `dir()`:

```python
objeto.__dict__
```

Diccionario que almacena los atributos de un objeto.

```python
instancia.__class__
```

Clase (tipo) al que pertenece la instancia.

```python
clase.__bases__
```

Tupla de clases base de un objeto clase.

```python
definicion.__name__
```

Nombre de una clase, función, método, descriptor o generador.

```python
definicion.__qualname__
```

Nombre cualificado (con la ruta de *namespaces* completa, en notación de puntos) de una clase, función, método, descriptor o generador.

```python
clase.__mro__
```

Tupla de clases, según el orden de búsqueda para la resolución de métodos (*MRO*, *method resolution order*).

```python
clase.__subclasses__()
```

Cada clase mantiene una lista de referencias débiles (*weak references*) a sus subclases inmediatas. Este método retorna una lista a las referencias que sigan vivas, en orden de definición.

### 4.15 Integer string conversion length limitation

(Limitación de longitud de conversión *string* a entero.)

Para evitar ataques del tipo *DoS* (*denial of service*) se impone un límite en la longitud de los *strings* que se deseen convertir a entero. Sin contar el signo y los caracteres separadores, el límite por defecto es 4300 dígitos.
