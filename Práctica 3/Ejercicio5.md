Date: 2025-09-20
Course: [[programacion_concurrente]]
Tags: [[monitores]], [[Practica 3 (Concurrencia)]]

# Enunciado
En un **corralón de materiales** se deben atender a **N clientes** de acuerdo con el orden de llegada. Cuando un cliente es llamado para ser atendido, **entrega una lista con los productos que comprará**, y espera a que alguno de los empleados le entregue el comprobante de la compra realizada.

**a)** Resuelva considerando que el corralón tiene un único empleado.
**b)** Resuelva considerando que el corralón tiene E empleados (E > 1). Los empleados no deben terminar su ejecución.
**c)** Modifique la solución (b) considerando que los empleados deben terminar su ejecución cuando se hayan atendido a todos los clientes.
# Respuesta

```java

Monitor Corralon
{
    cola colaEspera;
    cond atendidos[N];
    Comprobante comprobantes[N];
    cond hayClientes;

    procedure entradaCliente(int id, Lista productos, out Comprobante c) {
        push(colaEspera, (id, productos));
        signal(hayClientes); //Revisar si es correcto hacer esto
        wait(atendidos[id]);
        c = comprobantes[id];
    }

    procedure esperarCliente(out int idAux, out Lista productos) {
        while (colaEspera.isEmpty()) 
            wait(hayClientes);
        (idAux, productos) = pop(colaEspera);
    }

    procedure entregarComprobante(int id, Comprobante c) {
        comprobantes[id] = c;
        signal(atendidos[id]);
    }
}


Process Cliente[id: 1..N]
{
    Lista productos;
    Comprobante c;
    Corralon.entradaCliente(id, productos, c);
}

Process Empleado
{ while(true) 
    {
        int idAux;
        Lista productos;
        Comprobante c;

        Corralon.esperarCliente(idAux, productos);
        c = realizar_comprobante(productos);
        Corralon.entregarComprobante(idAux, c);
    }
}

```

# Resumen
Implemento un monitor llamado `Corralon` que coordina la interacción entre clientes y un empleado. Cada cliente entra al monitor dejando así su lista de productos en una cola de espera y luego se bloquea en su condición hasta que recibe su comprobante. El empleado, en este caso, espera a que haya clientes disponibles, y cuando los hay, toma el primer cliente de la cola, procesa su pedido y genera el comprobante, el cual entrega señalando la condición del cliente que lo espera. La sincronización se maneja dentro del monitor usando `wait` y `signal`, Logrando así que los clientes se atiendan en orden de llegada y reciban su comprobante, mientras que el empleado no queda bloqueado de manera innecesaria y atiende continuamente los pedidos.
