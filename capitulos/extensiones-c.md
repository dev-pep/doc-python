# Crear extensiones en *C*

El presente documento es un resumen de algunos capítulos del documento *Extending and Embedding Python*, del que se han resumido solo los aspectos básicos para crear un módulo *Python* en lenguaje compilado *C*. Se ha ampliado el resumen con información del documento *The Python/C API*. Ambos documentos pertenecen a la documentación oficial de *Python*.

Lo explicado aquí solo sirve para la implementación oficial (*CPython*). Para un proyecto más portable a otras implementaciones, en lugar de crear extensiones se puede utilizar la biblioteca ***ctypes*** o equivalente.

Nos centraremos en el aspecto más habitual: la creación de extensiones (módulos) de *Python* utilizando el lenguaje *C*. Las funciones del módulo serán mucho más rápidas que si las desarrollamos en *Python*.

La otra opción es incrustar (*embed*) *Python* dentro de una aplicación. Por ejemplo, crear una aplicación con *C++* que tenga incrustado el *runtime CPython*, con lo que podría hacer uso de las funciones de *Python*. Sin embargo, ignoraremos esta parte y nos centraremos en la creación de módulos.

## 2. CREATING EXTENSIONS WITHOUT THIRD PARTY TOOLS

### 2.1 Extending Python with C or C++

En el archivo fuente en *C* de una extensión (módulo) para *Python* se debe incluir:

```c
#include <Python.h>
```

Para que tal archivo de cabecera esté disponible en nuestro sistema, es posible que haya que instalar algún paquete **de desarrollo** de *Python*. En una distribución *Linux* basada en *Debian* podríamos tener que hacer algo así:

```
sudo apt install python3.10-dev
```

En ese caso, una probable ubicación para ese archivo sería ***/usr/include/python3.10/Python.h***.

Así, podríamos incluirlo directamente como:

```c
#include <python3.10/Python.h>
```

Lo cual funcionaría, pero presentaría un probable problema de portabilidad. Lo mejor es especificar el subdirectorio preciso en el comando de compilación, y así evitar escribir tal subdirectorio (dependiente de la plataforma) en el código fuente.

#### 2.1.1 A Simple Example

Vamos a crear un módulo ***spam*** que contendrá una función `system()` que llamará a la función `system()` de ***stdlib.h***, que recibe un *string* con un comando para el intérprete de comandos del sistema, y retorna un entero.

El **uso** de nuestro módulo debería ser algo así:

```
>>> import spam
>>> spam.system("ls -l")
```

En nuestro módulo, la primera línea debería ser:

```c
#include <Python.h>
```

Se debe incluir esa cabecera **antes** de incluir los *headers* estándar, ya que en algunos sistemas, las definiciones de ***Python.h*** pueden afectar a algunas de esas cabeceras estándar.

Al incluir ***Python.h*** ya quedan incluidos ***stdio.h***, ***string.h***, ***errno.h*** y ***stdlib.h***.

Como se irá viendo, todos los nombres accesibles que proporciona ***Python.h*** empiezan por ***Py*** o ***PY***.

Ahora vamos a incluir la función que será invocada cuando desde *Python* entremos `spam.system("ls -l")`.

```c
static PyObject * spam_system(PyObject *self, PyObject *args)
{
    const char *command;
    int sts;

    if (!PyArg_ParseTuple(args, "s", &command))
        return NULL;
    sts = system(command);
    return PyLong_FromLong(sts);
}
```

En primer lugar, `PyObject*` es el tipo (*C*) base para representar un objeto *Python*. Es el tipo que retorna la función invocada (lógico, ya que es el valor que recogerá *Python*). **Todos** los objetos de *Python* son, de hecho, objetos reservados dinámicamente en memoria. De ahí a usar siempre un apuntador para utilizarlos.

Por otro lado, la función `spam_system()`, que será invocada por *Python* (ya veremos cómo), recibe siempre dos argumentos: el primero (***self*** por convenio) es un apuntador al módulo al que pertenece la función (si la función correspondiera, por ejemplo, a un método, sería un apuntador a la instancia a la que pertenece).

El segundo argumento (***args*** por convenio) corresponde a un apuntador a un objeto tupla de *Python* con los argumentos de la llamada. Dichos argumentos son objetos *Python*, con lo que deberemos convertirlos a tipos *C*.

Y eso es lo que hace exactamente la función `PyArg_ParseTuple()`:

```c
int PyArg_ParseTuple(PyObject *args, const char *format, ...);
```

Extrae los elementos de una tupla y los referencia en apuntadores *C*. Usa una *format string* ***format*** que especifica los tipos a los que se deben convertir los objetos. En el ejemplo, un solo elemento *string* (***"s"***). Los argumentos variables (a partir del tercero) que pasamos a esta función son referencias a un tipo *C*. Es decir, son apuntadores a un tipo *C*. Si se trata de una referencia (apuntador) a `int`, recibiremos el valor de ese entero al dereferenciar el apuntador pasado, es decir, la función escribirá ese valor en la zona de memoria que indica el apuntador que le hemos pasado. Si lo que pasamos es una referencia (apuntador) a un *string* (es decir, a un `char*`), el *string* pasará a apuntar a una zona de memoria que contiene el resultado.

La función retorna 1 si se han convertido con éxito todos los elementos de la tupla, y cero en caso contrario.

Como vemos, si la función ha fallado, retornamos ***NULL***, lo cual es una forma de indicar a *Python* que ha habido un error.

#### 2.1.2 Intermezzo: Errors and Exceptions

Por convenio, si una llamada desde *Python* a nuestra función falla, debe levantar un estado de excepción y retornar un valor de error (normalmente ***NULL***). Las funciones de esta *API* que construyen objetos (como p.e. `PyLong_FromLong()`) tienen exactamente este comportamiento. El estado de error se guarda en una variable global del intérprete. Si el valor de esa variable es ***NULL***, no hay estado de error. Una segunda variable global guarda el valor asociado a la posible excepción. Una tercera variable guarda la pila de *traceback* (solo si la excepción se ha producido en el código *Python*).

Para establecer un estado de error, la función más común de esta *API* es `PyErr_SetString()`, a la que se debe pasar un objeto excepción (por ejemplo ***PyExc_ZeroDivisionError***, existen objetos predefinidos para todos los tipos de excepción) y un *string C*, que se convierte a un tipo *Python* que pasa a ser el valor asociado a la excepción.

Otra función útil es `PyErr_SetFromErrno()`, que solo recibe un objeto excepción y construye el valor asociado según el valor de la variable global ***errno*** (***errno.h***).

La función más general es `PyErr_SetObject()` cuyos dos argumentos son el objeto excepción y el objeto con el valor asociado.

Para saber si existe una excepción levantada, `PyErr_Occurred()` retorna el objeto excepción actual (o ***NULL*** si no hay).

Cuando una función llama a otra, que a su vez llama a otra, y esta última encuentra un error, debería, a parte de retornar un valor de indicación de error (por ejemplo ***NULL*** si retorna apuntador, o ***-1*** si retorna enteros) levantar excepción. La función llamante debería, a su vez, retornar indicación de error, pero **NO** levantar excepción; las restantes funciones llamadoras también deberían hacer esto, hasta llegar al intérprete de *Python*. Este es el comportamiento que se debe usar generalmente. Si una función llamante debe obligatoriamente levantar una excepción durante esos retornos, se perderá la información de la primera excepción levantada.

Sin embargo, si no deseamos pasar una excepción levantada a *Python* y queremos gestionarla desde *C*, se puede limpiar el estado de excepción con `PyErr_Clear()`.

Si una función que usa `malloc()` o `realloc()` (***stdlib.h***) recibe un error de estas funciones, debería levantar excepción con `PyErr_NoMemory()` y retornar indicación de error.

> Indicar que si bien lo habitual en caso de enteros es retornar -1 en caso de error, y positivo o 0 en caso de éxito, la familia de funciones `PyArg_ParseTuple()` no sigue este esquema.

Si retornamos indicador de error desde una función, todos los objetos creados en esta dejan de ser válidos, con lo que debemos recordar decrementar el conteo de referencias adecuadamente.

Se pueden crear excepciones a medida:

```c
PyObject* PyErr_NewException(const char *name, PyObject *base, PyObject *dict);
```

Esto retorna una nueva referencia a una nueva clase de excepción y añade otra referencia al diccionario del módulo. Se la pasa un nombre, que debería tener la forma ***modulo.clase*** (en nuestro caso, ***spam.error***). El atributo ***\_\_module\_\_*** de la nueva clase se establece a lo que está a la izquierda del último punto (***spam***), y su nombre a lo que está a su derecha (***error***). Aunque podemos poner cualquier nombre que deseemos, se debería usar este convenio.

Si el segundo argumento es ***NULL***, la clase derivará de `Exception`, aunque podemos indicar aquí otra clase base. En cuanto a ***dict*** suele ser ***NULL***, o indicar un diccionario de variables y métodos para la clase.

La nueva clase retornada se podría almacenar en una variable global de almacenamiento estático (si lo hacemos debemos incrementar el conteo de referencias también). Así, aunque un código externo elimine la clase del módulo (de su diccionario), nosotros seguiremos teniendo acceso a ella a través de esta variable.

Las variables globales cuyo valor deseemos mantener entre llamadas al módulo, se declararán como `static`, al igual que las funciones que definamos (a excepción de una función, que veremos ahora). Esto es así para asegurarnos que los objetos y funciones no tienen *external linkage*, ya que si fuese así podrían entrar en conflicto con otros elementos de otros módulos. En cambio, con *internal linkage*, *Python* no tendrá acceso a estos elementos globales del módulo.

La única excepción es la función de inicialización del módulo, que sí debe ser accesible desde *Python*, como veremos más adelante.

Por otro lado, a veces puede interesarnos que algún elemento del módulo sea accesible a otros módulos. Para ello, habrá que declarar nuestro elemento (normalmente una función) sin `static`, proporcionar un *header file* con las declaraciones adecuadas, y, sobre todo, enlazar (*link*) nuestro módulo estáticamente recompilando el intérprete. Cuando los módulos están enlazados dinámicamente a través de librerías compartidas, el acceso puede ser más complejo. Pero esta es la práctica habitual. La visibilidad de nombres entre módulos enlazados dinámicamente depende del sistema operativo, por lo que si deseamos ofrecer nombres a otros módulos, deberemos asegurarnos de que son visibles. Para más información, el apartado 2.1.12 da una explicación al respecto.

