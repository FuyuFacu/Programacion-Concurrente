Date: 2025-09-21
Course: [[programacion_concurrente]]
Tags: [[monitores]], [[Practica 3 (Concurrencia)]]

## Corregido, a ver si ta bien esta implementacion...

# Enunciado
Existe una comisión de 50 alumnos que deben realizar tareas de a pares, las cuales son corregidas por un JTP. Cuando los alumnos llegan, forman una fila. Una vez que están todos en fila, el JTP les asigna un número de grupo a cada uno. Para ello, suponga que existe una función `AsignarNroGrupo()` que retorna un número **“aleatorio”** del 1 al 25. Cuando un alumno ha recibido su número de grupo, comienza a realizar su tarea. Al terminarla, el alumno le avisa al JTP y espera por su nota. Cuando los dos alumnos del grupo completaron la tarea, el JTP les asigna un puntaje (el primer grupo en terminar tendrá como nota 25, el segundo 24, y así sucesivamente hasta el último que tendrá nota 1).

> Nota: el *JTP* no guarda el número de grupo que le asigna a cada alumno.
# Respuesta

```java
Process Alumno[id: 1..50] {
    int numero_grupo;
    int nota;
    
    Fila.llegar(id, numero_grupo);
    -- hacer el examen
    Tarea.finalizar(numero_grupo, nota );
}

Process JTP() {
    Fila.asignar();
    Fila.asignarNota();
}

Monitor Fila() {
    int cantidad_alumnos = 0;
    cola fila;
    cond asignado[50];
    cond espera;
    int grupoAsignado[50]

    procedure llegar(int id, int out grupo) {
        push(cola, id);
        cantidad_alumnos++; // Los propios alumnos son los que se encargan de avisar que todos
                            // hayan llegado a la fila.
        if (cantidad_alumnos == 50) 
            signal(espera);

        wait(asignado[id]);

        grupo = grupoAsignado[id];

    }

    procedure asignarGrupo() {
        if (cantidad_alumnos < 50) wait(espera);

        repeat 25:
            int num_grupo = AsignarNroGrupo();
            repeat 2:
                int id = pop(cola, id);
                grupoAsignado[id] = num_grupo;
                signal(asignado[id]);
    }
}

Monitor Tarea() {
    cond hayAlumno;
    int grupos[25] = ([25], 0);
    int notas[25] = ([25], 0);
    cond asignado[25];
    cola c;
    int nota_asignada = 25;

    procedure finalizar(int num_grupo, out int nota) {
        grupos[num_grupo]++;

        push(c, num_grupo);
        signal(hayAlumno);

        wait(asignado[num_grupo]);
        nota = notas[num_grupo];
    }

    procedure asignar_nota() {
        while (nota_asignada > 0) {
            if (c.isEmpty()) wait(hayAlumno);

            int num_grupo = c.pop();

            if (grupos[num_grupo] == 2) {
                notas[num_grupo] = nota_asignada;
                nota_asignada--;
                signal_all(asignado[num_grupo]);
            }
        }
    }
}

```
