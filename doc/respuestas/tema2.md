<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

Encapsulación: Es el principio por el cual los datos (estado) y las operaciones que trabajan con esos datos (métodos) se agrupan dentro de una misma unidad: la clase. Su objetivo es modelar objetos coherentes y controlados.
Ocultación de información: Es la práctica de restringir el acceso directo al estado interno y a ciertos detalles de implementación, exponiendo solo lo esencial a través de una interfaz pública estable y segura.

Ventajas de la ocultación de información:

-Mantiene el estado del objeto siempre válido (no se “rompe” desde fuera).
-Cambiar la implementación interna no obliga a cambiar a los clientes.
-Facilita corregir errores y mejorar el rendimiento sin romper código externo.
-Evita usos indebidos (por ejemplo, valores invalidos).
-Se puede extender la clase sin efectos colaterales visibles.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La interfaz pública es el conjunto de miembros accesibles desde fuera de la clase: métodos, constructores y, en su caso, constantes públicas. Define qué se puede hacer con el objeto, no cómo está implementado.

Relación con la ocultación:
La interfaz pública es la “frontera” entre el cliente (quien usa la clase) y su implementación interna (oculta).
Gracias a la ocultación, solo la interfaz pública es visible; atributos y detalles internos quedan privados o protegidos, evitando dependencias frágiles.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

La interfaz pública es un contrato con todos los clientes de la clase. Afecta a usabilidad, seguridad, mantenibilidad y extensibilidad.
Cambiarla no es fácil, cualquier modificación puede romper el código de todos los clientes.
Por ello conviene mantenerla mínima y coherente, evitar exponer detalles de implementación, diseñar con invariantes, precondiciones y postcondiciones en mente y documentarla y testearla bien.

## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Una invariante de clase es una condición lógica que debe cumplirse siempre para cualquier objeto válido de esa clase (excepto quizás durante la ejecución interna de un método).
Ejemplos: “el saldo nunca es negativo”, “las coordenadas no son NaN”, “la fecha es válida”.

La ocultación de información ayuda porque impide que código externo modifique el estado de forma arbitraria y obliga a que todas las mutaciones pasen por métodos controlados, donde podemos validar y hacer cumplir la invariante.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

public final class Punto {
    // Estado interno (oculto)
    private double x;
    private double y;

    // Parte de la interfaz pública
    public Punto(double x, double y) {
        validarNoNaN(x, y);
        this.x = x;
        this.y = y;
    }

    // Constructor de copia 
    public Punto(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("El punto no puede ser null");
        this.x = otro.x;
        this.y = otro.y;
    }

    // --- Métodos de acceso controlado (getters y setters opcionales) ---
    public double getX() { return x; }
    public double getY() { return y; }

    // Si se permiten cambios, validamos para mantener la invariante.
    public void setX(double x) {
        validarNoNaN(x, this.y);
        this.x = x;
    }

    public void setY(double y) {
        validarNoNaN(this.x, y);
        this.y = y;
    }

    // Parte de la interfaz pública
    public double calcularDistanciaAOrigen() {
        return Math.hypot(x, y); // equivalente a sqrt(x*x + y*y) y más estable numéricamente
    }

    public double calcularDistanciaA(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("El punto no puede ser null");
        return Math.hypot(this.x - otro.x, this.y - otro.y);
    }

    // Implementación oculta 
    private static void validarNoNaN(double x, double y) {
        if (Double.isNaN(x) || Double.isNaN(y)) {
            throw new IllegalArgumentException("Las coordenadas no pueden ser NaN");
        }
    }

    @Override
    public String toString() {
        return "Punto(" + x + ", " + y + ")";
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Punto)) return false;
        Punto p = (Punto) obj;
        return Double.doubleToLongBits(x) == Double.doubleToLongBits(p.x)
            && Double.doubleToLongBits(y) == Double.doubleToLongBits(p.y);
    }

    @Override
    public int hashCode() {
        long bits = Double.doubleToLongBits(x);
        bits = 31 * bits + Double.doubleToLongBits(y);
        return (int)(bits ^ (bits >>> 32));
    }
}

Interfaz pública de Punto:

-Constructores: Punto(double, double), Punto(Punto).
-Getters/Setters (si decides exponerlos): getX(), getY(), setX(double), setY(double).
-Métodos de dominio: calcularDistanciaAOrigen(), calcularDistanciaA(Punto).
-Métodos heredados sobrescritos útiles para clientes: toString(), equals(Object), hashCode().

