<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

En C podemos componer estructuras cuando una estructura contiene a otra. Es una relación del tipo “A tiene un B”.
En este caso:
Un Punto tiene dos coordenadas: x e y. Una Linea está formada por dos puntos.
#include <stdio.h>
#include <math.h>

/* Definición de un punto */
struct Punto {
    double x;
    double y;
};

/* Definición de una línea formada por dos puntos */
struct Linea {
    struct Punto inicio;
    struct Punto fin;
};

/* Calcula la distancia entre dos puntos */
double distancia(struct Punto p1, struct Punto p2) {
    double dx = p2.x - p1.x;
    double dy = p2.y - p1.y;
    return sqrt(dx * dx + dy * dy);
}

/* Calcula la longitud de una línea */
double longitud(struct Linea l) {
    return distancia(l.inicio, l.fin);
} // En C no existe encapsulación ni control de modificaciones: cualquier función puede alterar los campos directamente.

## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

En Java aplicamos orientación a objetos, mejorando el diseño gracias a:
-Encapsulación
-Inmutabilidad
-Métodos asociados al comportamiento del objeto

Clase Punto (inmutable):
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double distancia(Punto otro) {
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
Clase línea:
public final class Linea {
    private final Punto inicio;
    private final Punto fin;

    public Linea(Punto inicio, Punto fin) {
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
Ventajas frente a C:

·No se pueden modificar los puntos ni la línea tras crearse
·Se garantiza coherencia del estado
·El comportamiento está ligado a los datos

## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

En el ejemplo Linea – Punto:

De Linea a Punto, una Linea tiene exactamente 2 Puntos
Multiplicidad: 2

De Punto a Linea, un Punto puede estar en 0, 1 o muchas Líneas
Multiplicidad: 0..*

## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

Composición fuerte (composición propiamente dicha)

El objeto compuesto posee a sus partesy las partes no pueden existir sin el todo: comparten el ciclo de vida

Ejemplo:
Linea posee sus Puntos, si la línea desaparece, los puntos también
Es lo que en UML se llama composición

Composición débil (asociación o agregación)
Las partes pueden existir independientemente
El ciclo de vida no depende del objeto compuesto

Ejemplo:
Un Equipo y sus Jugadores, el equipo se disuelve, los jugadores siguen existiendo

Esto suele llamarse asociación o agregación.

## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

Cuando una clase solo usa a otra:

-Como parámetro de un método
-Como valor de retorno
-Creándola con new dentro de un método
-Como variable local
No es composición, estamos hablando de una dependencia. Ejemplo de dependencia:
public double calcular(Punto p) {
    Punto aux = new Punto(0, 0);
    return aux.distancia(p);
}
La relación es temporal y no forma parte del estado del objeto.

## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

Composición fuerte:
public final class LineaFuerte {

    public static final class Punto {
        private final double x;
        private final double y;

        private Punto(double x, double y) {
            this.x = x;
            this.y = y;
        }

        private double distancia(Punto otro) {
            double dx = otro.x - this.x;
            double dy = otro.y - this.y;
            return Math.sqrt(dx * dx + dy * dy);
        }
    }

    private final Punto inicio;
    private final Punto fin;

    public LineaFuerte(double x1, double y1, double x2, double y2) {
        this.inicio = new Punto(x1, y1);
        this.fin = new Punto(x2, y2);
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
Composición débil:
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double distancia(Punto otro) {
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public final class LineaDebil {
    private final Punto inicio;
    private final Punto fin;

    public LineaDebil(Punto inicio, Punto fin) {
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

En Java no existe destrucción explícita de objetos.
Motivo:

Java usa recolección automática de basura (Garbage Collector)
Un objeto se destruye cuando no hay referencias a él

En la composición fuerte:

Cuando una Linea deja de estar referenciada
Sus Punto también dejan de estarlo
El GC los elimina automáticamente

Conclusión:
La destrucción está implícita en la pérdida de referencias, no en llamadas explícitas.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

Profesor:
public final class Profesor {
    private final String nombre;

    public Profesor(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}
Departamento:
public final class Departamento {

    private static final int MAX = 50;
    private final Profesor[] profesores = new Profesor[MAX];
    private int numProfesores = 0;
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) {
            throw new IllegalArgumentException("Debe existir un director");
        }
        this.director = directorInicial;
        this.profesores[0] = directorInicial;
        this.numProfesores = 1;
    }

    public int getNumeroProfesores() {
        return numProfesores;
    }

    public Profesor getProfesor(int pos) {
        if (pos < 0 || pos >= numProfesores) {
            throw new IndexOutOfBoundsException();
        }
        return profesores[pos];
    }

    public void addProfesor(Profesor p) {
        if (numProfesores == MAX) {
            throw new IllegalStateException("Departamento lleno");
        }
        profesores[numProfesores++] = p;
    }

    public void removeProfesor(int pos) {
        Profesor p = getProfesor(pos);
        if (p == director) {
            throw new IllegalStateException("No se puede eliminar al director");
        }
        for (int i = pos; i < numProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[--numProfesores] = null;
    }

    public void cambiarDirector(Profesor nuevoDirector) {
        boolean pertenece = false;
        for (int i = 0; i < numProfesores; i++) {
            if (profesores[i] == nuevoDirector) {
                pertenece = true;
                break;
            }
        }
        if (!pertenece) {
            throw new IllegalArgumentException(
                "El director debe pertenecer al departamento"
            );
        }
        this.director = nuevoDirector;
    }

    public Profesor getDirector() {
        return director;
    }
}

## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public final class Departamento {

    private final List<Profesor> profesores = new ArrayList<>();
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) {
            throw new IllegalArgumentException("Debe existir un director");
        }
        this.director = directorInicial;
        profesores.add(directorInicial);
    }

    public int getNumeroProfesores() {
        return profesores.size();
    }

    public Profesor getProfesor(int pos) {
        return profesores.get(pos);
    }

    public void addProfesor(Profesor p) {
        profesores.add(p);
    }

    public void removeProfesor(int pos) {
        Profesor p = profesores.get(pos);
        if (p == director) {
            throw new IllegalStateException("No se puede eliminar al director");
        }
        profesores.remove(pos);
    }

    public void cambiarDirector(Profesor nuevoDirector) {
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalArgumentException(
                "El director debe pertenecer al departamento"
            );
        }
        this.director = nuevoDirector;
    }

    public List<Profesor> getProfesores() {
        return Collections.unmodifiableList(profesores);
    }
}
Con esto nos ahorramos la gestión manual del tamaño, los desplazamientos al eliminar y el control de límites.
¿Por qué no devolver la lista directamente?
Problema:
-El código externo podría modificar la lista
-Se rompe la encapsulación y las invariantes

Solución:
-Devolver una vista inmodificable
-O una copia defensiva

## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

public final class Persona {
    private final String nombre;
    private final Persona madre;

    public Persona(String nombre, Persona madre) {
        this.nombre = nombre;
        this.madre = madre;
    }

    public String getNombre() {
        return nombre;
    }

    public Persona getMadre() {
        return madre;
    }
}
Ejemplo de uso:
public class Main {
    public static void main(String[] args) {
        Persona abuela = new Persona("Ana", null);
        Persona madre = new Persona("Beatriz", abuela);
        Persona hijo = new Persona("Carlos", madre);

        System.out.println(hijo.getNombre());                    // Carlos
        System.out.println(hijo.getMadre().getNombre());         // Beatriz
        System.out.println(hijo.getMadre().getMadre().getNombre()); // Ana
    }
}
Otros ejemplos clásicos de composiciones recursivas: Árboles (nodos con hijos), carpetas y subcarpetas, excepciones (Throwable con causa)...

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

Este tipo de composición permite navegar la relación en ambos sentidos. En este caso Departamento conoce a sus profesores y todos los profesores conocen su departamento. Para ello habría que añadir un atributo Departamento en Profesor
public final class Profesor {
    private final String nombre;
    private Departamento departamento;

    void setDepartamento(Departamento d) {
        this.departamento = d;
    }

    public Departamento getDepartamento() {
        return departamento;
    }
}
public void addProfesor(Profesor p) {
    profesores.add(p);
    p.setDepartamento(this);
}
Hay inconsistencias si se te olvida actualizar uno de los dos lados.
