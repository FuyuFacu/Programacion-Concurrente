Date: 2025-09-23
Course: [[programacion_concurrente]]
Tags: [[monitores]], [[Practica 3 (Concurrencia)]]

# Enunciado
En un parque hay un juego para ser usada por **N personas** de a una a la vez y de acuerdo al orden en que llegan para solicitar su uso. Además, hay un empleado encargado de desinfectar el juego durante 10 minutos antes de que una persona lo use. Cada persona al llegar espera hasta que el empleado le avisa que puede usar el juego, lo usa por un tiempo y luego lo devuelve. Nota: suponga que la persona tiene una función Usar_juego que simula el uso del juego; y el empleado una función Desinfectar_Juego que simula su trabajo. **Todos los procesos deben terminar su ejecución.**

# Respuesta

```java
Monitor Juego {
    cond asignado[N];
    bool libre = true;
    bool horaLimpieza = false;
    cond liberado, hayCliente;
    cola c;
    int atendidos = 0;
    
    procedure jugar(in int id) {
        push(c,id);
        signal(hayCliente);
        wait(asignado[id]);
        libre = false;
    }
    
    procedure salir() {
        libre = true;
        signal(liberado);
    }
    
    procedure limpiar() {
        while (horaLimpieza == false) {
            if (not libre) wait(liberado);
            if (c.isEmpty()) wait(hayCliente);
            int idAux = c.pop();
            signal(asignado[idAux]);
            atendidos++;
        }
    }
    
    procedure hora_limpiar() {
        horaLimpieza = true;
        wait(completado);
    }
    
    procedure finalizarLimpieza() {
        horaLimpieza = false;
        signal(completado);
        if (atendidos == N)
            finalizado = true;
    }
    
}


Process Persona[id: 1..N] {
    Juego.jugar(id);
    -- Usar_juego();
    Juego.salir();
}

Process Empleado {
    bool finalizado = false;

    while (finalizado == false) {
        Juego.limpiar(); // o algo asi antes de que una  persona lo use
        Desinfectar_juego();
        Juego.finalizarLimpieza(finalizado);
    }
}

Process contador {
    bool finalizado = false
    while (finalizado == false) {
        delay(10m)
        Juego.hora_limpiar(finalizado);
    }
}
```


### Dudas
Mi duda es acerca de lo ultimo que menciona: **Todos los procesos deben terminar su ejecución.**
A esto se refiere a que cada uno de los procesos deben finalizar en cuanto todas las personas hayan pasado por el juego?  o  se refiere a que los procesos no deben  quedarse atascados en un loop infinito?
