
a)
```java
// P personas que esperan a que el empleado les deje acceder al simulador, lo usa por un rato y se retira
Process Persona[id: 1..P]{
    Empleado!request(id);
    usarSimulador();
    Empleado!finalizar();
}

// Empleado encargado de administrar su uso
Process Empleado {
    for i := 1 to P do begin
        int idP;
        Persona[*]?request(idP);
        Persona[idP]?finalizar();
    end;
}
```


b)
```java
// P personas que esperan a que el empleado les deje acceder al simulador, lo usa por un rato y se retira
Process Persona[id: 1..P]{
    Empleado!request();
    usarSimulador();
    Empleado!finalizar();
}

// Empleado encargado de administrar su uso
Process Empleado {
    int idP;
    for i := 1 to P do begin
        Persona[i]?request();
        Persona[i]?finalizar();
    end;
}
```

c) 
```java 
// Arreglado!!!!

Process Persona[id: 1..P]
{
    Administrador!request(id);
    Empleado?turno();
    usarSimulador();
    Empleado!finalizar();
}

Process Administrador {
    int idE, idP;
    cola Fila;
    int cant_atendidos = 0;

    while (cant_atendidos <= P) {
        if (not empty(Fila)); Empleado?pedido() -> {
            Empleado!recibir(Fila.pop());
            cant_atendidos++;
        }
        [] Persona[*]?request(idP); -> {
            push(Fila, idP);
        }
    }
}

Process Empleado {
    int idP;
    cola Fila;
    int cant_atendidos = 0;

    for i:= 1 to P {
        Administrador!pedido();
        Administrador?recibir(idP);
        Persona[idP]!turno();
        Persona[idP]?finalizar();
    }
}

```