<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.
Primera opción:
-La función devuelve 0 si todo bien y -1 si hay error.
-El resultado se devuelve por un puntero (parámetro de salida).
#include <stdio.h>
#include <math.h>  
#include <stdbool.h>

int raiz(double x, double *out_result) {
    if (x < 0.0 || out_result == NULL) {
        return -1; // error
    }
    *out_result = sqrt(x);
    return 0; // éxito
}

int main(void) {
    double r;
    if (raiz(9.0, &r) == 0) {
        printf("√9 = %f\n", r);
    } else {
        printf("Error: argumento negativo o puntero nulo\n");
    }

    if (raiz(-4.0, &r) != 0) {
        printf("Error detectado en main: no se puede raíz de negativo\n");
    }
    return 0;
}

Segunda opción:
-La función devuelve un único valor (struct) que incluye estado y resultado.
-Ventaja: mantiene juntos el dato y su validez.
#include <stdio.h>
#include <math.h>

typedef struct {
    int status;     // 0 = bien, -1 = error
    double value;   // válido solo si status es 0
} ResultadoRaiz;

ResultadoRaiz raiz(double x) {
    if (x < 0.0) {
        return (ResultadoRaiz){ .status = -1, .value = 0.0 };
    }
    return (ResultadoRaiz){ .status = 0, .value = sqrt(x) };
}

int main(void) {
    ResultadoRaiz r1 = raiz(16.0);
    if (r1.status == 0) {
        printf("√16 = %f\n", r1.value);
    } else {
        printf("Error: argumento negativo\n");
    }

    ResultadoRaiz r2 = raiz(-1.0);
    if (r2.status != 0) {
        printf("Error detectado en main: no se puede raíz de negativo\n");
    }
    return 0;
}

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una excepción es un evento (un error o condición excepcional) que interrumpe el flujo normal de ejecución y se señala mediante un mecanismo del lenguaje para que otro contexto (el llamador) decida cómo gestionarlo.
Un programador lanza (throw) una excepción cuando detecta una condición inválida (p. ej., “raíz de negativo”).
Quien llama controla (catch) la excepción donde tenga sentido reaccionar (mostrar mensaje, reintentar, degradar funcionalidad, registrar, etc.).
Objetivo: separar la lógica normal de la lógica de error, evitar códigos de retorno repetitivos y permitir la propagación automática hasta un manejador adecuado.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

public class Calculadora {

    // Lanza IllegalArgumentException si x < 0
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException("No se puede calcular la raíz de un número negativo: " + x);
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        try {
            double a = Calculadora.raiz(9.0);
            System.out.println("√9 = " + a);

            double b = Calculadora.raiz(-4.0); // provoca la excepción
            System.out.println("√-4 = " + b);  // no se ejecuta
        } catch (IllegalArgumentException e) {
            // Control desde fuera del método raiz
            System.err.println("Error al calcular raíz: " + e.getMessage());
        } finally {
            // Este bloque se ejecuta siempre 
            System.out.println("Fin del cálculo");
        }
    }
}


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

Lanzar una excepción: ejecutar throw para señalar un error. Ej.: throw new IllegalArgumentException(...).
Capturar/Controlar una excepción: envolver código en try y reaccionar en catch.
Propagar una excepción: si un método no la captura, la excepción sube automáticamente a su llamador; este proceso continúa a través de la pila hasta encontrar un catch compatible o hasta terminar el programa (lo que causaría un error no capturado).

Pila de llamadas (stack unwinding):
Al propagarse la excepción, la pila se desenrolla: los frames de las funciones activas se van descartando, ejecutando sus finally (y autocloseables con try-with-resources).
Los métodos que no capturan la excepción NO se reanudan después; su ejecución termina en el punto donde ocurrió la excepción.
Con el ejemplo anterior:
public static double raiz(double x) {
    if (x < 0) {
        throw new IllegalArgumentException("Negativo: " + x); // lanzar
    }
    return Math.sqrt(x);
}

public static void main(String[] args) {
    try {
        double r = raiz(-4); // aquí se lanzará
        System.out.println(r); // no se ejecuta
    } catch (IllegalArgumentException e) { // capturar
        System.err.println("Control desde main: " + e.getMessage());
    }
    // main continúa normal tras el catch
}

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

1-Separación clara de responsabilidades
Lógica normal en el flujo principal; lógica de error en catch.
En C sueles intercalar if (status != OK) return status; en muchos puntos, ensuciando el flujo.

2-Propagación automática
En Java no hay que encadenar manualmente códigos de retorno; la excepción sube sola hasta un manejador adecuado.
En C debes propagar a mano cada error (propenso a olvidos).