#### 2.1.3 Back to the Example

Ahora ya sabemos que si `PyArg_ParseTuple()` ha fallado, ha levantado la excepción adecuada, y ha retornado ***NULL***. De lo contrario, tenemos en ***command*** un apuntador a un *string*. No deberíamos modificar su contenido (pertenece al objeto *Python* y tiene su mismo tiempo de vida); por eso está declarado como `const char *command`.

Ahora ya podemos invocar a `system()` de ***stdlib.h*** y pasarle ese *string*. Recogemos el valor devuelto por esta función en ***sts***, y retornamos ese entero, previa conversión a un entero de *Python* con `PyLong_FromLong()`, que retorna, como de costumbre, un objeto *Python* (`PyObject*`).

Si queremos retornar el objeto *Python* ***None***, lo haremos mediante ***Py_None*** (no debemos retornar ***NULL***, ya que eso sería retornar error). Debería hacerse así:

```c
Py_INCREF(Py_None);
return Py_None;
```

Incrementamos el conteo de referencias a ***None*** también (2.1.10).

#### 2.1.4 The Module’s Method Table and Initialization Function

Para que nuestra función sea llamada al realizar la invocación desde *Python*, tenemos que registrarla en la denominada "tabla de métodos" (*method table*) del módulo:

```c
static PyMethodDef SpamMethods[] = {
    {"system", spam_system, METH_VARARGS, "Execute a shell command."},
    /* ... */
    {NULL, NULL, 0, NULL}
};
```

El identificador de la tabla puede ser cualquiera, pero debe ser de tipo `PyMethodDef`.

Para cada función, proporcionamos 4 datos: nombre de la función en el módulo (*string C*), referencia a la función, *calling convention*, y una descripción de la función (*docstring* expresada como *string C*).

El último elemento de la tabla debe estar a ***NULL*** para marcar el fin de la tabla.

El primer y cuarto dato de cada registro no merece más explicación que ser simples *strings C*.

El segundo de los datos debe ser de tipo `PyCFunction`, que se define así:

```c
PyObject *PyCFunction(PyObject *self, PyObject *args);
```

En cuanto al tercer dato, la *calling convention* que usará *Python*, suele tener el valor ***METH_VARARGS***, que indica que los argumentos de *Python* se pasarán en una tupla que se podrá procesar correctamente mediante `PyArg_ParseTuple()`.

Si la función no recibe argumentos, se indicará ***METH_NOARGS***. La función debe ser también del tipo `PyCFunction`, aunque en el segundo parámetro recibirá ***NULL***.

También es posible indicar ***METH_VARARGS | METH_KEYWORDS***. En este caso, la función recibirá también los *keyword arguments* en un tercer argumento con un `PyObject*` conteniendo un diccionario con esos *keyword arguments*. Por lo tanto, la función que escribiremos ya no será del tipo `PyCFunction`, sino del tipo `PyCFunctionWithKeywords`, que se define así:

```c
PyObject *PyCFunctionWithKeywords(PyObject *self, PyObject *args, PyObject *kwargs);
```

En tal caso, podemos extraer los argumentos como queramos, pero hay una función frecuentemente usada en estos casos: `PyArg_ParseTupleAndKeywords()` (se verá más adelante).

Sin embargo, en este caso, a la hora de registrar nuestra función en la tabla de métodos, no podemos hacerlo tal cual, ya que le estaremos pasando como referencia a la función un identificador de tipo `PyCFunctionWithKeywords` cuando la estructura espera un tipo `PyCFunction`. Debemos solucionarlo, simplemente, con un *cast* a este último:

```c
static PyMethodDef SpamMethods[] = {
    {"miFunc", (PyCFunction)spam_miFunc, METH_VARARGS | METH_KEYWORDS, "Hace cosas."},
    /* ... */
    {NULL, NULL, 0, NULL}
};
```

El resto de *calling conventions* disponibles están *deprecated*.

El siguiente paso es inicializar el módulo, que se hará, lógicamente a través de su **función de inicialización**. Para ello necesitaremos proporcionar a esta función toda la información sobre dicho módulo, información que incluiremos en el `struct` de definición del módulo, de tipo `struct PyModuleDef`.

Los campos más importantes de esta estructura son (en orden de definición):

- ***m_base***: contiene siempre el valor ***PyModuleDef_HEAD_INIT*** (de tipo `PyModuleDef_Base`).
- ***m_name***: nombre del módulo (*string*).
- ***m_doc***: *docstring* del módulo (*string*).
- ***m_size***: si se le da un valor (entero) mayor que 0, el estado del módulo se guardará en una zona de memoria propia, en lugar de en las variables globales estáticas. Esto es útil en caso de sub-intérpretes que carguen el módulo, en cuyo caso cada instancia tendrá su propio estado. Si no, ***-1*** indica que el módulo no soporta sub-intérpretes, y tiene estado global (este campo tiene tipo `Py_ssize_t`, un tipo de entero).
- ***m_methods***: un apuntador a la tabla de métodos (`PyMethodDef*`). Puede ser ***NULL*** si el método no define funciones.

En cuanto al tipo `Py_ssize_t`: el compilador *C* dispone del tipo `size_t` (***stddef.h*** y otros), que es un tipo *unsigned*, de mínimo 16 *bits*, capaz de albergar **cualquier índice** a memoria. Entonces, en el presente *API*, `Py_ssize_t` es un tipo de entero basado en `size_t`, aunque es *signed*. Para acceder a memoria es mejor utilizar un tipo de datos como `size_t` que cualquier otro tipo de entero, pero en el caso de *Python*, que admite índices negativos, había que usar un tipo con signo. De ahí al uso de `Py_ssize_t`.

```c
static struct PyModuleDef spammodule = {
    PyModuleDef_HEAD_INIT,
    "spam",
    spam_doc,
    -1,
    SpamMethods
};
```

Ahora ya podemos escribir la función de inicialización. Se deberá llamar ***PyInit_\<nombre>***, donde ***nombre*** es el nombre exacto del módulo. En nuestro caso, sería ***PyInit_spam***. La función retorna un valor de tipo `PyMODINIT_FUNC`, y **es el único elemento global que no se declarará como** `static`.

```c
PyMODINIT_FUNC PyInit_spam(void)
{
    return PyModule_Create(&spammodule);
}
```

`PyMODINIT_FUNC` en realidad define un tipo de retorno `PyObject*` con las declaraciones de *linkage* necesarias para que la función sea accesible desde *Python* (por eso no debemos declararla `static`). La función retorna lo retornado por `PyModule_Create()`: el objeto módulo que ha creado. Al retornar el objeto módulo, este queda insertado en ***sys.modules***, listo para su uso.

Podemos añadir propiedades al módulo, de modo que no solo proporcione funciones, sino también objetos con valores (constantes o no). Para ello se usan funciones como `PyModule_AddObject()`, `PyModule_AddIntConstant()`, `PyModule_AddStringConstant()`, `PyModule_AddIntMacro()` o `PyModule_AddStringMacro()`.

`PyModule_AddObject()` añade un objeto a un módulo. El primer parámetro es el módulo, el segundo el nombre con el que se añadirá, y el tercero es el objeto a añadir. La función roba (*steals*) la referencia a dicho objeto, de tal modo que a su vez transfiere la *ownership* al módulo en sí (si alguien quita el objeto del módulo, se decrementará su conteo, y posiblemente será liberado). Sin embargo, estas transferencias de propiedad solo se materializan si la función tiene éxito, por lo que es doblemente importante comprobar si la función ha fallado, en cuyo caso deberemos nosotros decrementar el conteo, o gestionar la *ownership*, que seguirá siendo nuestra.

`PyModule_AddIntConstant()` y `PyModule_AddStringConstant()` añaden una constante entera (`long int`) o *string* (`const char*`) al módulo. Los parámetros son como los de `PyModule_AddObject()`, solo cambia el tipo del tercer parámetro.

`PyModule_AddIntMacro()` y `PyModule_AddStringMacro()` son equivalentes a las dos anteriores, pero el valor es una macro del tipo adecuado, y prescinden del segundo parámetro, ya que como nombre se usa el nombre de dicha macro.

```c
#define VALCONST 50
#define STRINGCONST "Un string cualquiera"

static PyObject *ob;
static char *stri = "Otro string";
static const int entero;

/* ... */

PyMODINIT_FUNC PyInit_spam(void)
{
    m = PyModule_Create(&spammodule);
    PyModule_AddObject(m, "atributo1", ob);
    PyModule_AddIntConstant(m, "atributo2", entero);
    PyModule_AddStringConstant(m, "atributo3", stri);
    PyModule_AddIntMacro(m, VALCONST);
    PyModule_AddStringMacro(m, STRINGCONST);
    return m;
}
```

La función de inicialización es invocada al hacer el `import` desde *Python*.

#### 2.1.5 Compilation and Linkage

Para compilar y enlazar el módulo creado para que sea importable a *Python* tenemos dos opciones: la primera es compilar el código fuente y *linkarlo* con el intérprete. Esto implica volver a compilar el intérprete desde su código fuente, incluyendo nuestros módulos. Ignoraremos esto y nos centraremos en la otra aproximación: la creación de nuestros módulos como librerías compartidas. Se verá más adelante (2.4 y 2.5).

#### 2.1.6 Calling Python Functions from C

A veces nos interesará que nuestra función *C* haga llamadas a funciones *Python*. Es frecuente tener que llamar a una función *callback*. Se podría pensar, como ejemplo, en una función de ordenación hecha en un módulo en *C* que recibe como argumento una función de comparación *Python*. La función de ordenación *C* tendrá que invocar a esa función *Python* de comparación para poder ordenar.

En este caso, para leer desde *C* la referencia a la función que nos llega desde *Python*, usaremos `PyArg_ParseTuple()` como de costumbre, pero en lugar de especificar ***s*** para el formato (*string*), usaremos ***O*** (objeto, sin especificar). Una vez tengamos ese objeto, podemos utilizarlo para hacer esa llamada a la función *Python*:

