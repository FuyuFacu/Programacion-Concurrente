
1)

b)
```java

process RobotExaminador[id: 1..R]
{
    text Direccion;

    while (true) {
        Direccion = buscarVirus()
        Analizador!dire(Direccion);
    }
}

process Analizador
{
    text auxDire;
    while (true) {
        RobotExaminador[*]?dire(auxDire);
        if (esVirus(auxDire)) {
            print("direccion infectada");
        } else print("direccion no infectada");
    }
}
```


c)
```java

process RobotExaminador[id: 1..R]
{
    text Direccion;

    while (true) {
        Direccion = buscarVirus()
        Administrador!reporte(Direccion);
    }
}

process Administrador {
    cola Fila; text Direccion; 

    while (true) {
        // consultar sobre si esta bien esta parte del switch
        do RobotExaminador[*]?reporte(Direccion) -> push (Fila, Direccion);
        [] (not empty(Fila)); Analizador?pedido() ->  { pop(Fila, Direccion)
                                                       Analizador!reporte(Direccion); }
    }
}

process Analizador
{
    text auxDire;
    bool hayVirus;
    while (true) {
        Administrador!pedido();
        Administrador?reporte(auxDire);

        hayVirus = chequear_direccion(auxDire);
        if (resultado) {
            print("direccion infectada");
        } else print("direccion no infectada");
    }
}

```