3-Desenrollado seguro de la pila (stack unwinding)
finally (y try-with-resources) garantizan limpieza incluso en error.
En C necesitas goto cleanup o disciplina estricta para liberar recursos en cada camino de error.

4-Expresividad y tipo
Excepciones con tipos específicos y mensajes más informativos.
En C, códigos numéricos o errno son menos expresivos y más fáciles de usar mal.

5-Mantenibilidad y composición
APIs más limpias: firmas de funciones sin códigos de retorno mezclados con valores.
Componer llamadas en C incrementa el boilerplate de verificación tras cada llamada.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

Sí, en la mayoría de lenguajes orientados a objetos (Java, C#, Python…), las excepciones son objetos, instancias de clases que heredan de alguna clase base de excepción. Ventajas en términos de encapsulación: como son objetos, una excepción puede encapsular información relevante sobre el error, por ejemplo:
un mensaje explicativo (getMessage()), el tipo del problema (clase concreta), la causa original (getCause()), el estado en el que ocurrió o datos adicionales que el programador quiera incluir. Esto permite que el capturador (catch) pueda tomar decisiones inteligentes basadas en esa información.
¿Podemos crear excepciones personalizadas?
Sí, en Java basta con crear una clase que herede de Exception o RuntimeException:
public class RaizNegativaException extends IllegalArgumentException {
    public RaizNegativaException(double x) {
        super("No se puede calcular la raíz de " + x);
    }
}

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

Una excepción en Java siempre trae información muy útil:

-Mensaje descriptivo (getMessage()): explica el problema exacto.
-Tipo de la excepción: permite distinguir entre errores distintos (p. ej., IOException vs IllegalArgumentException).
-La pila de llamadas (stack trace): es una reconstrucción automática de dónde ocurrió el error y qué funciones había llamadas antes. Imprescindible para depurar.
-Causa original (getCause()): si una excepción fue causada por otra, Java permite encadenarlas.

En C no existe nada de esto automáticamente, tienes que manejar mensajes, códigos de error y stack manualmente.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

Sí, en Java se pueden tener varios bloques catch:
try {
    ...
} catch (IOException e) {
    ...
} catch (NumberFormatException e) {
    ...
}
Solo se ejetuca uno de los catch, el primero cuyo tipo coincida con la excepción lanzada. Después de ejecutarse, se salta directamente fuera del bloque try-catch (o se ejecuta finally si existe).

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

Para asegurarnos de eso usamos el bloque finally, que va después del último catch y se ejetuca siempre, tanto si ocurre una excepción como si no. Ejemplo con catch:
try {
    abrirRecurso();
    usarRecurso();
} catch (Exception e) {
    System.err.println("Error: " + e.getMessage());
} finally {
    cerrarRecurso();   // se ejecuta siempre
}
Ejemplo sin catch:
try {
    abrirRecurso();
    usarRecurso();  // si hay excepción, se salta directo al finally
} finally {
    cerrarRecurso(); // siempre se ejecuta
}

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Si, lo podemos ver en el ejercicio 9. Se ejecuta siempre, haya o no haya excepción. Return en el try:
try {
    return 5;
} finally {
    System.out.println("Esto siempre se ejecuta");
}
Salida: Esto siempre se ejecuta. El return espera a que el finally termine y luego vuelve al llamador.

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

Las excepciones controladas son excepciones que el compilador obliga a gestionar de alguna forma:
Capturándolas con try/catch, o propagándolas con throws. Suponen errores recuperables, dependientes del entorno externo: ficheros, red, recursos.
Las no controladas son excepciones que el compilador no obliga a capturar ni declarar con throws. Representan errores de programación, lógicos o contractuales (fallos del programador).
RuntimeException es la clase base de todas las excepciones no controladas.
-No es obligatorio capturarlas, el programador usa esta categoría para errores no recuperables por el cliente.
-Rompen invariantes o precondiciones.
-Se suelen usar cuando el llamador no tiene forma sensata de reaccionar más que corregir el código.
Ejemplos de excepciones controladas (checked), podemos lanzar estas desde métodos y el compilador obligará a gestionarlas:
-IOException : error al leer/escribir ficheros.
-FileNotFoundException : un fichero no existe.
-SQLException : error al acceder a una BD.
-ClassNotFoundException : una clase no está disponible.

Ejemplos de excepciones no controladas (unchecked):
-NullPointerException
-IllegalArgumentException
-ArrayIndexOutOfBoundsException
-ArithmeticException (ej.: división entre cero)

Situaciones donde se suele preferir una excepción controlada (checked):
-Errores externos y recuperables. Ej.: problemas de red, ficheros que no se pueden abrir.
-El llamador puede reaccionar de forma útil. Ej.: pedir al usuario otro fichero, reintentar la conexión.
-Operaciones que dependen del entorno. BD, sockets, ficheros, servicios.
-Cuando quieres obligar al programador a gestionar el error. Es un mecanismo de diseño.

Situaciones donde se suele preferir una excepción no controlada (unchecked):
-Errores de programación. Ej.: pasar argumentos inválidos.
-Violación de precondiciones o invariantes. Ej.: raíz con valor negativo, listas con índice fuera de rango.
-Estado imposible o inconsistente. Ej.: lógica malformed o branches imposibles.
-El llamador no puede recuperarse de un modo razonable. No tiene sentido obligarle a capturarla.

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

Es una parte de la firma de un método que indica: Este método puede lanzar esta excepción. Si la lanzara, otro debe hacerse cargo de capturarla. Se usa para propagar una excepción checked hacia arriba, para informar al llamador de que debe gestionarla y evita capturarla en el mismo método si no es el lugar adecuado. Es una alternativa capturar una excepción controlada porque en Java las excepciones checked deben: o bien capturarse con try/catch, o bien declararse con throws.Si no haces una de las dos, el código no compila. Capturar:
try {
    cargarArchivo("datos.txt");
} catch (IOException e) {
    System.out.println("No se pudo cargar el archivo");
}
Propagar:
public static void main(String[] args) throws IOException {
    cargarArchivo("datos.txt");  // main las propaga
}

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

public void abrirFichero(String ruta) throws FileNotFoundException {
    FileInputStream fis = null;
    try {
        fis = new FileInputStream(ruta); // Puede lanzar FileNotFoundException
    } finally {
        // El finally siempre se ejecuta
        if (fis != null) {
            try {
                fis.close();
            } catch (IOException e) {
                // Manejo opcional del cierre
            }
        }
    }
}


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

Sí, podemos poner excepciones no controladas en throws, pero no es obligatorio. Las excepciones no controladas (unchecked) no requieren try-catch ni declaración en throws. No debe poner try-catch. Incluso aunque se declare en throws, el llamador no está obligado. 
¿Qué sentido tiene declararlas?
Principalmente documentar la intención del método:
Indicar que puede lanzar un error de programación (IllegalStateException, IllegalArgumentException, etc.), mejorar la claridad de la API... Pero no cambia su comportamiento.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

-Excepciones controladas (checked)
Se usan cuando el error es esperable y forma parte del flujo normal del programa.
El programador puede y debe recuperarse.

Ejemplos:
-IOException
-SQLException
Son típicas en Java.

-Excepciones no controladas (unchecked)
Se usan cuando el error representa fallos de programación, no situaciones recuperables.
El programador no debería continuar sin corregir el código.

Ejemplos:
NullPointerException
IllegalArgumentException

En Java existen tanto las excepciones controladas como las no controladas, en cambio, en lenguajes como C++, C#, Python o JavaScript solo existen las no controladas. En caso de solo existir una las más habituales son las no controladas, es el enfoque moderno y más común porque evita la verbosidad de las controladas.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Lanzar otra excepción dentro del catch tiene sentido cuando queremos convertir una excepción de bajo nivel en una de alto nivel más significativa para el programa.
try {
    leerBD();
} catch (SQLException e) {
    throw new MiExcepcionAplicacion("Error accediendo a la base de datos", e);
}

Sí se puede relanzar la misma excepción capturada:
try {
    metodoPeligroso();
} catch (IOException e) {
    System.err.println("Log: ocurrió un error, lo relanzo");
    throw e; // se relanza la MISMA excepción
}
Tendría sentido hacer esto cuando queremos registrar (log) la excepción pero dejar que otro nivel la maneje o cuando queremos añadir contexto (aunque en ese caso es más típico encapsularla).

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

Cuando una excepción A causa una excepción B, decimos que B tiene como causa a A. En Java esto se hace pasando la excepción original como segundo parámetro:
try {
    FileInputStream fis = new FileInputStream("datos.txt");
} catch (FileNotFoundException e) {
    throw new MiExcepcionDeNegocio("No se pudo cargar el archivo requerido", e);
}
-FileNotFoundException es la excepción causante.
-MiExcepcionDeNegocio es la excepción envolvente de nivel superior.

¿Se ve la causa en pantalla?
Sí. Al imprimir el stack trace aparece la excepción, es decir, Java muestra automáticamente la causa.
