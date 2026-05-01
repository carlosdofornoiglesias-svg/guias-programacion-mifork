<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

C con void:
#include <stdio.h>

typedef struct {
    void* datos[10];
    int tamaño;
} Vector;

void añadir(Vector* v, void* elemento) {
    v->datos[v->tamaño++] = elemento;
}

int main() {
    Vector v = { .tamaño = 0 };

    int x = 5;
    double y = 3.14;

    añadir(&v, &x);
    añadir(&v, &y);

    printf("%d\n", *(int*)v.datos[0]);
    printf("%f\n", *(double*)v.datos[1]);

    return 0;
}

Java con object:
class Vector {
    private Object[] datos = new Object[10];
    private int size = 0;

    public void add(Object o) {
        datos[size++] = o;
    }

    public Object get(int i) {
        return datos[i];
    }
}

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

La programación genérica consiste en escribir código independiente del tipo concreto de los datos, de forma que pueda reutilizarse con múltiples tipos manteniendo seguridad y claridad.
Los ejemplos anteriores son un antecedente o forma rudimentaria de programación genérica, pero no son programación genérica real, porque:

-No hay verificación de tipos en tiempo de compilación.
-Los errores de tipo aparecen en ejecución.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

-Pérdida de chequeo de tipos en compilación
-Necesidad de conversiones explícitas (casting)
-Posibles errores en tiempo de ejecución (ClassCastException en Java)
-Código menos legible y más propenso a fallos
-El compilador no puede ayudar a detectar usos incorrectos

Error típico:
Integer x = (Integer) vector.get(0); // puede fallar

## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

Los parámetros de tipo son identificadores (como T, E, K, V) que representan tipos aún no concretados y que se especifican al instanciar una clase, interfaz o método.

## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

Java(generics):
import java.util.ArrayList;

ArrayList<String> lista = new ArrayList<>();
lista.add("Hola");
lista.add("Mundo");

for (String s : lista) {
    System.out.println(s.toUpperCase());
}

C(templates):
#include <vector>
#include <string>
#include <iostream>

int main() {
    std::vector<std::string> v;
    v.push_back("Hola");
    v.push_back("Mundo");

    for (const std::string& s : v) {
        std::cout << s << std::endl;
    }
}

## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

¿Qué hace el compilador?
Cuando se instancia una clase genérica Java y C++ NO hacen lo mismo

Java: type erasure
-El compilador elimina la información de tipos genéricos
-List<String> y List<Integer> son la misma clase en tiempo de ejecución
-Los genéricos existen solo en compilación
-El bytecode usa Object y conversiones automáticas

C++: instanciación de plantillas
-Para cada tipo concreto, el compilador genera una clase distinta
-vector<string> y vector<int> son tipos completamente diferentes
-La información de tipo se mantiene en tiempo de compilación y ejecución
-No hay sobrecoste ni castings

## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

public class Par<A, B> {

    private A primero;
    private B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() {
        return primero;
    }

    public B getSegundo() {
        return segundo;
    }
}

Ejemplo de uso:
public class Estadistica {

    public static Par<Double, Double> mediaYDesviacion(double[] datos) {
        double suma = 0.0;
        for (double d : datos) {
            suma += d;
        }
        double media = suma / datos.length;

        double sumaCuadrados = 0.0;
        for (double d : datos) {
            sumaCuadrados += Math.pow(d - media, 2);
        }
        double desviacion = Math.sqrt(sumaCuadrados / datos.length);

        return new Par<>(media, desviacion);
    }
}

## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

Versión no genérica(con object):
public static Object seleccionaUno(Object a, Object b) {
    return Math.random() < 0.5 ? a : b;
}

Versión genérica:
public static <T> T seleccionaUno(T a, T b) {
    return Math.random() < 0.5 ? a : b;
}

## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

Solución sin genéricos:
public class Punto {

    private Number x;
    private Number y;

    public Punto(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() {
        return x;
    }

    public Number getY() {
        return y;
    }

    public double calcularDistanciaA(Punto otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

Solución con genéricos:
public class Punto<T extends Number> {

    private T x;
    private T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() {
        return x;
    }

    public T getY() {
        return y;
    }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
Uso:
Punto<Integer> p1 = new Punto<>(3, 4);
Punto<Integer> p2 = new Punto<>(6, 8);

Tras la compilación, en ambos casos, el tipo efectivo es Punto y los campos se tratan como Number.

## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

Sin genéricos si pero con genéricos no.

Tipo devuelto por getX():
-Sin genéricos: devuelve Number
-Con genéricos: devuelve T (por ejemplo, Integer o Double)

## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

El problema del diseño original es que el método distanciaA(Punto p) es demasiado general, por lo que permite pasar cualquier implementación de Punto, lo que obliga a usar instanceof y casting.
La solución es usar un parámetro de tipo recursivo (F-bounded polymorphism), para indicar que un punto solo puede calcular distancia con puntos de su mismo tipo concreto. Punto<T>:

public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}
La parte de extends Punto<T> significa que T es un tipo que implementa Punto de sí mismo.

Punto2D:
public class Punto2D implements Punto<Punto2D> {

    private final double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

Punto3D:
public class Punto3D implements Punto<Punto3D> {

    private final double x, y, z;

    public Punto3D    }    public Punto3D(double x, double y, double z) {

    @Override
    public double distanciaA(Punto3D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
        this.x = x;
        this.y = y;
        this.z = z;


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

No, aunque String ⊂ Object, en Java: List<String> ⊄ List<Object>, los genéricos en Java son invariantes.
String[] si que es un subtipo de Object<>[]. Los arrays en Java son covariantes:
                                                                String[] sa = new String[10];
                                                                Object[] oa = sa; // permitido
Pero esto introduce un problema en tiempo de ejecución: Javaoa[0] = 10; // ArrayStoreException en runtime
El compilador no lo detecta; Java añade una comprobación dinámica.

Covariante:
Si A ⊂ B, entonces G<A> ⊂ G<B>
Ejemplo: arrays en Java

Contravariante:
Si A ⊂ B, entonces G<B> ⊂ G<A>
Se usa para consumidores de datos

Invariante:
No hay relación de subtipado
Ejemplo: genéricos en Java (List<T>)


Java elige invariancia por defecto para garantizar seguridad en compilación.

## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

Un wildcard (?) es un marcador que representa un tipo desconocido, permitiendo expresar relaciones de variancia de forma segura.
? extends T (covariante):
Se usa para leer, garantiza que el tipo es T o un subtipo y no permite añadir elementos (salvo null).
Ejemplo(suma de números):
public static double suma(List<? extends Number> lista) {
    double total = 0.0;
    for (Number n : lista) {
        total += n.doubleValue();
    }
    return total;
}

? super T (contravariante):
Se usa para escribir, garantiza que la lista acepta T o subtipos y al leer, solo se obtiene Object

Ejemplo(añadir enteros):
public static void añadirEnteros(List<? super Integer> lista) {
    lista.add(1);
    lista.add(2);
    lista.add(3);
}
Permite:
List<Integer>
List<Number>
List<Object>


