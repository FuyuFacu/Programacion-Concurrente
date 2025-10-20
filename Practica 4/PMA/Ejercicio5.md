
a)

```java
chan colaImpresion(text);

process Administrativo[id: 1..N]
{
    while (true) {
        text textoAEnviar;
        send colaImpresion(textoAEnviar);
    }

}


Process Impresora[id: 1..3]
{
    text muestra;
    while (true) {
        receive canal_aviso() // Preguntar si esta bien hacer esto...
        receive colaImpresion(muestra);
        imprimirMuestra(muestra);
    }
}

```





b)
```java
chan CANAL_DOCUMENTO(documento, int);
chan CANAL_AVISAR(int);
chan CANAL_RECIBIR_DOCUMENTO[3](documento, int);
chan IMPRESION[N](impresion);

process Administrativo [Id: 1..N] {
		documento doc;
		impresion impr;
		// Los administrativos están continuamente trabajando
		while (True) {
				doc = HacerTarea();
				send CANAL_DOCUMENTO(doc, Id);
                sen CANAL_AVISAR(1);
		}
}

process Director {
    documento doc;
    impresion impr

    while (True) {
            doc = HacerTarea();
            send CANAL_DIRECTOR(doc, Id);
            send CANAL_AVISAR(1);
    }
}

process Coordinador {
    int idI, idAux;
    while (true) {
        receive CANAL_AVISO();
        receive CANAL_IMPRESORA(idI)

        if (not CANAL_DIRECTOR.isEmpty()) -> {
            receive CANAL_DIRECTOR(documento, idD);
        } 
        [] (not CANAL_DOCUMENTO.isEmpty() && CANAL_DIRECTOR.isEmpty()) -> {
            receive CANAL_DOCUMENTO(documento, idAux);
        }

        send CANAL_RECIBIR_DOCUMENTO[idAux](documento, idAux);
    }
}

process Impresora [Id: 1..3] {
		int id_cli;
		documento doc;
		impresion impr;
		while (True) {
				send CANAL_AVISO_IMPRESORA(Id);
				receive CANAL_RECIBIR_DOCUMENTO[Id](id_cli, doc);
				impr = Imprimir(doc);
		}

}
```

c)
```java

chan colaImpresion(text);

process Administrativo[id: 1..N]
{
    repeat 10 {
        text textoAEnviar;
        send colaImpresion(textoAEnviar);
        send canal_aviso(1);
    }
}


Process Impresora[id: 1..3]
{
    text muestra;

    bool continuar = true;
    while (continuar) {
        send Consulta(id);
        receive Respuestas[id](resultado);
        if (resultado) -> {
            receive Muestras[id](muestra);
            imprimirMuestra(muestra);
        } 
        [] (resultado == false) {
            continuar = false;
        }
    }
}

Process Coordinador() {
    int cant_impresiones = 0;
    int total_impresiones = N*10;

    while (not finalizado) {
        receive canal_aviso();
        receive Consulta(idI);

        if (cant_impresiones <= total_impresiones) -> {
            receive colaImpresion(muestra);
            send Muestras[idI](muestra);
            send Respuestas[idI](true);

            cant_impresiones++;
        } 
        [] (cant_impresiones > total_impresiones) -> {
            finalizado = true;
            for i := 1 to 3 {
                send Respuestas[i](false);
            }
        }

    }
}
```

d) // no creo que haya busy waiting, asi esta bien creo

```java
chan CANAL_DOCUMENTO(documento, int);
chan CANAL_AVISAR(int);
chan CANAL_RECIBIR_DOCUMENTO[3](documento, int);
chan IMPRESION[N](impresion);
chan Respuesta[1..3](boolean);

process Administrativo [Id: 1..N] {
        repeat 10 {
            documento doc;
            impresion impr;
            // Los administrativos están continuamente trabajando
            while (True) {
                    doc = HacerTarea();
                    send CANAL_DOCUMENTO(doc, Id);
                    sen CANAL_AVISAR(1);
            }
        }
}

process Director {
    repeat 10 {
        documento doc;
        impresion impr

        while (True) {
                doc = HacerTarea();
                send CANAL_DIRECTOR(doc, Id);
                send CANAL_AVISAR(1);
        }
    }
}

process Coordinador {
    int idI, idAux;
    int cant_impresiones = 0;
    int maximo_impresiones = N*10 + 10; // +10 por el director 

    while (continuar) {
        receive CANAL_AVISO();
        receive CANAL_IMPRESORA(idI)

        if (cant_impresiones <= maximo_impresiones) -> {
            if (not CANAL_DIRECTOR.isEmpty()) -> {
                receive CANAL_DIRECTOR(documento, idD);
            } 
            [] (not CANAL_DOCUMENTO.isEmpty() && CANAL_DIRECTOR.isEmpty()) -> {
                receive CANAL_DOCUMENTO(documento, idAux);
            }
        }
        [] (cant_impresiones > maximo_impresiones) -> {
            continuar = false;
            for i := 1 to 3 {
                send Respuesta[i](false);
            }
        }

        send CANAL_RECIBIR_DOCUMENTO[idAux](documento, idAux);
    }
}

process Impresora [Id: 1..3] {
		int id_cli;
		documento doc;
		impresion impr;
        boolean continuar = true;
        boolean respuesta;
		while (continuar) {
				send CANAL_AVISO_IMPRESORA(Id);
                receive Respuesta[Id](respuesta);
                if (respuesta) -> {
                    receive CANAL_RECIBIR_DOCUMENTO[Id](id_cli, doc);
                    impr = Imprimir(doc);
                }
                [] (respuesta == false) -> continuar == false;
		}
}

```