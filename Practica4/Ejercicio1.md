
1)
a)
```java
chan Consultas(texto);

Process Cliente[id:1..N] {
    texto Consulta;
    while (true) {
        Consulta = GenerarConsulta();
        send Consultas(Consulta);
    }
}
Process Empleado {
    texto C;
    while (true) {
        receive Consultas(C);
        responderConsulta(C);
    }
}
```

b)
```java
chan Consultas(texto);

Process Cliente[id:1..N] {
    texto Consulta;
    while (true) {
        Consulta = GenerarConsulta();
        send Consultas(Consulta);
    }
}

Process Empleado[id: 1..2]{
    texto C;
    while (true) {
        receive Consultas(C);
        responderConsulta(C);
    }
}

```

c) 

```java
chan Consultas(texto);

Process Cliente[id:1..N] {
    texto Consulta;
    while (true) {
        Consulta = GenerarConsulta();
        send Consultas(Consulta);
    }
}

Process Empleado {
    texto C;
    while (true) {
        if (not empty Consultas) { // Con esto verifico que no este vacio en canal, y si lo esta pues espera 15 
                                    // minutos simulando que esta haciend tareas administrativas
            receive Consultas(C);
            responderConsulta(C);
        } else delay(900);
    }
}
```
