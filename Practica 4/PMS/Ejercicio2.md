

2)
```java // esta mal, hay que generar 3 procesos distintos, ya que el id 1 se queda esperando a que reciba la muestra el id 2 cuando deberia enviar la muestra y seguir preparando


process EmpleadoPrimero {
    while (true) {
        ADN a = prepararMuestras();
        Administrador!enviomuestra(a);
    }
}

process EmpleadoSegundo {
    text Resultado;
    while (true) {
        ADN aux;
        Administrador!pedido();
        Administrador?enviar(aux);
        analisis a = prepararSetAnalisis(aux);
        EmpleadoTercero!analisis(a);
        EmpleadoTercero?resultado(Resultado);
    }
}

process EmpleadoTercero {
    while (true) {
        analisis a;
        EmpleadoSegundo?analisis(a);
        text Resultado = realizarAnalisis(a);
        EmpleadoSegundo!resultado(Resultado);
    }
}

process Administrador {
    cola Fila;

    while (true) {
        // recordar hacer sentencia de entrada, o sea EmpleadoSegundo?pedido() 
        if (not Fila.isEmpty()); EmpleadoSegundo?pedido() -> {
            ADN a = Fila.pop();
            EmpleadoSegundo!enviar(a);
        }
        [] EmpleadoPrimero?enviomuestra(a) -> {
            Fila.push(a);
        }
    }
}
```