```c
PyObject_CallObject(my_callback, tupla_args);
```

Tanto ***my_callback*** como ***tupla_args*** son del tipo `PyObject*`, y representan respectivamente la función *callback* y los argumentos (una tupla) a esta (si es ***NULL*** no se pasan argumentos). Retorna un **nuevo** objeto *Python*.

Para pasar *keyword arguments*:

```c
PyObject_CallObject(my_callback, tupla_args, dict_args);
```

Es como la anterior, pero en este caso, además, ***dict_args*** es un diccionario con los *keyword arguments*. Tanto ***dict_args*** como ***tupla_args*** pueden ser ***NULL***. Más tarde veremos cómo construir tipos *Python* (listas, tuplas, diccionarios) con la función `Py_BuildValue()`.

#### 2.1.7 Extracting Parameters in Extension Functions

```c
int PyArg_ParseTuple(PyObject *args, const char *format, ...);
```

Extrae elementos de una tupla. La veremos con más detalle más adelante.

Los objetos *Python* recibidos en la función llamadora son *borrowed references* (2.1.10), por lo que no debemos decrementar el conteo de referencias tras usarlos.

Estas funciones retornan verdadero si todo funciona bien, o falso en caso contrario.

#### 2.1.8 Keyword Parameters for Extension Functions

```c
int PyArg_ParseTupleAndKeywords(PyObject *args, PyObject *kw, const char *format, char *keywords[], ...);
```

No se pueden parsear tuplas anidadas cuando usamos *keyword arguments*. Se verá también más adelante.

#### 2.1.9 Building Arbitrary Values

```c
PyObject *Py_BuildValue(const char *format, ...);
```

Realiza la acción inversa a las funciones de parseo de tuplas que hemos visto. Utiliza una *format string* similar, pero los argumentos no son apuntadores sino simples valores; y no son de salida, sino de entrada. La función retorna un objeto `PyObject*` que se puede retornar a *Python* perfectamente.

El objeto retornado es una tupla siempre y cuando el número de *format units* sea superior a uno. Si es uno, retorna ese simple valor. Si es cero, retorna ***None***. Si queremos forzar a que retorne una tupla con 0 o 1 elemento, hay que indicar un par de paréntesis en la *format string*.

```c
Py_BuildValue("");                   // None
Py_BuildValue("i", 123);             // 123
Py_BuildValue("iii", 123, 456, 789); // (123, 456, 789)
Py_BuildValue("s", "hello");         // 'hello'
Py_BuildValue("y", "hello");         // b'hello'
Py_BuildValue("ss", "hello", "world");  // ('hello', 'world')
Py_BuildValue("s#", "hello", 4);     // 'hell'
Py_BuildValue("y#", "hello", 4);     // b'hell'
Py_BuildValue("()");                 // ()
Py_BuildValue("(i)", 123);           // (123,)
Py_BuildValue("(ii)", 123, 456);     // (123, 456)
Py_BuildValue("(i,i)", 123, 456);    // (123, 456)
Py_BuildValue("[i,i]", 123, 456);    // [123, 456]
Py_BuildValue("{s:i,s:i}",
      "abc", 123, "def", 456);       // {'abc': 123, 'def': 456}
Py_BuildValue("((ii)(ii)) (ii)",
      1, 2, 3, 4, 5, 6);             // (((1, 2), (3, 4)), (5, 6))
```

Se verá con más detalle más adelante.

#### 2.1.10 Reference Counts

En nuestras funciones *C* debemos preocuparnos de emparejar correctamente nuestros `free()` con nuestros `malloc()` (***stdlib.h***). Pero cuando trabajamos con objetos *Python*, de tipo `PyObject*` la gestión de memoria la realiza el *garbage collector* de *Python*. Para gestionar la memoria reservada para estos objetos, debemos trabajar con dicho *GC*, estableciendo el conteo de referencias a los objetos *Python* correctamente, evitando así *memory leaks* (memoria reservada que no se libera) y uso de memoria liberada.

> Todos los objetos *Python* (de tipo `PyObject*`) tienen un tipo *Python* asociado (objeto de tipo `PyTypeObject`) y una *reference count*. Los objetos `PyObject*` son almacenados en memoria dinámica. Sin embargo los objetos tipo (`PyTypeObject`) no se liberan (suelen ser estáticos).

Al mantener correctamente el conteo de referencias a un objeto *Python*, este es liberado (*deallocated*) cuando el conteo llega a cero. En ese caso, se ejecuta la función de liberación (*deallocator*) del objeto, que es un apuntador a función que reside en la estructura del tipo de objeto. Si el objeto en sí contenía otros objetos, el conteo de referencias a estos objetos que contenía es a su vez decrementado, y si es necesario son liberados. Y así recursivamente.

Para incrementar el conteo de referencias a un objeto *Python* se usa la macro `Py_INCREF()`, a la que se pasa como argumento un apuntador a dicho objeto. Igualmente, para decrementar dicho conteo, se usa `Py_DECREF()` al que se pasa el también el objeto como argumento. Si al decrementar el conteo este llega a cero, se libera la memoria reservada al objeto. Por lo tanto, la liberación de un objeto *Python* solo se produce tras una llamada a `Py_DECREF()`.

Para saber cuándo invocar estas macros, hay que conocer el concepto de **propiedad** (*ownership*): ninguna función es propietaria de un **objeto**, pero sí puede serlo de una **referencia** a un objeto concreto. **Que una función o código posea la propiedad de una referencia significa únicamente una cosa: que es responsable de decrementar el conteo de tal referencia cuando deje de usarla**.

Cuando una función le pasa la *ownership* de una referencia a su llamador, se dice que el llamador recibe **una nueva referencia**. Por lo tanto, recibir una nueva referencia implica que solo debemos preocuparnos por decrementar el conteo cuando haga falta, y mientras no lo hagamos, el objeto seguirá debidamente *allocated*.

La propiedad de una referencia puede pasarse no solo a través de retornar un objeto, sino también a través del paso del objeto como argumento (aunque no es muy frecuente). Esto se hace cuando la función llamada roba la propiedad de esa referencia al objeto (*stealing the reference*), es decir, la función llamada se convierte en la responsable de llamar a `Py_DECREF()` en el momento que no necesite más la referencia. Si pasamos un objeto a una función así, debemos dejar de considerarnos propietarios de tal referencia, por lo que ya no será necesario decrementar su conteo nunca (ya no tenemos ninguna responsabilidad al respecto).

También se podría pasar una referencia a un objeto como argumento, pero sin transferir la propiedad de dicha referencia. En ese caso, seguimos manteniendo la responsabilidad de decrementar el conteo tras el retorno de dicha función. Entonces, la función llamada puede utilizar la referencia al objeto, sin robar (*steal*) la propiedad de la misma. Se dice en este caso que la función llamada toma prestada (*borrows*) la referencia. Puede utilizar el objeto pero no tiene la propiedad de la referencia, con lo que no decrementará nunca el conteo. Sin embargo, para evitar que se libere el objeto mientras usa la referencia, la función llamada debe tener la seguridad de que el objeto no será liberado de memoria durante su ejecución. Para ello no debe trabajar con tal objeto más allá del momento en que el módulo llamador acabe decrementando la referencia (y posiblemente liberando el objeto).

En todo caso, mientras se está ejecutando la función llamada, el llamador no ejecuta nada, con lo que la función puede disponer del objeto tranquilamente hasta que retorne. Pero si la función llamada quiere conservar la referencia más tiempo, por ejemplo hasta la siguiente llamada, si no está seguro de que el llamador no decrementará el conteo, tendrá que convertirse en propietaria de la referencia invocando `Py_INCREF()` y almacenando el objeto en una variable global o estática (no en una variable local, que haría perder la referencia al salir del *scope* local). Esto no afecta al llamador, que sigue siendo responsable de la antigua referencia, y no de esta nueva.

Un caso importante de *borrow* de referencias es una llamada desde *Python* a una función *C* de un módulo, ya que está garantizado que los argumentos que se pasen a dicha función, es decir, la tupla de argumentos, estará referenciada en el código *Python* llamador durante toda la ejecución de la función *C*. Así, no es necesario incrementar el conteo (adquirir la propiedad de la referencia) para asegurarnos de que la tupla no será liberada. Y por supuesto, tampoco hay que decrementar el conteo de la tupla recibida en la función *C*, ya que la función no posee la propiedad de la referencia a la tupla. Otro caso sería si la función deseara guardar alguno de los argumentos recibidos (o todos) en el estado global del módulo, en cuyo caso sí debería convertirse en propietaria de tales objetos.

Cuando pasamos un objeto *Python* como argumento a una función de este *API*, normalmente esta toma prestada dicha referencia; son pocas las funciones que roban la propiedad de la misma (*reference stealing*). Básicamente lo hacen `PyList_SetItem()` y `PyTuple_SetItem()`, que se apropian de la referencia del objeto a añadir (no de la tupla o lista donde se añaden). En este caso no podríamos pasarle por parámetro una referencia que hubiésemos tomado prestada; necesitamos hacernos propietarios de la referencia incrementando el conteo para llamar a la función (que nos robaría tal referencia, y dejaríamos de ser propietarios inmediatamente).

La forma más habitual de crear objetos *Python* es mediante `Py_BuildValue()`. Para establecer elementos en una secuencia, se suele usar `PyObject_SetItem()`, a la que podemos pasar *borrowed references* (porque no roba referencias). Si le pasamos una referencia de nuestra propiedad, seguirá siendo de nuestra propiedad tras la llamada.

Una función que retorna un objeto *Python* es muy frecuente que transfiera también la propiedad de la referencia al llamador. Esto debe ser así en muchas ocasiones, ya que supongamos que el objeto es creado durante la ejecución de la función llamada; retornar una *borrowed reference* implicaría que el código llamante no es responsable de decrementar el conteo de un objeto cuya única referencia es la que ha recibido dicho código llamante. Por lo tanto, este objeto nunca será liberado por nadie. Todas las funciones que **crean y retornan** un objeto *Python* transfieren también la propiedad de la referencia al código llamante.

