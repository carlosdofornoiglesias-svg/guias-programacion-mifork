<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

Polimorfismo es la capacidad de una referencia de un tipo base (clase padre o interfaz) para apuntar a objetos de distintas clases derivadas y que, al invocar un método, se ejecute la versión correspondiente al tipo real del objeto, no al tipo de la referencia.
Sirve para escribir código más flexible, reutilizable y extensible, desacoplando el uso de los objetos de su implementación concreta.
La sobreescritura (overriding) de métodos consiste en que una clase hija proporciona su propia implementación de un método heredado de la clase base, manteniendo la misma firma. Es el mecanismo fundamental que permite el polimorfismo en tiempo de ejecución.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

La ligadura dinámica (o enlace tardío) consiste en que la decisión de qué método se ejecuta se toma en tiempo de ejecución, según el tipo real del objeto, y no en tiempo de compilación.
Está directamente relacionada con el polimorfismo, ya que es el mecanismo que permite que una llamada a un método sobre una referencia base ejecute distintas implementaciones.
C++:
Por defecto, los métodos no usan ligadura dinámica.
Es necesario declarar el método como virtual en la clase base.
Se recomienda usar override en la clase derivada para evitar errores.

Java:
La ligadura dinámica es el comportamiento por defecto para los métodos de instancia.
No hay que indicar nada explícitamente.
Solo quedan fuera los métodos static, final y private.

Python:
Todo el despacho de métodos es dinámico.
No existe distinción entre métodos virtuales y no virtuales.
El polimorfismo es implícito y se basa en el principio de duck typing (“si se comporta como un pato, es un pato”).

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

class Soldado {
    public void saludar() {
        System.out.println("Soldado saluda.");
    }
}

class Zapador extends Soldado {
    @Override
    public void saludar() {
        System.out.println("Zapador saluda.");
    }
}

class Artillero extends Soldado {
    @Override
    public void saludar() {
        System.out.println("Artillero saluda.");
    }
}

public class PruebaPolimorfismo {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[2];
        ejercito[0] = new Zapador();
        ejercito[1] = new Artillero();

        for (Soldado s : ejercito) {
            s.saludar(); // Se ejecuta la versión concreta
        }
    }
}


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

Sí, al sobreescribir un método se puede invocar el método de la clase base usando la palabra clave super.
class Zapador extends Soldado {
    @Override
    public void saludar() {
        super.saludar(); // Llama al método de Soldado
        System.out.println("ZAPADOR A SUS ORDENES");
    }
}

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

Restricciones al sobreescribir:

-El nombre y los parámetros deben ser idénticos.
-El tipo de retorno debe ser el mismo o covariante (una subclase del retorno original).
-No se puede reducir la visibilidad (public → protected no es válido).
-No se pueden lanzar excepciones más generales que en el método base.

Sobreescritura (overriding):
-Ocurre entre clases relacionadas por herencia.
-Se resuelve en tiempo de ejecución.
-Permite comportamiento polimórfico.

Sobrecarga (overloading):
-Métodos con el mismo nombre pero distintos parámetros.
-Puede existir en la misma clase.
-Se resuelve en tiempo de compilación.
-No implica polimorfismo dinámico.

@Override:
-Indica explícitamente que un método pretende sobreescribir otro.
-El compilador avisa si hay errores en la firma.
-Mejora la legibilidad y evita fallos sutiles.
-Es altamente recomendable usarla siempre.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

Sí. Desde los primeros temas de Java se utiliza polimorfismo, muchas veces sin ser plenamente conscientes de ello.
Por ejemplo, al sobreescribir toString(), al sobreescribir equals(Object o) o al trabajar con referencias de tipo Object

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

Una clase abstracta es una clase que no puede instanciarse y que está pensada para ser heredada. Sirve como plantilla común para sus subclases.
Un método abstracto es un método que no tiene implementación (no tiene cuerpo) y obliga a las clases hijas a implementarlo.
No se pueden crear instancias de una clase abstracta.
En Java, la palabra clave abstract se coloca:

En la declaración de la clase, si tiene al menos un método abstracto, en la declaración del método, cuando no se implementa.
Ejemplo:
abstract class Soldado {
    public void saludar() {
        System.out.println("Soldado saluda.");
    }

    public abstract void atacar();
}

class Zapador extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Zapador coloca explosivos.");
    }
}

class Artillero extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Artillero dispara el cañón.");
    }
}

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

Una clase final no puede ser heredada y un método final no puede ser sobreescrito.

Relación con el polimorfismo:
-Un método final rompe el polimorfismo, ya que impide que las subclasses cambien su comportamiento.
-Una clase final impide directamente cualquier comportamiento polimórfico por herencia.

Ejemplo en la API de Java:
-String es una clase final.
-Esto garantiza inmutabilidad y seguridad, evitando que alguien modifique su comportamiento mediante herencia.

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

Una interfaz define un contrato: especifica qué métodos debe tener una clase, pero no cómo se implementan (salvo métodos default o static).
Similitudes con clases abstractas:
-Ambas pueden definir métodos sin implementación.
-Ambas permiten polimorfismo.

Diferencias:
-Una clase solo puede heredar de una clase abstracta.
-Una clase puede implementar múltiples interfaces.
-Las interfaces no tienen estado (no atributos de instancia tradicionales).

Sí, una clase puede implementar más de una interfaz.

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

abstract class Punto {
    public abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
    double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("Puntos incompatibles");
        }
        Punto2D p = (Punto2D) otro;
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

class Punto3D extends Punto {
    double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("Puntos incompatibles");
        }
        Punto3D p = (Punto3D) otro;
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}

class Linea {
    private Punto a;
    private Punto b;

    public Linea(Punto a, Punto b) {
        this.a = a;
        this.b = b;
    }

    public double longitud() {
        return a.calcularDistanciaA(b);
    }
}

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

La herencia de interfaces consiste en que una interfaz puede extender otra interfaz.
Sí, en Java existe herencia múltiple de interfaces: una interfaz puede extender varias interfaces, y una clase puede implementar múltiples interfaces.
interface Fichero {
    String leer();
}

interface FicheroEscribible extends Fichero {
    void escribir(String contenido);
    void eliminar();
}
Una clase que implemente FicheroEscribible estará obligada a implementar todos los métodos de ambas interfaces.
Este mecanismo permite reutilización de contratos sin los problemas clásicos de la herencia múltiple de clases.