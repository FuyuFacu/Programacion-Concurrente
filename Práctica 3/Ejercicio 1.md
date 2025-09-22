**Respuesta:** 
**Sí, el código funciona correctamente.**  
Utiliza una variable `cant` como semáforo binario (0: libre, 1: ocupado).  
Si el puente está ocupado (`cant > 0`), el proceso espera en la condición `cola`.  
Al liberar el puente (`salirPuente()`), se decrementa `cant` y se hace `signal(cola)` para despertar a otro proceso.


b)
Si, se podría simplificar, se podría no utilizar monitores, menos procedimientos no creo si dejáramos el monitor, se podría pero no se respetaría el orden de llegada como el otro código original.

**Código sin monitor:**
```java
sem mutex = 1;

Process Auto [a: 1..M]
{
	P(mutex);
	usarPuente();
	V(mutex);
}
```

**Código con monitor (no respeta orden de llegada):**
```java
Monitor Puente
cond cola;
bool libre = true;

procedure entrarPuente() {
    if (!libre)
        wait(cola);
    libre = false;
}

procedure salirPuente() {
    libre = true;
    signal(cola);
}

End Monitor;