Sin embargo un retorno sí puede ser una *borrowed reference* (que toma prestada el llamador) en otras circunstancias, concretamente, una función puede retornar una referencia sin transferir su propiedad cuando tras la ejecución de dicha función existen todavía otras referencias a ese objeto en otras partes del código, es decir el conteo es superior a cero y alguien más tiene la propiedad de una referencia al objeto. La función está retornando un objeto que no ha creado ella, sino que existía ya previamente. La función no era propietaria de la referencia retornada (es por ello que retorna una *borrowed reference*):

```c
long int sum_list(PyObject *list)  // recibimos una lista de enteros que tomamos prestada
{
    Py_ssize_t i, n;
    long int total = 0, value;
    PyObject *item;
    n = PyList_Size(list);

    for (i = 0; i < n; i++)
    {
        item = PyList_GetItem(list, i);  // recibimos referencia borrowed en 'item'
        value = PyLong_AsLong(item);
        total += value;
    }
    return total;
}
```

Como vemos en el ejemplo, la variable ***item***, de tipo `PyObject*` recibe, a través de `PyList_GetItem()`, una referencia *borrowed* del elemento especificado. Es *borrowed* porque la función no es propietaria de la referencia cuando la retorna. Si en lugar de esta función hubiésemos usado `PySequence_GetItem()`, sí habríamos obtenido la propiedad de ***item*** cada vez, teniendo que llamar a `Py_DECREF()` tras cada uso de la referencia. El hecho de que la función llamada transfiera al llamador la propiedad de la referencia retornada o no, depende de la implementación de la función concreta.

Las funciones que extraen objetos de otros objetos suelen retornar dicho objeto al mismo tiempo que la *ownership* de la referencia. Sin embargo, hay excepciones, como hemos visto. Estas funciones retornan referencias *borrowed*: `PyTuple_GetItem()`, `PyList_GetItem()`, `PyDict_GetItem()` y `PyDict_GetItemString()`.

Otra forma de extraer objetos de una secuencia (lista, tupla,...) es mediante apuntadores en la lista de argumentos que pasarán a referenciar el objeto extraído. Es el caso de funciones como `PyArg_ParseTuple()` o `PyArg_UnpackTuple()`, entre muchas otras. En este caso, los argumentos que les pasamos que sean del tipo apuntador a `PyObject*` recibirán una referencia a un objeto *Python*. Es la forma de recibir múltiples objetos por parte de una función. Es frecuente que estas funciones retornen una *borrowed reference*. Esto es posible porque sigue existiendo un propietario de dichos objetos fuera de la función llamada: dicho propietario es el objeto secuencia en sí (y por tanto el propietario o propietarios de tal objeto secuencia). Así, mientras se está ejecutando la función llamante que ha recibido estas *borrowed references* podemos usar los objetos tranquilamente, ya que tales propietarios no se están ejecutando, y por tanto no decrementarán el conteo de estos objetos que hemos recibido.

En relación a esto, un error frecuente que puede cometer una función es el siguiente: la función llama a una de esta funciones que extrae un objeto de una lista retornando una o varias *borrowed references*. Pero las trata como si tuviese la propiedad de dichas referencias, olvidándose de incrementar su conteo. En este caso, guardaría tales referencias en variables estáticas o globales y acabaría retornando en algún momento. Más tarde, podría volver a ejecutarse, todavía manteniendo en esas variables globales o estáticas referencias a esos objetos. El problema es que entre llamadas, el propietario de la secuencia de la que se extrajeron los objetos puede, por ejemplo, haberlos sacado de la lista, decrementando así su conteo y posiblemente liberándolos. Entonces la función despistada tendría referencias a elementos liberados.

Por lo tanto, cuando usemos ese tipo de funciones que extraen elementos, o una función que retorna un objeto *Python*, pero en todo caso a través de *borrowed references*, debemos recordar que solo las podemos almacenar en variables locales, es decir, variables que no utilizaremos más que en la presente ejecución (se perderán una vez salgan del *scope* de la función). Si deseamos mantenerlas más allá (en variables estáticas o globales), hay que invocar `Py_INCREF()` antes de retornar. Alternativamente se pueden extraer estos elementos utilizando funciones genéricas del *API* que retornen el objeto junto con la propiedad de la referencia. Es el caso de las funciones que empiezan por `PyObject_`, `PyNumber_`, `PySequence_` o `PyMapping_`, que incrementan automáticamente el conteo de la referencia obtenida para que el llamador sea el responsable de decrementar el conteo cuando no la use más.

Cada función debe documentar qué hace con las referencias a objetos *Python* que recibe por parámetro: si roba o si toma prestada la propiedad. También debe documentar, en caso de retornar un objeto *Python*, si transfiere o no la propiedad al llamador. El llamador debe adaptarse al funcionamiento de la función llamada.

Una función *C* que retorne un objeto a *Python* debe transferir obligatoriamente la propiedad de la referencia, es decir, debe ser propietaria de la referencia antes de retornarla.

Las macros `Py_INCREF()` y `Py_DECREF()` no comprueban si el apuntador pasado es ***NULL*** antes de ejecutarse. Sin embargo, las variantes `Py_XINCREF()` y `Py_XDECREF()` sí lo hacen. En caso de recibir ***NULL*** por parámetro no hacen nada, en lugar de levantar error.

Para finalizar, hay que tener en cuenta qué tipos de objetos entrarán en esta dinámica del conteo de referencias: referencias a objetos *Python* cuyo tiempo de vida se extienda más allá de la ejecución actual de la función. Quedarán, por tanto, fuera de este mecanismo, todos los objetos *C*, sea cual sea su tiempo de vida, ya que no es el *garbage collector*, sino nosotros mismos, los responsables de liberarlos, si procede:

- Un objeto *C* (no apuntador) declarado `static` o en una variable global se liberará automáticamente al terminar el programa.
- Una zona de memoria reservada en el *heap* (con `malloc()` o similar) deberá ser liberada (con `free()`) adecuadamente, ya sea en la ejecución actual, o en una futura (en cuyo caso deberemos guardar un apuntador estático o global para la futura ejecución).

Si asignamos a una variable local un objeto *Python* ya existente, no hace falta incrementar el conteo de referencias, ya que esta variable local dejará de referenciar al objeto al retornar la función.

### 2.2 Defining Extension Types: Tutorial

Pendiente.

### 2.4 Building C and C++ Extensions

Una extensión *C* es un archivo de biblioteca compartida que exporta una función de inicialización. En *Unix* será un archivo ***.so***, y en *Windows* será un archivo ***.pyd***, que básicamente es lo mismo que un archivo ***.dll*** pero con una extensión propia de *Python* para evitar conflictos con otras bibliotecas *DLL*.

Tal archivo debe pues tener el nombre correcto del módulo y la extensión adecuada al sistema operativo. Para que sea localizable, debe residir en una de las carpetas de ***PYTHONPATH*** (o estar localizable según la variable ***sys.path***).

Este apartado explica cómo construir nuestro módulo sin utilizar directamente un compilador, sino a través de un *script* de creación de un archivo de biblioteca compartida usando el módulo *Python* ***distutils***. Sin embargo no vamos a documentar ese proceso, ya que dicho módulo está en fase de desaparición (será marcado como *deprecated* próximamente).

Vamos a ver cómo podríamos generar el módulo usando el compilador *GCC* en sistemas *Unix*. Se podrían usar estos comandos (se pueden usar a discreción, son solo un ejemplo):

```
gcc -DNDEBUG -g -O3 -std=c18 -Wall -fPIC -I/usr/local/include/python3.10 -c demo.c -o build/demo.o
gcc -shared build/demo.o -o build/demo.so
```

La primera orden genera el código objeto en ***demo.o***:

- `-DNDEBUG` define el nombre de macro ***NDEBUG***, que según el estándar *C*, desactiva las *assertions* (***assert.h***).
- `-g` compila con símbolos para depuración de código.
- `-O3` activa optimizaciones.
- `-std=c18` indica el estándar a utilizar (en este caso, *C18*).
- `-Wall` activa todos los avisos (*warnings*) del compilador.
- `-fPIC` genera código relocalizable (*position-independent code*), necesario en bibliotecas compartidas.
- `-I/usr/local/include/python3.10` incluye el directorio ***/usr/local/include/python3.10*** en la lista de directorios donde buscar archivos de cabecera ***.h***.
- `-c` impide que se cree archivo ejecutable: no se realiza enlazado, se detiene tras la creación del archivo de código objeto.
- `demo.c` es el archivo fuente.
- `-o build/demo.o` define el nombre del archivo de salida. En este caso, ***build/demo.o***.

En cuanto a la segunda orden, crea la biblioteca compartida ***demo.so***:

- `-shared` indica que se debe crear una biblioteca compartida.
- `build/demo.o` es el archivo de entrada.
- `-o build/demo.so` es el archivo de salida (***build/demo.so***).

## Referencia rápida de algunas funciones útiles

Esta información puede encontrarse en el documento *The Python/C API*.

### *Reference Counts*

```c
void Py_INCREF(PyObject *o);
void Py_DECREF(PyObject *o);
void Py_XINCREF(PyObject *o);
void Py_XDECREF(PyObject *o);
```

Incrementan y decrementan el conteo de referencias, como hemos visto.

```c
void Py_CLEAR(PyObject *o);
```

Establece en 0 el conteo de referencias al objeto ***o***. Si el apuntador es ***NULL***, la función no tiene efecto.

### Excepciones

Existe un indicador global de error por cada hilo, que consiste en 3 apuntadores: el tipo de excepción, el valor de la excepción y el objeto *traceback*.

Al encontrar un error, una función debe levantar una excepción. Entonces optará por tratarla y continuar normalmente (limpiando el estado de error), o descartar todas las referencias que posea (y liberar otros recursos abiertos), y retornar indicador de error: ***NULL*** (si retorna apuntador) o ***-1*** (si retorna entero). Como excepción, las funciones que empiezan por `PyArg_` retornan ***0*** en caso de error (***1*** si tienen éxito).

Si opta por retornar error, la función llamante no levantará excepción (de eso se encarga la función que encuentra el error), y decidirá si tratar y limpiar la excepción, o pasar la ejecución a su llamador (liberando recursos y retornando error).

