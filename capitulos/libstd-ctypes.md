# 16.16 ctypes - A foreign function library for Python

Este es un resumen del apartado 16.16 de la documentación oficial sobre la biblioteca estándar de *Python* 3.10.

Esta funcionalidad específica de la biblioteca proporciona tipos de datos compatibles con *C* y permite llamar a funciones en bibliotecas compartidas (***.so*** en sistemas *Unix* y ***.dll*** en *Windows*).

## 16.16.1 ctypes tutorial

> En sistemas donde el tamaño de un `long` coincide con el de un `int`, el tipo `c_int` (definido en este módulo) es un alias del tipo `c_long` (también parte de este módulo).

Para cargar bibliotecas dinámicas, se utiliza el constructor de la clase `CDLL`.

Una instancia de esta clase representa una biblioteca dinámica que ha sido cargada. Las funciones de esta biblioteca usan la *standard C calling convention* (llamada ***cdecl***). Es por ello que puede no funcionar en *Windows* (las *DLL* de *Windows* usan otro convenio de llamada). El tipo de retorno de estas funciones es `int`.

El único argumento al constructor que nos interesa (y el único obligatorio) es la ruta del archivo a cargar (la biblioteca compartida).

En sistemas *Windows*, no hay que incluir la extensión ***.dll***, ya que se añade automáticamente. Sin embargo, en sistemas *Linux* se debe indicar el nombre completo.

En sistemas *Windows* exclusivamente, existe la clase `WinDLL`, que es como `CDLL` pero usa la *calling convention* de las *DLL* de *Windows* (llamada ***stdcall***). El valor esperado de retorno sigue siendo `int`.

También exclusiva de *Windows* es la clase `OleDLL`, que es como `WinDLL`, pero el tipo retornado esperado es un código de tipo `HRESULT` (específico de *Windows*), en cuyo caso, si dicho código señala error, se levantará una excepción ***OSError***.

Por último, la clase `PyDLL` es como `CDLL`, con la diferencia que durante la ejecución de la función, el *Python GIL* no es liberado, con lo que otros hilos no pueden usar el intérprete. Al retornar la función se comprueba el estado de error de *Python*, y si está activado, se levanta la correspondiente excepción. Por lo tanto solo es útil para funciones del *API C* (estas pueden establecer el estado de error de *Python* mediante las funciones del *API*; véase la documentación de creación de extensiones *C*).

En cuanto al nombre del archivo, si no se especifica ruta (solo nombre), se busca en los directorios de bibliotecas del sistema. Si especificamos ruta (relativa al directorio actual, o absoluta) intentará cargar ese archivo específico.

Una vez se ha cargado la biblioteca en la instancia correspondiente (del objeto de tipo `CDLL`, o `WinDLL`, etc.), se puede acceder a las funciones a través del nombre de las mismas usando la sintaxis de acceso a atributos. Supongamos que la librería compilada define las funciones ***foo1()*** y ***foo2()***:

```python
from ctypes import *
libreria = CDLL("./libdemo.so")
fun = libreria.foo1  # acceso
resul = libreria.foo2()  # llamada
```

Al pasar argumentos a las funciones de una librería, solo pueden pasarse tal cual los tipos *Python* nativos ***None***, entero, *bytes* y *strings* (*Unicode*). ***None*** se pasa como ***NULL***, un entero como el tipo `int`, un objeto *bytes* como un apuntador `char*` a un bloque de memoria que contiene sus datos, y un *string* también, pero mediante un `wchar_t*`.

### Tipos fundamentales

*ctypes* define una serie de tipos compatibles con los tipos primitivos de *C*. Se crean pasando un valor adecuado al constructor:

| Tipo ctypes    | Tipo C                 | Tipo Python         |
| :------------- | :--------------------- | :------------------ |
| c_bool         | _Bool                  | bool                |
| c_char         | char                   | bytes (longitud 1)  |
| c_wchar        | wchar_t                | string (longitud 1) |
| c_byte         | char                   | int                 |
| c_ubyte        | unsigned char          | int                 |
| c_short        | short                  | int                 |
| c_ushort       | unsigned short         | int                 |
| c_int          | int                    | int                 |
| c_uint         | unsigned int           | int                 |
| c_long         | long                   | int                 |
| c_ulong        | unsigned long int      | int                 |
| c_longlong     | long long int          | int                 |
| c_ulonglong    | unsigned long long int | int                 |
| c_size_t       | size_t                 | int                 |
| c_ssize_t      | Py_ssize_t             | int                 |
| c_float        | float                  | float               |
| c_double       | double                 | float               |
| c_longdouble   | long double            | float               |
| c_char_p       | char *                 | objeto bytes o None |
| c_wchar_p      | wchar_t *              | string o None       |
| c_void_p       | void *                 | int o None          |

El constructor de `c_bool` acepta cualquier objeto convertible a booleano. El tipo `Py_ssize_t` está definido en el *API C* de *Python*. Los apuntadores a carácter (*wide* o `char`) recibirán un *string* o `bytes` y apuntarán a un bloque de memoria *null-terminated*.

Si no damos valor al constructor del tipo, se inicializa con ceros.

Existen también los tipos `c_int8`, `c_int16`, `c_int32` y `c_int64`, así como sus correspondientes sin signo `c_uint8`, `c_uint16`, `c_uint32` y `c_uint64` para asegurarnos la cantidad de bits necesaria.

Para funciones *C* que utilizan el *API C*, existe el tipo `py_object`, que se convierte en `PyObject*`.

En *Windows* existe también el tipo `HRESULT`.

Ejemplos de uso de estos tipos:

```python
>>> c_int()
c_long(0)
>>> c_wchar_p("Hello, World")
c_wchar_p(140018365411392)
>>> c_ushort(-3)
c_ushort(65533)
>>>
```

Estos tipos son mutables, con lo que puede cambiarse su valor (atributo ***value***):

```python
>>> i = c_int(42)
>>> print(i)
c_long(42)
>>> print(i.value)
42
>>> i.value = -99
>>> print(i.value)
-99
```

Si cambiamos el valor de un apuntador, no se modifica el contenido apuntado, solo la dirección de memoria a la que apunta. El contenido apuntado es inmutable y no debería modificarse desde la función *C*.

Sin embargo, si la función *C* espera un apuntador a una zona de memoria que pueda modificar tranquilamente, *ctypes* dispone de las funciones `create_string_buffer()` y `create_unicode_buffer()`, que retornan un objeto que puede pasarse como `char*` o `wchar_t*` respectivamente (de hecho lo que crean es un *array*). Al constructor se le puede pasar un entero (número de elementos, puestos a 0), un objeto *bytes* o *string* (*buffer* incluirá el terminador nulo), o un *bytes* o *string* seguido de un entero (truncará o rellenará con ceros).

### Especificar tipo de argumentos y de retorno

Es posible especificar los tipos de los argumentos de una función, mediante una secuencia que los defina. Por un lado, es un modo de asegurarnos de que haremos las llamadas correctamente. Por otro lado, *Python* convertirá adecuadamente los argumentos que pasemos a la función (si es que pueden convertirse).

```python
libreria.foo1.argtypes = [c_char_p, c_char_p, c_int, c_double]
```

En cuanto al valor retornado, por defecto esperamos un `int` de vuelta (a excepción de librerías cargadas a través de `OleDLL`). Si queremos cambiar el tipo esperado de retorno, se debe indicar en el atributo ***restype*** del objeto función.

```python
libreria.foo1.restype = c_char_p  # esperaremos char* de vuelta
```

Para validar el resultado de la llamada, se usa el atributo ***errcheck*** de la función, a la que se le asigna un objeto función o cualquier objeto *callable*. Al regresar la función *C*, esta función de comprobación será llamada con 3 argumentos:

