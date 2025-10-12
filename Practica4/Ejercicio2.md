2)
```java
chan request[5](texto);
chan Respuestas[P](texto);

Process Cliente[id: 1..P] {
    int min = 999;
    int caja = 0;
    texto consulta;
    texto comprobante;

    for i:= 1 to 5  {
        if (request[i].size() < min) {
            min = request[i].size();
            caja = i;
        }
    }
    send(request[i](id, consulta));
    receive(Respuestas[id](comprobante));
}

Process Caja[id: 1..5] {
    texto consulta;
    int idP;
    receive(reset[id](idP, consulta));
    texto comprobante = responderConsulta(consulta);
    send(Respuestas[idP](comprobante));
}
```