Este *API* proporciona unas variables globales de tipo `PyObject*` con el tipo de cada una de las excepciones posibles, cuyo nombre es el nombre del objeto excpeción *Python* con el prefijo ***PyExc\_*** (***PyExc_ZeroDivisionError***, ***PyExc_ValueError***, ***PyExc_TypeError***, etc.).

```c
void PyErr_Clear();
```

Limpia el estado de error.

```c
void PyErr_SetString(PyObject *type, const char *message);
```

Levanta una excepción. El primer argumento es el objeto excepción pertinente (no hay que incrementar el conteo de referencias, ya que lo trata como una *borrowed reference*), y el segundo es un mensaje de error en un *string* codificado con *UTF-8*, que será el valor asociado a la excepción.

```c
void PyErr_SetObject(PyObject *type, PyObject *value);
```

Es como `PyErr_SetString()`, pero indicando un objeto *Python* como valor de la excepción (no robará tampoco la *ownership* de ese objeto valor).

```c
PyObject* PyErr_Occurred();
```

Retorna una *borrowed reference* del tipo de excepción que esté levantada. Si no hay, retorna ***NULL***. Dado que el estado de error se almacena por cada hilo, si el código es *multithread*, el llamador a la función debe tener el *GIL* (*Global Interpreter Lock*).

> No se debe comparar el valor retornado con un tipo de excepción concreto, ya que uno podría ser una instancia y el otro la clase, o uno podría ser una subclase del otro y la comparación retornaría falso. Para comprobación se debe usar `PyErr_ExceptionMatches()`.

```c
int PyErr_GivenExceptionMatches(PyObject *given, PyObject *exc);
```

Retorna verdadero si la excepción ***given*** es del tipo en ***exc***. Si ***exc*** es un objeto clase, retornará verdadero si ***given*** es una subclase de ***exc***, o una instancia de esta o de una subclase de la misma. Si ***exc*** es una tupla, se comprobarán todos los tipos de excepción que contenga (recursivamente si hay anidación) buscando una coincidencia.

```c
int PyErr_ExceptionMatches(PyObject *exc);
```

Equivalente a `PyErr_GivenExceptionMatches(PyErr_Occurred(), exc)`. **Solo debe llamarse si hay una excepción levantada**.

```c
PyObject* PyErr_NewException(const char *name, PyObject *base, PyObject *dict);
```

Retorna una nueva referencia a un tipo nuevo de excepción, como vimos anteriormente. ***base*** y ***dict*** suelen ser ***NULL***.

### *Parsing arguments and building values*

```c
int PyArg_ParseTuple(PyObject *args, const char *format, ...);
```

Extrae los elementos de una tupla. Si alguno de los argumentos extraídos es un objeto *Python*, será una *borrowed reference* (no habrá que decrementar conteo). Retorna 1 si tiene éxito o 0 en caso contrario.

La *format string* definirá el tipo de datos esperado de los argumentos de la tupla. Siempre hay que pasar la dirección de la variable que almacenará el valor del argumento en cuestión. Si es un apuntador, la dirección del apuntador.