- ***result*** es el resultado de la función *C*, según el tipo especificado por el atributo ***restype***.
- ***func*** es el objeto función *C* en sí.
- ***arguments*** una tupla con los argumentos pasados inicialmente a la función *C*.

En principio, esta función retornará lo recibido en ***result***; aunque también puede interceptar ese valor y cambiarlo, con lo que la llamada a la función *C* se evaluará a ese valor. Adicionalmente, puede comprobar ese valor retornado por la función externa, y, en su caso, levantar una excepción.

### Paso por referencia

Si queremos pasar una variable por referencia a la función *C*, podemos usar la función de *ctypes* `byref()`. Así, en lugar de pasar ***var***, pasaremos `byref(var)`. Lo mismo puede hacerse mediante `pointer()`, aunque esta es un poco más lenta, ya que construye un nuevo objeto apuntador *ctypes* y realiza la llamada con este nuevo objeto.

### Estructura y uniones

*ctypes* proporciona las clases `Structure` y `Union`, que sirven como clases base para construir nuestras propias estructuras y uniones. El funcionamiento de ambos es el mismo (solo cambia el modo de almacenamiento interno), por lo que todo lo dicho para las estructuras se aplica también a las uniones (exceptuando el tema de la alineación, claro).

En la definición de la subclase se debe indicar un miembro ***\_fields\_***, que será una lista de tuplas de 2 elementos: nombre (*string*) y tipo *ctypes*.

A la hora de inicializar se puede hacer en orden de definición, o por nombre del campo. Si se indican menos valores, se inicializan con valor cero. No se pueden indicar más valores que campos.

```python
class POINT(Structure):
    _fields_ = [("x", c_int),
                ("y", c_int)]

point1 = POINT(10, 20)  # x=10, y=20
point2 = POINT(y=5)  # x=0, y=5
```

Las estructuras pueden anidarse:

```python
class RECT(Structure):
    _fields_ = [("upperleft", POINT),
                ("lowerright", POINT)]

rc1 = RECT(point1, point2)
rc2 = RECT(POINT(1, 2), POINT(3, 4))
rc3 = RECT((1, 2), (3, 4))
```

Para acceder a los campos se realizará mediante sintaxis de atributo: ***rc1.upperleft.x***, ***rc2.lowerright***.

La alineación de los campos se hará según el compilador. Se puede empaquetar la estructura definiendo un atributo ***\_pack\_*** en la subclase, estableciéndolo a un entero positivo que definirá la alineación máxima de los campos.

Si queremos definir un *bit field*, lo indicaremos mediante un entero como tercer elemento de la tupla correspondiente al campo. Un objeto de tipo estructura que tiene *bit fields* no se debe pasar por valor a una función *C* (usaremos apuntador).

En cuanto al orden de *bytes*, se utilizará el nativo. Si queremos controlarlo, usaremos las clases base `BigEndianStructure`, `LittleEndianStructure`, `BigEndianUnion`, y `LittleEndianUnion` (estas clases no pueden contener apuntadores).

Para especificar, por ejemplo, un tipo apuntador a estructura ***RECT***: `POINTER(RECT)`

### *Arrays*

Un *array* es una secuencia conteniendo instancias del mismo tipo. Las estructuras pueden contener campos *array*. A su vez, los elementos de los *arrays* pueden ser de tipo estructura.

Para definir un tipo *array*, lo recomendable es multiplicar un tipo base:

```python
TenPointsArrayType = POINT * 10
arr = TenPointsArrayType()
for pt in arr:
    # ...
```

Por defecto se inicializan los valores a 0, pero podemos inicializar:

```python
TenInts = c_int * 10
arr = TenInts(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

Pasar un *array* a una función es pasar un apuntador al primer elemento.

Es posible iterar sobre los elementos del *array*.

### Apuntadores

Para crear una instancia de tipo apuntador se usa la función `pointer()`, a la que pasamos el objeto al que deseamos apuntar. El atributo ***contents*** de la instancia apuntador retorna el objeto apuntado. Sin embargo, no es estrictamente el mismo objeto, ya que se construye una copia nueva a cada acceso al atributo.

```python
>>> i = c_int(42)
>>> pi = pointer(i)
>>> pi.contents
c_int(42)
>>> pi.contents is i
False
>>> pi.contents is pi.contents
False
```

Es posible acceder a la zona de memoria donde apunta el apuntador utilizando indexación. Hay que tener la seguridad de que los accesos a memoria son correctos (zonas debidamente *allocated*).

```python
>>> pi.contents
c_int(42)
>>> pi[0]=99
>>> pi.contents
c_int(99)
```

Es posible crear un **tipo de apuntador** pasando un tipo base (que puede ser apuntador a su vez) a la función `POINTER()`. Esto retorna el tipo derivado.

```python
tipo_ap = POINTER(c_int)  # tipo apuntador a entero
i = c_int(50)
ap = tipo_ap(i)  # apuntador a entero
```

Esto puede hacerse así:

```python
ap = POINTER(c_int)(c_int(50))
```

Lo cual es precisamente el equivalente de:

```python
ap = pointer(c_int(50))
```

Crear un tipo apuntador sin pasarle el objeto al que apuntar produce un apuntador ***NULL*** (p.e. `POINTER(c_int)()`).

El tipo `POINTER()` mantiene una referencia al contenido apuntado, para asegurarse que la zona de memoria a la que apunta no queda liberada por el *garbage collector* mientras exista una referencia (instancia) del apuntador.

Supongamos que pasamos el apuntador ***ap*** del ejemplo anterior a una función *C*, que almacena ese apuntador en una variable `static`. En las sucesivas llamadas a dicha función, el contenido del apuntador estará a salvo. Sin embargo, si ***ap*** deja de referenciar a ese objeto `POINTER()`, el apuntador será *garbage collected*, lo cual decrementará el *reference count* de la zona apuntada. Si además las referencias directas a la zona de memoria dejan de existir, ese contenido apuntado será liberado en cualquier momento.

Si obtenemos una instancia de un apuntador a un *array*, podemos iterar sobre él normalmente. Los elementos serán del tipo del *array*.

Veamos un ejemplo de cómo se hacen las referencias a los distintos tipos:

```c
class ESTRUC(Structure):
    _fields_ = [("edad", c_int),
                ("altura", c_double),
                ("nombre", POINTER(c_char))]
ARRESTRUC = ESTRUC * 20

foo.argtypes = [c_char, ESTRUC, POINTER(ARRESTRUC)]
```

### Conversiones de tipos

En los tipos de argumento en el atributo ***argtypes*** de la función a llamar, o en las especificaciones de tipo de los campos de estructuras, *ctypes* solo aceptará argumentos del tipo exacto. Hay algunas excepciones:

- Si está definido un tipo apuntador, se acepta un *array*. Si hemos especificado un tipo `POINTER(c_int)` se admitirá un *array* de `c_int`.
- Se puede dar el valor ***None*** a un apuntador (se convertirá en ***NULL***).

```python
>>> class Bar(Structure):
...     _fields_ = [("count", c_int), ("values", POINTER(c_int))]
...
>>> bar = Bar()
>>> bar.values = (c_int * 3)(1, 2, 3)  # llamada al constructor de un array de 3 enteros
>>> bar.count = 3
>>> for i in range(bar.count):
... print(bar.values[i])
...
1
2
3
```

Si queremos convertir tipos incompatibles explícitamente podemos usar la función `cast()` cuyo primer parámetro es el objeto a convertir y el segundo el tipo al que convertir.

```python
bar.values = cast((c_byte * 4)(), POINTER(c_int))
```

El ejemplo muestra un *array* de 4 `char`, inicializado a ceros, y convertido a `int*`.

En *C* podemos definir tipos incompletos en los campos de una estructura. El caso característico de un apuntador al tipo propio:

```c
struct cell
{
    double size;
    struct cell *next;
}
```

Esto no funciona en *ctypes*:

```python
class cell(Structure):
    _fields_ = [("size", c_double),
                ("next", POINTER(cell))]  # ERROR! Tipo desconocido
