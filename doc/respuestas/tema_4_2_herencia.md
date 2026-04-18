<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

¿Qué es la herencia en orientación a objetos?
La herencia es una relación entre clases que expresa que “A es‑un B”.
Es decir, una clase derivada (subclase) representa un tipo más específico de otra clase base (superclase). Ejemplo conceptual:
-Un Artillero es‑un Soldado
-Un Zapador es‑un Soldado


Implicaciones principales de la herencia
1.Compatibilidad de tipos (subtipado). Si Artillero y Zapador heredan de Soldado, entonces:
Un objeto Artillero puede usarse donde se espera un Soldado y un objeto Zapador puede usarse donde se espera un Soldado, esto permite:

-Polimorfismo
Colecciones homogéneas de tipos distintos pero compatibles
Formalmente, si Artillero extends Soldado, entonces Artillero es subtipo de Soldado.


2.Herencia de estado y comportamiento
Las subclases heredan el estado (atributos) y el comportamiento (métodos) sin tener que reescribirlos.
En el ejemplo:
nombre pertenece a Soldado, saludar() pertenece a Soldado y Artillero y Zapador no lo redefinen, pero lo usan

Ejemplo completo en Java de clase base Soldado:
public class Soldado {

    private final String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soy el soldado " + nombre);
    }
}  //nombre es privado, pero las subclases lo heredan como estado, no como acceso directo.
Subclase artillero:
public class Artillero extends Soldado {

    private final int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }

    public void dispararCohete() {
        System.out.println("Disparando cohete");
    }
}
Subclase Zapador:
public class Zapador extends Soldado {

    private final int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }

    public void ponerMina() {
        System.out.println("Mina colocada");
    }
}
Uso de compatibilidad:
public class Main {
    public static void main(String[] args) {

        Soldado[] ejercito = new Soldado[3];

        ejercito[0] = new Artillero("Juan", 5);
        ejercito[1] = new Zapador("Luis", 3);
        ejercito[2] = new Artillero("Pedro", 2);

        for (Soldado s : ejercito) {
            s.saludar();
        }
    }
}

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

Al crear un objeto de una subclase, siempre se ejecutan dos constructores (o más si hay más niveles):
Primero el constructor de la superclase y después el constructor de la subclase

Ejemplo:
JavaArtillero a = new Artillero("Juan", 5);``Mostrar más líneas
Orden real de ejecución:

Soldado(String nombre)
Artillero(String nombre, int cohetes)

-Regla fundamental:
La construcción de un objeto va siempre de arriba hacia abajo en la jerarquía.

¿Qué significa super dentro de un constructor?
super(...) sirve para invocar explícitamente un constructor de la clase base.
Javapublic Artillero(String nombre, int cohetes) {    super(nombre);   // constructor de Soldado    this.cohetes = cohetes;}``Mostrar más líneas

Inicializa la parte heredada del objeto y debe ser la primera instrucción del constructor
¿Es obligatorio llamar a super?
Sí, siempre se llama a algún constructor de la superclase, de dos formas:
Explícita: super(...)
Implícita: super() (sin parámetros)
Si la superclase NO tiene un constructor sin parámetros visible,

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

Un objeto Artillero en memoria contiene la parte Soldado (con nombre) y la parte Artillero (con cohetes). Aunque nombre sea private, existe físicamente en el objeto. No se puede usar directamente desde la subclase. La subclase hereda el estado, pero no el acceso directo.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

Implica que podemos añadir nuevos subtipos sin modificar el código existente que trabaja con el supertipo.
Esto es el principio Open/Closed: abierto a extensión, cerrado a modificación
Nuevo soldado:
public class Medico extends Soldado {

    private final int botiquines;

    public Medico(String nombre, int botiquines) {
        super(nombre);
        this.botiquines = botiquines;
    }

    public int getBotiquines() {
        return botiquines;
    }
}
Código:
Soldado[] ejercito = {
    new Artillero("Juan", 5),
    new Zapador("Luis", 3),
    new Medico("Ana", 10)
};

for (Soldado s : ejercito) {
    s.saludar();  // funciona igual
}

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

¿Puede una referencia del supertipo apuntar a un subtipo?
Sí (esto es lo normal):
JavaSoldado s = new Artillero("Juan", 5);Mostrar más líneas
Esto se llama upcasting (implícito y seguro).

¿Puedo invocar métodos del subtipo con esa referencia?
No directamente.
Javas.getCohetes(); //no compila porque el tipo de la referencia es Soldado.

Upcasting y Downcasting
Upcasting: Subtipo → Supertipo
JavaSoldado s = new Artillero("Juan", 5);Mostrar más líneas, implícito, seguro

Downcasting: Supertipo → Subtipo
JavaArtillero a = (Artillero) s;Mostrar más líneas, requiere comprobación

¿Qué es instanceof?
Sirve para comprobar el tipo real del objeto en tiempo de ejecución.

Ejemplo completo:
for (Soldado s : ejercito) {
    s.saludar();

    if (s instanceof Artillero) {
        Artillero a = (Artillero) s;   // downcasting seguro
        System.out.println("Cohetes: " + a.getCohetes());
    }
}

## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

protected permite acceso desde la misma clase, desde subclases y desde el mismo paquete.
Ejemplo en soldado:
public class Soldado {

    protected final String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soy el soldado " + nombre);
    }
}