Cuando extraemos un argumento que precisa de un *buffer*, como un *string*, la función asigna al apuntador correspondiente una dirección de memoria a un *buffer* ya existente, gestionado por el objeto *Python* del que extraemos la información. Dicho *buffer* tiene el mismo tiempo de vida que el objeto y no hay que liberarlo de memoria (con las excepciones de los datos tipo ***es***, ***es#***, ***et*** y ***et#***).

En ocasiones, los datos se leen y rellenan una estructura `Py_buffer`, con un *buffer* en el miembro ***buf***. En ese caso, el *buffer* queda bloqueado para escritura (sobre posibles objetos mutables). Para liberarlo una vez se ha terminado de modificar, se debe llamar a `PyBuffer_Release()` para que otro posible hilo pueda escribir sobre él (código *multi-threaded*).

La *format string* está compuesta por unidades de formato, que describen objetos *Python*. Una *format unit* suele ser uno o dos caracteres o una serie de unidades de formato entre paréntesis. Cada unidad proporcionará un valor o una dirección de memoria.

Algunas unidades de formato retornan la longitud del argumento (***s#***, ***y#***,...). Tal longitud es un entero, del tipo `int`, aunque es mejor que retorne un tipo `Py_ssize_t`, como se ha visto anteriormente. Para que esto sea así, hay que definir la macro `PY_SSIZE_T_CLEAN` **antes** de incluir ***Python.h***:

```c
#define PY_SSIZE_T_CLEAN
#include <Python.h>
//...
```

Posibles *format units* del *string* de formato:

- ***s*** convierte un *string Unicode* (no `bytes`) de *Python* a un apuntador a `const char*`, que apuntará a un *buffer null-terminated*, codificado con *UTF-8*. El *string Python* no puede contener *codepoints* nulos.
- ***s\**** como ***s***, pero también acepta objetos de tipo `bytes`, y en lugar de establecer la dirección del apuntador *C*, rellena una estructura `Py_buffer` (le pasaremos un apuntador a `Py_buffer`). La dirección del *buffer* resultante estará en el miembro ***buf*** de la estructura. En este caso, el *string C* puede contener *bytes* nulos.
- ***s#*** es como ***s*** pero acepta también objetos de tipo `bytes`, solo que en esta ocasión no acepta objetos mutables. Proporciona dos valores: el *string* (pasaremos un apuntador a `const char*`) y la longitud (pasaremos un apuntador a `int` o a `Py_ssize_t`).
- ***z*** es como ***s***, pero el *string Python* puede ser ***None***, en cuyo caso el apuntador *C* será ***NULL***.
- ***z\**** es como ***s\****, pero el *string Python* puede ser ***None***, en cuyo caso el miembro ***buf*** de la estructura será ***NULL***.
- ***z#*** es como ***s#***, pero el *string Python* puede ser ***None***, en cuyo caso el apuntador *C* será ***NULL***.
- ***y*** es como ***s***, pero solo acepta objetos de tipo `bytes` **inmutables**, que no pueden contener *bytes* nulos).
- ***y\**** es como ***s\****, pero solo acepta objetos tipo `bytes`, que sí pueden contener *bytes* nulos. **Es la forma recomendada de obtener datos binarios**.
- ***y#*** es como ***s#***, pero solo acepta objetos de tipo `bytes` **inmutables**, que sí pueden contener *bytes* nulos).
- ***S*** extrae un objeto `bytes` directamente, sin convertir. Pasaremos un apuntador al tipo *C* (apuntador a `PyObject*` o a `PyBytesObject*`).
- ***Y*** extrae un objeto `bytearray` directamente, sin convertir. El tipo *C* será `PyObject*` o `PyByteArrayObject*` (pasaremos apuntador a uno de estos tipos).
- ***U*** extrae un *string Unicode* directamente, sin convertir. El tipo *C* será `PyObject*` (pasaremos apuntador a `Py_Object*`).
- ***w\**** es como ***y\****, pero permite lectura y escritura en el *buffer* obtenido. Una vez se ha terminado de modificar el *buffer* se debe desbloquear para escritura con `PyBuffer_Release()`.
- ***es*** es una variación de ***s*** que permite establecer la codificación del *string*. Necesita dos argumentos: el primero se utiliza como entrada, no salida: es un `const char*` que apunta al nombre de la codificación deseada (debe ser conocida por *Python*); si es ***NULL***, se entenderá *UTF-8*. El segundo es `char**`, es decir, un apuntador a un *buffer string*. El apuntador que referenciamos pasará a apuntar a un *buffer* con el texto solicitado. Para ello, la función reservará la memoria necesaria para el *buffer*, que no es gestionado por el objeto *Python*, con lo que cuando hayamos terminado de trabajar con él deberemos invocar `PyMem_Free()` para liberarlo. Debemos pasar como argumentos a `PyArg_ParseTuple()`, pues, uno de tipo `const char*` (es de entrada) y una referencia a un apuntador a un *string*, es decir, un apuntador a un apuntador a un *buffer* caracteres, es decir, un `char***`.
- ***et*** es como ***es***, pero acepta también objetos `bytes` (o `bytearray`), en cuyo caso, el resultado se pasará sin codificar (se asumirá que la codificación del `bytes` o `bytearray` ya es la deseada).
- ***es#*** es como ***es***, pero por un lado permite datos de entrada con caracteres nulos, y por otro lado requiere de un tercer argumento (pasaremos apuntador a `int` o a `Py_ssize_t`), en el que colocará la longitud del *buffer* de salida. En este caso hay dos modos de funcionamiento:
    - si el apuntador al *buffer* es nulo inicialmente, funcionará como de costumbre;
    - en caso contrario, usará el valor del *buffer* para colocar allí el texto, sin reservar memoria, ya que asumirá que ya hay una cantidad de memoria reservada, igual al valor inicial del tercer argumento.
- ***et#*** es como ***es#***, pero permite objetos `bytes` o `bytearray` (que pasarán sin codificar a la salida).
- Números. En las conversiones a *unsigned* no hay comprobación de *overflow*.
    - ***b*** entero no negativo a `unsigned char`.
    - ***B*** entero a `unsigned char`.
    - ***h*** entero a `short int`.
    - ***H*** entero a `unsigned short int`.
    - ***i*** entero a `int`.
    - ***I*** entero a `unsigned int`.
    - ***l*** entero a `long int`.
    - ***k*** entero a `unsigned long int`.
    - ***L*** entero a `long long int`.
    - ***K*** entero a `unsigned long long int`.
    - ***n*** entero a `Py_ssize_t`.
    - ***c*** objeto `bytes` o `bytearray` de longitud 1 a `char`.
    - ***C*** objeto *string* de longitud 1 a `int`.
    - ***f*** número de punto flotante a `float`.
    - ***d*** número de punto flotante a `double`.
    - ***D*** número complejo a estructura `Py_complex`.
- ***O*** es el objeto tal cual, sin conversión (tipo `PyObject*`).
- ***O!*** es como ***O***, pero en este caso precisa dos argumentos: el primero es de entrada, y es un apuntador a un objeto que define un tipo *Python*; el segundo es el acostumbrado apuntador a un objeto, que debe ser del tipo especificado en el primer argumento.
- ***O&*** realiza una conversión a través de una función indicada.
- ***p*** convierte un `bool` de *Python* a `int`.

Si en la *format string* se indican paréntesis, dentro de los cuales hay (opcionalmente) *format units*, definimos una secuencia *Python* con los elementos indicados. Pueden anidarse.

Existen otros caracteres (no permitidos dentro de paréntesis):

- ***|*** (barra *pipe*) indica que los argumentos a continuación son opcionales. Si no existen, la función no hace nada con el argumento *C* correspondiente. Las variables que van a recoger argumentos opcionales deberían inicializarse con su valor por defecto.
- ***$*** indica que el resto de los argumentos son *keyword only* (solo en `PyArg_ParseTupleAndKeywords()`).
- ***:*** indica que la lista de *format units* ha terminado; tras ***:*** se indica el nombre de función que aparecerá en los posibles mensajes de error (valor asociado de la excepción que se levante).
- ***;*** similar a ***:***, pero indica el mensaje de error en lugar del mensaje de error por defecto. ***:*** y ***;*** se excluyen mutuamente.

```c
int PyArg_ParseTupleAndKeywords(PyObject *args, PyObject *kw, const char *format, char *keywords[], ...);
```

Esta función es como `PyArg_ParseTuple()`, pero aquí proporcionamos, a parte de ***args*** (argumentos posicionales), el diccionario de argumentos *keyword*, ***kw***, que es el que ha recibido nuestra función como tercer argumento. Por otro lado, indicamos el formato de cada argumento en ***format***, y la secuencia (orden) de los argumentos en el *array* de *strings* ***keywords***, que es un *array NULL-terminated* (el último elemento debe ser ***NULL***) con los nombres de los *keyword arguments* en el orden deseado (los nombres vacíos indican parámetro *positional only*). El valor de retorno es como el de `PyArg_ParseTuple()`.

En una **llamada** a una función desde *Python*, los argumentos posicionales van antes de los *keyword arguments*. Con `PyArg_ParseTuple`, no se recogerá ningún *keyword argument* (solo los posicionales). De los argumentos posicionales podemos indicar cuántos serán obligatorios y cuantos opcionales mediante el cáracter ***|*** en la *format string*. Por ejemplo, el string ***ii|iii*** admitirá solamente entre 2 y 5 argumentos posicionales. Cualquier otro número de argumentos posicionales generará error.

En cuanto a `PyArg_ParseTupleAndKeywords()`, la lista de *keywords* definirá cuántos de los argumentos son posicionales y cuántos son *keyword arguments*. En todo caso, a la hora de definir esta lista, siempre se deben indicar los posicionales antes de los *keyword*, así:

```c
char *keys[] = {"", "", "primero", "segundo", "tercero", NULL};
```

En este caso, los dos primeros argumentos no pueden pasarse como *keyword* (son *positional-only*). Los tres restantes se pueden pasar como posicionales o como *keyword* (en este último caso, en el orden que se quiera). También es posible indicar qué argumentos son opcionales mediante ***|***, que puede estar en cualquier punto de la *string* (en medio de los posicionales, o de los *keyword*, etc.). Cualquiera de estas llamadas es correcta (y equivalente):

```python
foo(4, 10, 70, 5, 0)
foo(4, 10, primero=70, segundo=5, tercero=0)
foo(4, 10, tercero=0, primero=70, segundo=5)
foo(4, 10, 70, 5, tercero=0)
```

Estas son incorrectas:

```python
foo(4, 10, 70, 5)  # faltan argumentos
foo(4, arg=10, primero=70, segundo=5, tercero=0)  # ¿qué es 'arg'?
foo(4, 10, 70, tercero=0, primero=5)  # 'primero' está repetido, y faltan argumentos
```

Si queremos que a partir de cierto punto los argumentos solo se puedan indicar como *keyword* en la llamada (*keyword-only arguments*), se puede incluir el carácter ***$*** en la *format string*. Por ejemplo, siguiendo con el ejemplo anterior, ***iii$ii*** indicaría que los argumentos ***segundo*** y ***tercero*** son *keyword-only*.

> Indicar **|** y **$** en el mismo punto puede dar problemas si se indica como **$|**. Mejor hacerlo como **|$**.

```c
int PyArg_UnpackTuple(PyObject *args, const char *name, Py_ssize_t min, Py_ssize_t max, ...);
```

Esta forma de parsear la tupla de entrada no precisa de *format string*. Aquí la tupla ***args*** debe tener un mínimo de ***min*** elementos y un máximo de ***max*** (ambos números pueden ser iguales). Se deben proporcionar argumentos adicionales, apuntadores a `PyObject*`, que irán llenándose con los elementos de la tupla, sin conversiones. Los argumentos opcionales que no estén presentes en la tupla no modificarán el valor del correspondiente argumento (deberá ser inicializado anteriormente). El parámetro ***name*** es el nombre de función que se mostrará en un posible mensaje de error. Una función que parsee los argumentos de este modo debe declararse con ***METH_VARARGS*** en la tabla de funciones. Los objetos recibidos son *borrowed references*. La función retorna 1 si tiene éxito y 0 en caso contrario.

### *Building values*

```c
PyObject* Py_BuildValue(const char *format, ...);
```

Construye una tupla a partir de los argumentos (que son de entrada, por lo que no deberán ser apuntadores), y retorna una **nueva referencia** a dicha tupla. Usa una *format string* similar a la de `PyArg_ParseTuple()` y familia, aunque su efecto es a la inversa (de tipo *C* a tipo *Python*). Retorna ***NULL*** en caso de error.

Si solo se le pasa un argumento opcional, retorna un solo objeto, no una tupla (para forzar una tupla de un elemento debería incluirse la *format unit* entre paréntesis). Si no se proporciona ningún argumento opcional, retona ***None*** (para forzar tupla vacía, se debe indicar un par de paréntesis en la *format string*).

Estos son los valores que pueden tomar las *format units* (las que precisan una longitud tomarán un argumento `int` o `Py_ssize_t`):

- ***s*** convierte un *null-terminated string C* codificado con *UTF-8* en un *string Python*. Si el apuntador es ***NULL***, se convierte a ***None***.
- ***s#*** es como ***s*** pero usa un segundo argumento con la longitud del *string*, que no tiene por qué ser *null-terminated*.
- ***y*** es como ***s*** pero convirtiendo el *string C* en un objeto `bytes`.
- ***y#*** es como ***s#*** pero convirtiendo el *string C* en un objeto `bytes`.
- ***z*** y ***z#*** son lo mismo que ***s*** y ***s#*** respectivamente.
- ***u*** es como ***s*** pero la entrada es un *buffer Unicode* de `wchar_t`, con terminador nulo.
- ***u#*** es como ***u*** pero con indicador de longitud en lugar de terminador nulo.
- ***U*** y ***U#*** son lo mismo que ***s*** y ***s#*** respectivamente.
- Números:
    - ***i*** convierte `int` a entero.
    - ***b*** convierte `char` a entero.
    - ***h*** convierte `short int` a entero.
    - ***l*** convierte `long int` a entero.
    - ***B*** convierte `unsigned char` a entero.
    - ***H*** convierte `unsigned short int` a entero.
    - ***I*** convierte `unsigned int` a entero.
    - ***k*** convierte `unsigned long` a entero.
    - ***L*** convierte `long long int` a entero.
    - ***K*** convierte `unsigned long long` a entero.
    - ***n*** convierte `Py_ssize_t` a entero.
    - ***c*** convierte `int` conteniendo un *byte* a un objeto `bytes` de longitud 1.
    - ***C*** convierte `int` conteniendo un carácter (*wide*) a *string Unicode* de longitud 1.
    - ***d*** convierte `double` a punto flotante.
    - ***f*** convierte `float` a punto flotante.
    - ***D*** convierte `Py_complex*` a número complejo.
    - ***O*** y ***S*** convierten `Py_Object*` a objeto, sin cambio (solo aumenta su *reference count*).
    - ***O&*** realiza una conversión a través de una función indicada.
    - ***N*** es como ***O***, pero no incrementa el *reference count*, es decir, retorna una *borrowed reference* en lugar de una referencia nueva.

Si la *format string* indica *format units* entre paréntesis ***()***, crea una tupla. Entre corchetes ***\[\]*** una lista. Entre llaves ***{}*** un diccionario, en cuyo caso se especificarán los pares clave-valor separados por dos puntos (***:***). Los elementos de una secuencia pueden separarse por comas en la *format string*.

### Otras funciones

Los capítulos 7 y 8 del documento *The C/Python API* documentan una gran cantidad de funciones para trabajar con todo tipo de objetos *Python*. Indicamos a continuación el prototipo de algunas de ellas, a modo de referencia rápida.

#### *Object protocol*

```c
PyObject* Py_NotImplemented;  // objeto NotImplemented
Py_RETURN_NOTIMPLEMENTED;  // macro que incrementa el conteo y retorna
PyObject* PyObject_GetAttr(PyObject *o, PyObject *attr_name);
PyObject* PyObject_GetAttrString(PyObject *o, const char *attr_name);
int PyObject_SetAttr(PyObject *o, PyObject *attr_name, PyObject *v);
int PyObject_SetAttrString(PyObject *o, const char *attr_name, PyObject *v);
int PyObject_DelAttr(PyObject *o, PyObject *attr_name);
int PyObject_DelAttrString(PyObject *o, const char *attr_name);
PyObject* PyObject_Repr(PyObject *o);
PyObject* PyObject_ASCII(PyObject *o);
PyObject* PyObject_Str(PyObject *o);
PyObject* PyObject_Bytes(PyObject *o);
int PyObject_IsSubclass(PyObject *derived, PyObject *cls);
int PyObject_IsInstance(PyObject *inst, PyObject *cls);
int PyObject_IsTrue(PyObject *o);
int PyObject_Not(PyObject *o);
int PyObject_TypeCheck(PyObject *o, PyTypeObject *type);
Py_ssize_t PyObject_Size(PyObject *o);
Py_ssize_t PyObject_Length(PyObject *o);
PyObject* PyObject_GetItem(PyObject *o, PyObject *key);
int PyObject_SetItem(PyObject *o, PyObject *key, PyObject *v);
int PyObject_DelItem(PyObject *o, PyObject *key);
```

#### *Call protocol*

```c
PyObject* PyObject_Call(PyObject *callable, PyObject *args, PyObject *kwargs);
PyObject* PyObject_CallNoArgs(PyObject *callable);
PyObject* PyObject_CallOneArg(PyObject *callable, PyObject *arg);
PyObject* PyObject_CallObject(PyObject *callable, PyObject *args);
PyObject* PyObject_CallFunction(PyObject *callable, const char *format, ...);
PyObject* PyObject_CallMethod(PyObject *obj, const char *name, const char *format, ...);
PyObject* PyObject_CallFunctionObjArgs(PyObject *callable, ...);
PyObject* PyObject_CallMethodObjArgs(PyObject *obj, PyObject *name, ...);
PyObject* PyObject_CallMethodNoArgs(PyObject *obj, PyObject *name);
PyObject* PyObject_CallMethodOneArg(PyObject *obj, PyObject *name, PyObject *arg);
```

#### *Number protocol*

Existen numerosas funciones para todo tipo de operaciones matemáticas y de conversión.

```c
int PyNumber_Check(PyObject *o);
int PyIndex_Check(PyObject *o);
```
#### *Sequence protocol*

```c
int PySequence_Check(PyObject *o);
Py_ssize_t PySequence_Size(PyObject *o);
Py_ssize_t PySequence_Length(PyObject *o);
PyObject* PySequence_Concat(PyObject *o1, PyObject *o2);
PyObject* PySequence_Repeat(PyObject *o, Py_ssize_t count);
PyObject* PySequence_InPlaceConcat(PyObject *o1, PyObject *o2);
PyObject* PySequence_InPlaceRepeat(PyObject *o, Py_ssize_t count);
PyObject* PySequence_GetItem(PyObject *o, Py_ssize_t i);
PyObject* PySequence_GetSlice(PyObject *o, Py_ssize_t i1, Py_ssize_t i2);
int PySequence_SetItem(PyObject *o, Py_ssize_t i, PyObject *v);
int PySequence_DelItem(PyObject *o, Py_ssize_t i);
int PySequence_SetSlice(PyObject *o, Py_ssize_t i1, Py_ssize_t i2, PyObject *v);
int PySequence_DelSlice(PyObject *o, Py_ssize_t i1, Py_ssize_t i2);
Py_ssize_t PySequence_Count(PyObject *o, PyObject *value);
int PySequence_Contains(PyObject *o, PyObject *value);
Py_ssize_t PySequence_Index(PyObject *o, PyObject *value);
PyObject* PySequence_List(PyObject *o);
PyObject* PySequence_Tuple(PyObject *o);
```

#### *Mapping protocol*

```c
int PyMapping_Check(PyObject *o);
Py_ssize_t PyMapping_Size(PyObject *o);
Py_ssize_t PyMapping_Length(PyObject *o);
PyObject* PyMapping_GetItemString(PyObject *o, const char *key);
int PyMapping_SetItemString(PyObject *o, const char *key, PyObject *v);
int PyMapping_DelItem(PyObject *o, PyObject *key);
int PyMapping_DelItemString(PyObject *o, const char *key);
PyObject* PyMapping_Keys(PyObject *o);
PyObject* PyMapping_Values(PyObject *o);
PyObject* PyMapping_Items(PyObject *o);
```

#### *Fundamental objects*

`PyTypeObject` es una estructura *C* usada para describir tipos *built-in* en *Python*.

`PyType_Type` es un **objeto** *Python* (por tanto, de tipo `PyObject*`). Es un objeto tipo (o clase). En *Python* es un objeto ***type*** (el tipo del objeto base).

```c
int PyType_Check(PyObject *o);
```

Retorna *non-zero* si el objeto es un objeto *type*, incluyendo instancias de tipos derivados. En caso contrario, retorna 0.

> Esta función no comprueba si un objeto es de determinado tipo, sino si el objeto es un tipo en sí, ya sea el tipo base, o cualquier tipo derivado de este.

```c
int PyType_CheckExact(PyObject *o)
```

Esta función es como `PyType_Check()`, pero no da verdadero en subclases (solo el tipo base).

```c
PyObject* Py_None;  // instancia del objeto None
Py_RETURN_NONE;  // macro que incrementa su conteo y retorna None
```

#### Objetos numéricos

Los objetos *Python* son de tipo apuntador a `PyObject`. Este tipo tiene subtipos derivados, como `PyLongObject`, `PyFloatObject`, etc. Por otro lado existen los objetos que describen un tipo. Estos objetos son de tipo `PyTypeObject`, y se usan cuando queremos referirnos a un tipo, y no a un objeto.

Las funciones que retornan un entero (la mayoría de las que empiezan por `PyLong_As`) retornan ***-1*** en caso de error, lo cual puede confundirse precisamente con un valor numérico. Para saber si se ha producido error en ese caso, habrá que consultar `PyErr_Occurred()`.

Las funciones que comprueban que un objeto sea de un tipo determinado, comprueban que el tipo del objeto sea un objeto del tipo adecuado. Por ejemplo, la función `PyLong_Check()` comprueba que el objeto que le pasamos tenga como tipo asociado un objeto `PyLong_Type`. Si el objeto es realmente un entero *Python*, será un objeto que **en *C*** será del tipo `PyObject*` o subtipo `PyLongObject*`, lo cual es una estructura que describe el tipo que tiene el objeto **en *Python***, para lo cual uno de los campos de esta estructura tiene una referencia al tipo *Python* pertinente, en este caso una instancia del objeto `PyLong_Type` (que a su vez es de tipo `PyTypeObject`).

Enteros:

```c
PyLongObject;
PyTypeObject PyLong_Type;
int PyLong_Check(PyObject *p);
int PyLong_CheckExact(PyObject *p);
PyObject* PyLong_FromLong(long v);
PyObject* PyLong_FromUnsignedLong(unsigned long v);
PyObject* PyLong_FromLongLong(long long v);
PyObject* PyLong_FromUnsignedLongLong(unsigned long long v);
PyObject* PyLong_FromDouble(double v);
PyObject* PyLong_FromString(const char *str, char **pend, int base);
PyObject* PyLong_FromUnicodeObject(PyObject *u, int base);
long PyLong_AsLong(PyObject *obj);
long long PyLong_AsLongLong(PyObject *obj);
Py_ssize_t PyLong_AsSsize_t(PyObject *pylong);
unsigned long PyLong_AsUnsignedLong(PyObject *pylong);
size_t PyLong_AsSize_t(PyObject *pylong);
unsigned long long PyLong_AsUnsignedLongLong(PyObject *pylong);
double PyLong_AsDouble(PyObject *pylong);
```

Booleanos (subtipo de entero):

```c
int PyBool_Check(PyObject *o);
PyObject* Py_False;  // objeto falso
PyObject* Py_True;  // objeto verdadero
Py_RETURN_FALSE;  // macro que incrementa conteo y retorna
Py_RETURN_TRUE;  // macro que incrementa conteo y retorna
PyObject* PyBool_FromLong(long v);
```

Punto flotante:

```c
PyFloatObject;
PyTypeObject PyFloat_Type;
int PyFloat_Check(PyObject *p);
int PyFloat_CheckExact(PyObject *p);
PyObject* PyFloat_FromDouble(double v);
double PyFloat_AsDouble(PyObject *pyfloat);
```

Complejos:

```c
typedef struct {
    double real;
    double imag;
} Py_complex;  // estructura que guarda el complejo en C
PyComplexObject;
PyTypeObject PyComplex_Type;
int PyComplex_Check(PyObject *p);
int PyComplex_CheckExact(PyObject *p);
PyObject* PyComplex_FromCComplex(Py_complex v);
PyObject* PyComplex_FromDoubles(double real, double imag);
double PyComplex_RealAsDouble(PyObject *op);
double PyComplex_ImagAsDouble(PyObject *op);
Py_complex PyComplex_AsCComplex(PyObject *op);
```

#### Secuencias

Bytes:

```c
PyBytesObject;
PyTypeObject PyBytes_Type;
int PyBytes_Check(PyObject *o);
int PyBytes_CheckExact(PyObject *o);
PyObject* PyBytes_FromString(const char *v);
PyObject* PyBytes_FromStringAndSize(const char *v, Py_ssize_t len);
PyObject* PyBytes_FromObject(PyObject *o);
Py_ssize_t PyBytes_Size(PyObject *o);
char* PyBytes_AsString(PyObject *o);
int PyBytes_AsStringAndSize(PyObject *obj, char **buffer, Py_ssize_t *length);
void PyBytes_Concat(PyObject **bytes, PyObject *newpart);
void PyBytes_ConcatAndDel(PyObject **bytes, PyObject *newpart);
```

Bytearrays:

```c
PyByteArrayObject;
PyTypeObject PyByteArray_Type;
int PyByteArray_Check(PyObject *o);
int PyByteArray_CheckExact(PyObject *o);
PyObject* PyByteArray_FromObject(PyObject *o);
PyObject* PyByteArray_FromStringAndSize(const char *string, Py_ssize_t len);
PyObject* PyByteArray_Concat(PyObject *a, PyObject *b);
Py_ssize_t PyByteArray_Size(PyObject *bytearray);
char* PyByteArray_AsString(PyObject *bytearray);
```

En cuanto a los objetos *Unicode* (*strings Python*), existen los tipos `Py_UCS4` (entero de 32 bits), `Py_UCS2` (16 bits), `Py_UCS1` (8 bits) y `Py_UNICODE` (`wchar_t`).

Las funciones de este *API* que trabajan con objetos *Unicode* usan el tipo `PyObject*`, aunque internamente usan los subtipos (dependiendo de los bits por carácter) `PyASCIIObject`, `PyCompactUnicodeObject` y `PyUnicodeObject`.

Es posible escribir en un *string Python*, siempre y cuando solo exista una referencia al objeto (la que tenemos nosotros), ya que son inmutables.

```c
PyTypeObject PyUnicode_Type;
int PyUnicode_Check(PyObject *o);
int PyUnicode_CheckExact(PyObject *o);
Py_UCS1* PyUnicode_1BYTE_DATA(PyObject *o);
Py_UCS2* PyUnicode_2BYTE_DATA(PyObject *o);
Py_UCS4* PyUnicode_4BYTE_DATA(PyObject *o);
int PyUnicode_KIND(PyObject *o);  // indica cuántos bits usa el string. Posibles valores de retorno:
// PyUnicode_WCHAR_KIND, PyUnicode_1BYTE_KIND, PyUnicode_2BYTE_KIND o PyUnicode_4BYTE_KIND
void* PyUnicode_DATA(PyObject *o);
void PyUnicode_WRITE(int kind, void *data, Py_ssize_t index, Py_UCS4 value);
Py_UCS4 PyUnicode_READ(int kind, void *data, Py_ssize_t index);
int PyUnicode_IsIdentifier(PyObject *o);
int Py_UNICODE_ISSPACE(Py_UNICODE ch);
int Py_UNICODE_ISLOWER(Py_UNICODE ch);
int Py_UNICODE_ISUPPER(Py_UNICODE ch);
int Py_UNICODE_ISTITLE(Py_UNICODE ch);
int Py_UNICODE_ISLINEBREAK(Py_UNICODE ch);
int Py_UNICODE_ISDECIMAL(Py_UNICODE ch);
int Py_UNICODE_ISDIGIT(Py_UNICODE ch);
int Py_UNICODE_ISNUMERIC(Py_UNICODE ch);
int Py_UNICODE_ISALPHA(Py_UNICODE ch);
int Py_UNICODE_ISALNUM(Py_UNICODE ch);
int Py_UNICODE_ISPRINTABLE(Py_UNICODE ch);
int Py_UNICODE_TODECIMAL(Py_UNICODE ch);
int Py_UNICODE_TODIGIT(Py_UNICODE ch);
double Py_UNICODE_TONUMERIC(Py_UNICODE ch);
Py_UNICODE_IS_SURROGATE(ch);  // en UTF-16; 0xD800 <= ch <= 0xDFFF
Py_UNICODE_IS_HIGH_SURROGATE(ch);  // el primero del par
Py_UNICODE_IS_LOW_SURROGATE(ch);  // el segundo del par
Py_UNICODE_JOIN_SURROGATES(high, low);  // retorna carácter 32 bits
PyObject* PyUnicode_New(Py_ssize_t size, Py_UCS4 maxchar);  // maxchar suele ser uno de 127, 255, 65535 o 1114111
PyObject* PyUnicode_FromKindAndData(int kind, const void *buffer, Py_ssize_t size);
PyObject* PyUnicode_FromStringAndSize(const char *u, Py_ssize_t size);
PyObject *PyUnicode_FromString(const char *u);
Py_ssize_t PyUnicode_GetLength(PyObject *unicode);
Py_ssize_t PyUnicode_CopyCharacters(PyObject *to, Py_ssize_t to_start, PyObject *from, Py_ssize_t from_start, Py_ssize_t how_many);
Py_ssize_t PyUnicode_Fill(PyObject *unicode, Py_ssize_t start, Py_ssize_t length, Py_UCS4 fill_char);
int PyUnicode_WriteChar(PyObject *unicode, Py_ssize_t index, Py_UCS4 character);
Py_UCS4 PyUnicode_ReadChar(PyObject *unicode, Py_ssize_t index);
PyObject* PyUnicode_Substring(PyObject *str, Py_ssize_t start, Py_ssize_t end);
Py_UCS4* PyUnicode_AsUCS4(PyObject *u, Py_UCS4 *buffer, Py_ssize_t buflen, int copy_null);
Py_UCS4* PyUnicode_AsUCS4Copy(PyObject *u);
PyObject* PyUnicode_FromWideChar(const wchar_t *w, Py_ssize_t size);
Py_ssize_t PyUnicode_AsWideChar(PyObject *unicode, wchar_t *w, Py_ssize_t size);
wchar_t* PyUnicode_AsWideCharString(PyObject *unicode, Py_ssize_t *size);
PyObject* PyUnicode_Decode(const char *s, Py_ssize_t size, const char *encoding, const char *errors);
PyObject* PyUnicode_AsEncodedString(PyObject *unicode, const char *encoding, const char *errors);
PyObject* PyUnicode_Concat(PyObject *left, PyObject *right);
PyObject* PyUnicode_Split(PyObject *s, PyObject *sep, Py_ssize_t maxsplit);
PyObject* PyUnicode_Splitlines(PyObject *s, int keepend);
PyObject* PyUnicode_Join(PyObject *separator, PyObject *seq);
Py_ssize_t PyUnicode_Find(PyObject *str, PyObject *substr, Py_ssize_t start, Py_ssize_t end, int direction);
Py_ssize_t PyUnicode_FindChar(PyObject *str, Py_UCS4 ch, Py_ssize_t start, Py_ssize_t end, int direction);
Py_ssize_t PyUnicode_Count(PyObject *str, PyObject *substr, Py_ssize_t start, Py_ssize_t end);
PyObject* PyUnicode_Replace(PyObject *str, PyObject *substr, PyObject *replstr, Py_ssize_t maxcount);
int PyUnicode_Compare(PyObject *left, PyObject *right);
const char* PyUnicode_AsUTF8AndSize(PyObject *unicode, Py_ssize_t *size);
const char* PyUnicode_AsUTF8(PyObject *unicode);
```

Tuplas:

```c
PyTupleObject;
PyTypeObject PyTuple_Type;
int PyTuple_Check(PyObject *p);
int PyTuple_CheckExact(PyObject *p);
PyObject* PyTuple_New(Py_ssize_t len);
PyObject* PyTuple_Pack(Py_ssize_t n, ...);
Py_ssize_t PyTuple_Size(PyObject *p);
PyObject* PyTuple_GetItem(PyObject *p, Py_ssize_t pos);
PyObject* PyTuple_GetSlice(PyObject *p, Py_ssize_t low, Py_ssize_t high);
int PyTuple_SetItem(PyObject *p, Py_ssize_t pos, PyObject *o);  // solo si no hay referencias fuera de la nuestra (tuplas son inmutables)
int _PyTuple_Resize(PyObject **p, Py_ssize_t newsize);  // idem
```

Listas:

```c
PyListObject;
PyTypeObject PyList_Type;
int PyList_Check(PyObject *p);
int PyList_CheckExact(PyObject *p);
PyObject* PyList_New(Py_ssize_t len);
Py_ssize_t PyList_Size(PyObject *list);
PyObject* PyList_GetItem(PyObject *list, Py_ssize_t index);
int PyList_SetItem(PyObject *list, Py_ssize_t index, PyObject *item);
int PyList_Insert(PyObject *list, Py_ssize_t index, PyObject *item);
int PyList_Append(PyObject *list, PyObject *item);
PyObject* PyList_GetSlice(PyObject *list, Py_ssize_t low, Py_ssize_t high);
int _PyList_Resize(PyObject **p, Py_ssize_t newsize);
int PyList_SetSlice(PyObject *list, Py_ssize_t low, Py_ssize_t high, PyObject *itemlist);
int PyList_Sort(PyObject *list);
int PyList_Reverse(PyObject *list);
PyObject* PyList_AsTuple(PyObject *list);
```

#### Contenedores

Diccionarios:

```c
PyDictObject;
PyTypeObject PyDict_Type;
int PyDict_Check(PyObject *p);
int PyDict_CheckExact(PyObject *p);
PyObject* PyDict_New();
void PyDict_Clear(PyObject *p);
int PyDict_Contains(PyObject *p, PyObject *key);
PyObject* PyDict_Copy(PyObject *p);
int PyDict_SetItem(PyObject *p, PyObject *key, PyObject *val);
int PyDict_SetItemString(PyObject *p, const char *key, PyObject *val);
int PyDict_DelItem(PyObject *p, PyObject *key);
int PyDict_DelItemString(PyObject *p, const char *key);
PyObject* PyDict_GetItem(PyObject *p, PyObject *key);
PyObject* PyDict_GetItemString(PyObject *p, const char *key);
PyObject* PyDict_Items(PyObject *p);
PyObject* PyDict_Keys(PyObject *p);
PyObject* PyDict_Values(PyObject *p);
Py_ssize_t PyDict_Size(PyObject *p);
```

Sets:

```c
PySetObject;
PyTypeObject PySet_Type;
PyTypeObject PyFrozenSet_Type;
int PySet_Check(PyObject *p);
int PyFrozenSet_Check(PyObject *p);
int PyAnySet_Check(PyObject *p);
int PyAnySet_CheckExact(PyObject *p);
int PyFrozenSet_CheckExact(PyObject *p);
PyObject* PySet_New(PyObject *iterable);
PyObject* PyFrozenSet_New(PyObject *iterable);
Py_ssize_t PySet_Size(PyObject *anyset);
int PySet_Contains(PyObject *anyset, PyObject *key);
int PySet_Add(PyObject *set, PyObject *key);
int PySet_Discard(PyObject *set, PyObject *key);
PyObject* PySet_Pop(PyObject *set);
int PySet_Clear(PyObject *set);
```

#### Objetos función y método

Funciones:

```c
PyFunctionObject;
PyTypeObject PyFunction_Type;
int PyFunction_Check(PyObject *o);
PyObject* PyFunction_New(PyObject *code, PyObject *globals);
```

Métodos (*bound functions*):

```c
PyTypeObject PyMethod_Type;
int PyMethod_Check(PyObject *o);
PyObject* PyMethod_New(PyObject *func, PyObject *self);
PyObject* PyMethod_Function(PyObject *meth);
PyObject* PyMethod_Self(PyObject *meth);
```

#### Inicialización de módulos (*single-phase*)

```c
PyObject* PyModule_Create(PyModuleDef *def);
```

#### Soporte para la creación de módulos

```c
int PyModule_AddObject(PyObject *module, const char *name, PyObject *value);
int PyModule_AddIntConstant(PyObject *module, const char *name, long value);
int PyModule_AddStringConstant(PyObject *module, const char *name, const char *value);
int PyModule_AddIntMacro(PyObject *module, macro);
int PyModule_AddStringMacro(PyObject *module, macro);
```

#### Soporte para la creación de objetos

```c
Py_TYPE(o);
int Py_IS_TYPE(PyObject *o, PyTypeObject *type);
void Py_SET_TYPE(PyObject *o, PyTypeObject *type);
Py_REFCNT(o);
void Py_SET_REFCNT(PyObject *o, Py_ssize_t refcnt);
```
