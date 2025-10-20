

```java
chan Pedido
chan cobrar
chan Respuesta[1..N];
chan Factura[1..N];

Process Cliente[id: 1..N]
{
    ticket t;
    int numeroCajero;
    send Pedido(id);
    receive Respuesta[id](numeroCajero);
    usarCajero(numeroCajero);
    send cobrar(id);
    receive Factura[id](t);
}

Process Empleado {
    int idAux;
    int numeroCabina;
    while (true) {
        if (cobrar.isEmpty()) {
            receive Pedido(idAux);
            numeroCabina = elegirCabina()
            send Respuesta[idAux](numeroCabina);
        } else {
            receive cobrar(idAux) 
            Ticket f = ProcesarYCrearFactura(idAux);
            send Factura[id](f);
        }
    }
}

```