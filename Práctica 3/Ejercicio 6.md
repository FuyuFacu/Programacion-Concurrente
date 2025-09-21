Date: 2025-09-21
Course: [[programacion_concurrente]]
Tags: [[monitores]], [[Practica 3 (Concurrencia)]]

# Enunciado
Existe una comisión de 50 alumnos que deben realizar tareas de a pares, las cuales son corregidas por un JTP. Cuando los alumnos llegan, forman una fila. Una vez que están todos en fila, el JTP les asigna un número de grupo a cada uno. Para ello, suponga que existe una función `AsignarNroGrupo()` que retorna un número **“aleatorio”** del 1 al 25. Cuando un alumno ha recibido su número de grupo, comienza a realizar su tarea. Al terminarla, el alumno le avisa al JTP y espera por su nota. Cuando los dos alumnos del grupo completaron la tarea, el JTP les asigna un puntaje (el primer grupo en terminar tendrá como nota 25, el segundo 24, y así sucesivamente hasta el último que tendrá nota 1).

> Nota: el *JTP* no guarda el número de grupo que le asigna a cada alumno.
# Respuesta

```java
Monitor Fila {
    cond asignados[1..50];
    int numeros[1..50];
    int gruposFinalizados[1..25] = ([25], 0);
    int notaGrupo[1..25] = ([25], 0);
    cond esperaGrupos[1..25];
    
    procedure esperarNumero(int id, out int numero) {
        wait(asignados[id]);
        numero = numeros[id];
    }
    
    procedure asignarNumero(int numero, int id) {
        numeros[id] = numero;
        signal(asignados[id]);
    }
    
    procedure enviarResultado(int grupo, out int resultado){
        gruposFinalizados[grupo]++;
        if (gruposFinalizados[grupo] == 2) {
            push(colaTerminados, grupo);
            signal(jtp);
        } 
        wait(esperaGrupos[grupo]);
        resultado = notaGrupo[grupo];
        
    }
    
    procedure asignarNota(int nota) {
        if (colaTerminados.isEmpty()) wait(jtp);
        int n_grupo = colaTerminados.pop();
        notaGrupo[n_grupo] = nota;
        signal_all(esperaGrupos[n_grupo]);
    }
}

sem mutex = 1;
sem disponibles = 1;
int esperando = 0;

Process Alumno[id: 1..50]
{
    P(mutex);
    esperando++
    if (esperando == 50) 
        V(disponibles);
    V(mutex);
    
    int resultado;
    int numero;
    Fila.esperarNumero(id, numero);
    -- realizar Tarea
    Fila.enviarResultado(numero, resultado);
}

Process JTP {

    int numAux = 0;
    P(disponibles);
    for i: 1..50 {
        numAux = AsignarNroGrupo();
        Fila.asignarNumero(numAux, i);
    }
    
    int nota = 25;
    while (nota >= 1) {
        Fila.asignarNota(nota);
        nota--;
    }
}
```