Uso en zapador:
public class Zapador extends Soldado {

    private final int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public void ponerMina() {
        System.out.println(nombre + " ha puesto una mina");
    }
}

## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

En general (lenguajes OO) depende del lenguaje. Muchos lenguajes OO sí definen una clase raíz universal mientras otros lenguajes OO no la imponen. No es una característica obligatoria del paradigma, sino una decisión del lenguaje.

¿Qué ocurre en Java?
En Java existe una clase base para todos los objetos: java.lang.Object
Todas las clases en Java heredan directa o indirectamente de Object, incluso aunque no se indique explícitamente.
¿Qué aporta Object?
Todos los objetos en Java heredan métodos como:
-toString()
-equals(Object o)
-hashCode()
-getClass()
-clone() (protegido)
-finalize() (obsoleto)

Esto permite: tratar cualquier objeto de forma uniforme, usar colecciones genéricas.
Polimorfismo universal. No ocurre en todos los lenguajes
Ejemplos:
-Java, C#, Kotlin → sí (Object / Any)
-C++ → no existe una clase base universal
-Python → sí (object)
-Smalltalk → sí (Object)

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

La herencia múltiple ocurre cuando una clase hereda de más de una clase base.
Ejemplo conceptual:  Clase C hereda de A y de B
Esto permite: reutilizar código de varias jerarquías pero introduce problemas de ambigüedad.

Ejemplo clásico de problema:
Problema del diamante
¿Qué método se hereda si ambas clases lo definen?

¿Existe herencia múltiple en Java?
Java no permite herencia múltiple de clases y esto no es un accidente, es una decisión de diseño para evitar ambigüedades, simplificar el modelo mental y facilitar el mantenimiento.
Java si que tiene herencia múltiple pero solo de interfaces:
public class Artillero extends Soldado
        implements Disparable, Comunicable {
}

## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

Usuario:
public class Usuario {
    private final String id;

    public Usuario(String id) {
        this.id = id;
    }

    public String getId() {
        return id;
    }
}
Excepción personalizada:
public class UsuarioNoEncontradoException extends RuntimeException {

    private final Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        super("Usuario no encontrado: " + usuario.getId());
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
        super("Usuario no encontrado: " + usuario.getId(), causa);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}
Ejemplo de uso:
public class ServicioUsuarios {

    public Usuario buscarUsuario(String id) {
        Usuario u = null; // simula búsqueda fallida

        if (u == null) {
            throw new UsuarioNoEncontradoException(
                new Usuario(id)
            );
        }

        return u;
    }
}

## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

Porque la herencia no expresa “reutiliza código”, expresa “es‑un”. Cuando usamos herencia: creamos una relación semántica fuerte, introducimos dependencia estructural entre clases y aceptamos todas las decisiones de diseño de la superclase.

Problema principal:
Si A hereda de B solo para reutilizar código, estamos afirmando algo que no es verdad conceptualmente: que A es un B.

Consecuencias negativas:
-Jerarquías artificiales
-Subclases con métodos que no tienen sentido
-Fragilidad ante cambios en la superclase
-Violación de principios de diseño (LSP)

Por lo tanto, si lo único que quiero es reutilizar implementación, la herencia es una mala elección.

## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

Este principio signigica: Preferir construir objetos a partir de otros objetos, en lugar de heredar comportamiento.
Razones principales:
1.Menor acoplamiento
-La composición crea dependencias más débiles
-La herencia acopla fuertemente subclase ↔ superclase

2.Mayor flexibilidad
·Con composición:
 -Se puede cambiar el objeto compuesto
 -Se puede variar el comportamiento en tiempo de ejecución

·Con herencia:
 -La relación queda fijada en tiempo de compilación

3.Mejor encapsulación
-La composición respeta las interfaces públicas
-La herencia expone detalles internos de la superclase

4.Evita jerarquías rígidas
-La herencia crea árboles difíciles de modificar
-La composición permite diseños más planos y evolutivos

## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

·Con herencia:
-La subclase depende del comportamiento interno de la superclase
-Cambios en la superclase pueden romper subclases
-La subclase necesita conocer cómo funciona internamente el padre

La encapsulación no solo es ocultar atributos, sino ocultar decisiones de diseño

Ejemplo:
Si Soldado cambia la forma en que gestiona el nombre:
Las subclases pueden verse afectadas aunque el código compile o los atributos sigan siendo protected

·Con composición:
Solo dependes de la interfaz pública y los cambios internos no te afectan


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

Con herencia:
public class Persona {
    private final String dni;
    private final String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}
public class Estudiante extends Persona {

    public Estudiante(String dni, String nombre) {
        super(dni, nombre);
    }
}
public class Trabajador extends Persona {

    public Trabajador(String dni, String nombre) {
        super(dni, nombre);
    }
}

Con composición:
public class DatosPersonales {
    private final String dni;
    private final String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}
public class Estudiante {

    private final DatosPersonales datos;

    public Estudiante(DatosPersonales datos) {
        this.datos = datos;
    }

    public String getNombre() {
        return datos.getNombre();
    }
}
public class Trabajador {

    private final Datos;

    public Trabajador(DatosPersonales datos) {
        this.datos = datos;
    }

    public String getDni() {
        return datos.getDni();
    }
}