```

Para solucionarlo, simplemente definiremos los campos con posterioridad a la estructura:

```python
class cell(Structure):
    pass

cell._fields_ = [("size", c_double), ("next", POINTER(cell))]
```

### Funciones *callback*

Es posible crear apuntadores a funciones mediante la función `CFUNCTYPE()` (convención de llamada ***cdecl***) o `WINFUNCTYPE()` (***stdcall***). El primer argumento a pasar a estas funciones es el tipo de retorno de la función *callback*, y el resto de argumentos serán los tipos de los argumentos de entrada de dicha función. Obtendremos un tipo *callback function*. Una vez tengamos el tipo, lo instanciaremos con la función de la que deseamos el apuntador. Si se crea el apuntador a función con `PYFUNCTYPE()`, será como `CFUNCTYPE()`, pero la función no libera el *GIL* durante la llamada.

```python
def comparaNums(a, b):
    return b[0] - a[0]

CMPFUNC = CFUNCTYPE(c_int, POINTER(c_int), POINTER(c_int))
cmp_func = CMPFUNC(comparaNums)
```

En el ejemplo, vemos la definición de una función ***comparaNums*** que toma dos argumentos de tipo `POINTER(c_int)` y retorna un `c_int`. Luego definimos el tipo `CMPFUNC`, que es el tipo que describe ese tipo exacto de función. Finalmente creamos la instancia de *callaback function* (***cmp_func***) que ya podemos utilizar como tipo en funciones *C* que reciban (o retornen) un apuntador a función de este tipo.

Si nos fijamos, hemos hecho:

```python
cmp_func = CFUNCTYPE(c_int, POINTER(c_int), POINTER(c_int))(comparaNums)
```

Podríamos hacer:

```python
comparaNums = CFUNCTYPE(c_int, POINTER(c_int), POINTER(c_int))(comparaNums)
```

Por lo tanto podríamos usar un *decorator* al definir la función:

```python
@CFUNCTYPE(c_int, POINTER(c_int), POINTER(c_int))
def comparaNums(a, b):
    return b[0] - a[0]
```

Así, ***comparaNums*** es una *callback function* directamente, es decir, ya se podría pasar directamente como argumento a una funcion externa que esperase un apuntador a función de las características especificadas.

La función del ejemplo sigue siendo *callable* desde *Python*. En ese caso, deberíamos pasarle como argumentos, no dos números *Python*, sino dos objetos de tipo `POINTER(c_int)`.

Se debe tener en cuenta lo dicho para el tipo `POINTER()` en cuanto al *garbage collector*. Cuando dejen de haber referencias a la función apuntada y al objeto `CFUNCTYPE()`, la función puede ser liberada de memoria en cualquier momento. Hay que tener en cuenta que si se usa el *decorator*, no existe más referencia a la función que la que proporciona el objeto *callback function*.

### Valores exportados

A parte de funciones, las bibliotecas dinámicas pueden exportar variables. En ese caso deberán ser variables globales con *linkage* externo, y se leerán con el método `in_dll()` del tipo al que se convertirán.

```python
from ctypes import *
lib = CDLL("./mylib.so")
valor = c_int.in_dll(lib, "vari")
```

En este ejemplo, leeremos el valor de la variable ***vari***, en un `c_int`. Cualquier valor *ctypes* (primitivos, apuntadores, *arrays*, estructuras, etc.) disponen del método `in_dll()`.
