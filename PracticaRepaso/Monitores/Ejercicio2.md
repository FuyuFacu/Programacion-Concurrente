Resolver el siguiente problema. En una empresa trabajan 20 vendedores ambulantes que forman 5 equipos de 4 personas cada uno (cada vendedor conoce previamente a qué equipo pertenece). Cada equipo se encarga de vender un producto diferente. Las personas de un equipo se deben juntar antes de comenzar a trabajar. Luego cada integrante del equipo trabaja independientemente del resto vendiendo ejemplares del producto correspondiente. Al terminar cada integrante del grupo debe conocer la cantidad de ejemplares vendidos por el grupo. Nota: maximizar la concurrencia.

```java

Process Vendedor[id: 1..20]
    {
    int cant_ventas;
    int numeroEquipo;
    Calle.trabajar(numeroEquipo);
    comenzarAVender();
    int total = 0;
    Calle.finalizar(numeroEquipo, cant_ventas, total);

}

Monitor Calle {
    int grupos[5] = ([N], 0);
    int total_ventas[5] = ([N], 0);
    cond espera[5];
    cond finalizados[5];
    int gruposFinalizados[5] = ([N], 0);

    procedure trabajar(int numeroEquipo) {
        grupos[numeroEquipo]++;
        if (grupos[numeroEquipo] == 4) {
            signal_all(espera[numeroEquipo]);
        } else wait(espera[numeroEquipo]);
    }

    procedure finalizar(int numeroEquipo, int cant_ventas, int out total) {
        total_ventas[numeroEquipo] += cant_ventas;
        gruposFinalizados[numeroEquipo]++;

        if (gruposFinalizados[numeroEquipo] == 4) {
            signal_all(finalizado[numeroEquipo]);
        } else wait(finalizados[numeroEquipo]);


        total = total_ventas[numeroEquipo];
    }


}



```