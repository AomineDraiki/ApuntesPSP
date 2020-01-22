# Tema 2: Hilos

## INDEX

1. *__Qué es un hilo__*?

2. *__Diferencia entre hilo y proceso __*

3. *__Requisitos multihilo__*

4. ***Estados de los hilos***

5. ***Tipos De Hilos***
   1. ***Hilos Independientes***
   2. ***Hilos Cooperantes***
   
6. ***Métodos De Los Hilos***

   

## Concepto de proceso?



### Definición:

- Es una instancia de un programa
- Es propietario de otros recursos que se crean durante la vida de dicho proceso y se destruyen cuando termina.

- Es un árbol e-nario de hilos, es decir, es al menos un hilo, y se llama hilo primario.

  

### Un proceso posee:

---

- Su propio espacio de direcciones
- Su memoria
- Sus variables
- Ficheros abiertos
- Procesos hijos
- Estado

- Un sistema puede ejecutar varios procesos **concurrentemente**, aunque la UCP alterna entre esos procesos.

  

### Estados de un proceso

---

En los procesos es posible 4 transiciones:

 - De **preparado** a **ejecución**

 - De **ejecución** a **preparado**

 - De **ejecución** a **bloqueado**

 - De **bloqueado** a **preparado**

   

### Todos Los Estados Posibles:

---

- Nuevo
- Preparado
- Ejecución
- Bloqueado
- Finalizado



> ***IMPORTANTE***
>
> >En los procesos el que pasa a ejecución es aquel que tiene o mayor prioridad o esta en la cabeza de la cola.



## Qué es un hilo? 



### Definición

---

- Secuencia de código que está en ejecución dentro del contexto de un proceso, en el mismo espacio de direcciones.

  

> ***IMPORTANTE***
>
> > Cuando hablamos de hilos, podemos asumir que la cola de hilos está vacía siempre y cuando los hilos se ejecuten asincrónicamente.



### Información

---

- Los hilos comparten la UCP de la misma forma que lo hacen los procesos, pueden crear hilos hijo y se pueden bloquear. Mientras un hilo esté bloqueado se puede ejecutar otro hilo del mismo proceso.
- En la UCP puede haber varios programas con varios procesos ejecutándose concurrentemente (Multi-Tarea) y a su vez un proceso puede crear varios hilos hijo y ejecutarlos de forma concurrente.



Cada hilo se ejecuta en forma estrictamente secuencial y tiene su propia pila, el estado de los registros de la UCP y su propio contador de programa.



### Comparten

---

- El mismo espacio de direcciones

- Comparten las mismas variables globales

- Conjunto de ficheros abiertos

- Procesos hijos (**no hilos hijo**)

- Señales

- Semáforos

  

### Que ventajas aporta un hilo respecto a un proceso?

---

1. Debido a que los hilos comparten {**espacio de memoria, el código y los recursos**} su ejecución es más económica que la de los procesos.

2. Mayor facilidad al resolver problemas complejos.

   

### Que es el paralelismo Real?

---

- En un instante dado, varias instrucciones están siendo interpretadas ejecutándose en la unidad de control 

- Se ejecuta al mismo tiempo que otros hilos del proceso al que pertenece, atendido por un núcleo 

- Los n hilos de un proceso se están ejecutando al mismo tiempo y eso es lo que me da MULTITAREA 

  

### Que es el Multi-Threading

---

- Son múltiples hilos de un proceso ejecutándose concurrentemente



### Estados de un hilo

---

- ***Nuevo***
  - Ha sido creado pero aún no ha sido activado, es decir, hemos creado el objeto hilo pero aun no hemos ejecutado el método `.start()`
- ***Preparado***
  - El hilo está activo y está a la espera de que le sea asignada la UCP
- ***En ejecución***
  - El hilo está listo y le ha sido asignada la UCP (sólo los hilos preparados pueden ser ejecutados)
- ***Bloqueado***
  - El hilo esta esperando a que otro elimine el bloqueo
  - Un hilo ***bloqueado*** puede estar:
    - ***Dormido***
      - Bloqueado durante una cantidad de tiempo determinada, pasado ese tiempo despertara y pasará a estado preparado.
    - ***Esperando***
      - Esperando a que ocurra alguna otra cosa, cuando ocurra pasará a estado preparado
        - Un mensaje ***notify***
        - Operación E/S
        - Adquirir la propiedad de un método sincronizado
- ***Muerto***
  - El hilo ha finalizado, pero todavía no ha sido recogido por su padre. Estos hilos no pueden alcanzar ningún otro estado.

```java
public static void inicializarHilos()
{
    /*
        Crearemos los hilos suponiendo que ya tenemos sus clases creadas
        */
    
    ClaseHilo hiloNuevo = new ClaseHilo(); // Aqui el hilo se encontraria en 
										  //  Estado Nuevo
    
    hiloNuevo.start() // Con esta orden estamos haciendo que el planificador sepa 
					 //  acerca de la existencia de ese hilo y el hilo pasa a
        			//   Estado Preparado
}
```

> ***IMPORTANTE***
>
> > Todos los hilos pueden pasar por el estado bloqueado, pero no todos pueden pasar por el estado ***ESPERANDO***. Por este estado solo pasaran aquellos hilos que sean cooperantes, es decir, hilos que comparten recursos.
> >
> > La transición entre estados esta controlada por un planificador.



### Como se crean los hilos?

---

Los hilos en Java se pueden crear de dos formas:

