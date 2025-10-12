3)
```java

chan Pedido(int);
chan Siguiente[3]; // porque son 3 vendedores
chan consulta(texto, int);
chan comidas(texto, int);

Process Cliente[id: 1..C] 
{
    Comida comida;
    text ped;
    send consulta(ped, id);
    receive(entrega[id](comida))
}

Process Cocinero[id: 1..2]
{
    texto receta;
    int idCliente;

    while (true) {
        receive comidas(receta, idCliente);
        Plato platoComida = RealizarPlato(receta);
        send entrega[idCliente](platoComida);
    }
}

Process Vendedor[id: 1..3]
{
    texto Rep;
    int idP;

    while (true) {
        send Pedido(id);
        receive Siguiente[id](Rep, idC);
        if (Rep <> "VACIO") {
            send comida(Rep, idC);
        }
        else delay (180); // esto simula que estan reponeindo un pack de bebidad de la heladera. 
                         // Elegi el peor caso: 3 minutos
    }
}

Process Coordinador
{
    texto Rep;
    int idV, idC;
    while (true)
    {
        receive Pedido(idV);
        if (Empty(consulta)) Rep = "VACIO";
        else receive consulta(Rep, idC);

        send Siguiente[idV](Rep, idC);
    }

}

```