public: accesible desde cualquier clase/módulo. Forma parte de la interfaz pública.
private: accesible solo dentro de la propia clase. Es detalle de implementación oculto al exterior.

## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En Java, los modificadores de acceso public y private se pueden aplicar a:
- Clases (solo public o default, no private)

Solo las clases de nivel superior (las que no están dentro de otra clase) pueden ser:
public
default (sin modificador)

No pueden ser private.

- Clases internas (inner classes)

Sí pueden ser:

public
private
protected
default

- Miembros de clase
(atributos, métodos, constructores)
Pueden ser:

public
private
protected
default

## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

En POO sí existen más tipos, y dependen del lenguaje.
- En Java existen 4 niveles de visibilidad:

public → accesible desde cualquier parte.
private → accesible solo dentro de la clase.
protected → accesible en:

la misma clase
el mismo paquete
subclases (incluso en otros paquetes)


default (sin palabra clave) → accesible solo dentro del mismo paquete.

- En otros lenguajes:

C++:
public, private, protected.
No existe visibilidad por paquete.


Python:
No tiene visibilidad real y usa convenciones:

_atributo → “no lo uses desde fuera”.
__atributo → name mangling para minimizar accesos accidentales.

C#:
Más variado: public, private, protected, internal, protected internal, private protected.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

Los miembros privados están ocultos para otras clases,
pero NO están ocultos para otras instancias de la misma clase.
Eso significa que un objeto de la clase sí puede acceder a los atributos privados de otro objeto de la misma clase.
public class Punto {
    private double x;
    private double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double calcularDistanciaAPunto(Punto otro) {
        // Aquí accedemos a otro.x y otro.y aunque sean private.
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.hypot(dx, dy);
    }
}
-x e y son privados, así que otras clases no los pueden leer, pero dentro de la misma clase, cualquier instancia puede acceder a los atributos privados de otra instancia, porque comparten el mismo tipo y el mismo contexto.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Son métodos que permiten acceder o modificar atributos privados respetando la ocultación de información.

Getter: devuelve el valor de un atributo.
Ejemplo: getX() devuelve x.

Setter: permite modificar un atributo con validación.
Ejemplo: setX(double x) asigna un nuevo valor a x.

Sirven para proteger invariantes, controlar accesos y evitar exponer atributos públicos.

## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

No, en este contexto, “seguridad” no significa seguridad frente a hackers.
Significa:

Mayor robustez → es difícil usar mal la clase.
Protección del estado interno → no se puede dejar un objeto en un estado inválido.
Interfaces más fiables → menos errores, menos acoplamiento.

La ocultación de información mejora la calidad del diseño, no protege contra ataques externos al programa. Para evitar que un programa sea hackeado se usan otros mecanismos:
criptografía, autenticación, permisos del sistema, seguridad de red, validación de entradas externas, etc.

## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

Miembro de instancia:

-Pertenece a cada objeto individual.
-Cada instancia tiene su propia copia del atributo.
-Los métodos de instancia operan sobre el estado particular del objeto.
Ej.: x e y de un Punto.

Miembro de clase (static):

-Pertenece a la clase en sí, no a los objetos.
-Existe una única copia compartida entre todas las instancias.
-Se accede con NombreDeClase.miembro.
Ej.: un contador de objetos creados.

Sí que se pueden ocultar los miembros de clase:
Los miembros static pueden ser public, private, protected o default, igual que los de instancia.

## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, en varios casos:

-Patrón Singleton: se impide crear más de una instancia, el constructor es private y se da un método estático para obtener la instancia.

Fábricas estáticas (factory methods): se obliga a crear objetos mediante métodos estáticos controlados, como Punto.of(x, y).

Clases utilitarias: clases como Math no deben instanciarse, así que hacen su constructor private.

Control total del proceso de creación: evita inconsistencias y permite aplicar cachés, validaciones o inicialización especial.

## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.


// Miembros de clase 
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;

    public static double getMaxX() { return maxX; }
    public static double getMaxY() { return maxY; }


    public Punto(double x, double y) {
        this.x = x;
        this.y = y;

        // Actualización de los máximos globales
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }

    // --- Resto de métodos ---
    public double getX() { return x; }
    public double getY() { return y; }

    public void setX(double x) {
        this.x = x;
        if (x > maxX) maxX = x;
    }

    public void setY(double y) {
        this.y = y;
        if (y > maxY) maxY = y;
    }


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

### Respuesta


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

### Respuesta


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

### Respuesta


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

### Respuesta


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

### Respuesta


## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

### Respuesta
