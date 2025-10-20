2)
```java
chan request[5](texto);
chan Respuestas[P](texto);
chan salidaCaja(int);
chan ResolucionFila[P](int);

Process Cliente[id: 1..P] {
    texto consulta;
    texto comprobante;
    int pos;

    send esperaCantidad(id);
    receive resolucionFila[id](pos);

    send(request[i](id, consulta));
    receive(Respuestas[id](comprobante));
    send salidaCaja(pos);
}

Process Administrador {
    int caja = 0;
    int cant_espera[1..5] = ([1..5], 0); // Inicializo en 0
    int pos;

    while (true) {
        if (not esperaCantidad.isEmpty()) -> {
            receive(esperaCantidad(idC));

            caja = getCajaMinima();
            cant_espera[caja]++;
            send resolucionFila[idC](caja);
        }
        [] (not salidaCaja.isEmpty()) {
            receive salidaCaja(posCaja);
            cant_espera[posCaja]--;
        }
        end if;
    }
}

Process Caja[id: 1..5] {
    texto consulta;
    int idP;
    receive(request[id](idP, consulta));
    texto comprobante = responderConsulta(consulta);
    send(Respuestas[idP](comprobante));
}
```