- Escribiendo una nueva clase derivada de ***Thread***
  - El primer paso para hacerlo de esta forma es:
    - Escribir la clase del hilo derivada de **Thread** y sobrescribir el método `run()` heredado por esta , con el fin de especificar la tarea que tiene que realizar dicho hilo.

```java
public class ContadorAdelante extends Thread
{
    public ContadorAdelante(String nombre) // Constructor
    {
        if (nombre != null) setName(nombre);
        start(); // el hilo ejecuta su propio método run
    }
    
    public ContadorAdelante(){this(null);} // Constructor
    
    public void run()
    {
        for (int i = 1; i <= 1000; i++)
        {
            System.out.print(getName()+" " + i + "\r" );
        }
    }
}
```

```java
public class Test
{
    public static void main(String[] args)
    {
        // El método start() de este hilo se encuentra en su constructor
        ContadorAdelante cuenta = new ContadorAdelante("Contador+");
    }
}
```



### ***INFORMACIÓN:***

---

El operador `new` (En este momento el hilo está en estado **NUEVO**) .

El método `start` cambia el estado del hilo a **preparado**. A partir de este momento será el planificador de hilos el que determine cuándo éste pasa al estado de ejecución y cuándo lo abandona para permitir que se ejecuten simultáneamente otros hilos.

El método `start` no hace que se ejecute directamente el método `run` del hilo, sino que lo sitúa en el estado **preparado** para que compita por la UCP junto con el resto de hilos que estén en ese estado. Sólo el planificador de hilos puede asignar tiempo de UCP a un hilo y lo hará en cualquier instante después de que haya recibido el mensaje **start**.





> ***IMPORTANTE***

> > Para poder lanzar un hilo, primero tenemos que construir un objeto  de esa clase y después enviar a dicho objeto el mensaje `start()`
> >
> > El método `start()` es el que se encarga de presentar el hilo al planificador.
> >
> > Si llamamos al método `run` sin llamar antes a `start` entonces el hilo no va a ser conocido por el planificador y esto va a ocasionar varios problemas, por ello esto está considerado como un error gravísimo.



- Haciendo que una clase existente implemente la interfaz ***Runnable***
- 
- Clase Thread con sus métodos

```java
public class Thread extends Object implements Runnable
{
    //Atributos
    //Constructores
    Thread(argumentos);
    //Metodos
}
```



### Atributos:

---

- `static int MAX_PRIORITY;`
  - Prioridad máxima que un hilo puede tener
- `static int MIN_PRIORITY;`
  - Prioridad mínima que un hilo puede tener
- `static int NORM_PRIORITY;`
  - Prioridad asignada por omisión a un hilo



### Métodos:

---

| Método                            | Acción                                                       | Retorno         |
| :-------------------------------- | :----------------------------------------------------------- | :-------------- |
| `static Thread currentThread()`   | **Devuelve una referencia al hilo** que actualmente está en ejecución | **Thread**      |
| `boolean isDaemon()`              | Verifica si es un hilo demonio                               | **boolean**     |
| `String getName()`                | <u>Devuelve el nombre del hilo</u>                           | **String**      |
| `int getPriority()`               | <u>Devuelve la prioridad del hilo</u>                        | **int**         |
| `boolean isInterrupted()`         | Verifica si este hilo ha sido interrumpido                   | **boolean**     |
| `boolean isAlive()`               | Verifica si este hilo esta vivo (no ha terminado)            | **boolean**     |
| `void destroy()`                  | Destruye este hilo sin realizar ninguna operación de limpieza. | **No retorna ** |
| `void interrupt()`                | Envía este hilo al estado preparado                          | **No retorna**  |
| `void join()`                     | Espera indefinidamente o el tiempo especificado, a que este hilo termine | **No Retorna**  |
| `void run()`                      | Contiene el código que el hilo ejecutará cuando pasa al estado de ejecución. | **No Retorna**  |
| `void setDaemon(boolean on)`      | Define este hilo como un demonio o como un hilo de usuario   | **No Retorna**  |
| `void setName(String name)`       | Cambia el nombre al hilo                                     | **No Retorna**  |
| `void setPriority(int prioridad)` | Cambia la prioridad de este hilo                             | **No Retorna**  |
| `static void sleep()`             | Envía a este hilo a dormir por el tiempo especificado        | **No Retorna**  |
| `void start()`                    | Inicia la ejecución de este hilo, Java invoca al método run del hilo | **No Retorna**  |
| `static void yield()`             | Detiene temporalmente la ejecución de este hilo para permitir la ejecución de otros | **No Retorna**  |
| `void notify()`                   | Despierta **un** hilo de los que están esperando por el monitor del objeto | **No Retorna**  |
| `void notifyAll()`                | Despierta **todos** los hilos que están esperando por el monitor del objeto | **No Retorna**  |
| `void wait()`                     | Envía este hilo al estado de espera, hasta que otro hilo ejecute `notify()` o `notifyAll()` o hasta que acabe el tiempo. | **No Retorna**  |

> ***IMPORTANTE***
>
> > Una clase que implemente la interfaz **Runnable** tiene que sobrescribir el método run aportado por está, de ahí que **Thread** proporcione el método aunque no haga nada. **Run()** contendrá el código que debe ejecutar el hilo.
>
> > Los métodos como **stop, suspend, resume y runFinalizersOnExit** han sido desaprobados por lo inseguros que son.
> >
> > El método `yield()` el hilo sale de forma voluntaria del estado de ejecución, pero puede darse el caso de que la cola de hilos este vacía, en ese caso el planificador no lo sacará de ejecución.





