# Taller Sincronización — Patrón de Barrera

**Curso:** ARSW  
**Estudiante:** Brayan Loaiza  

---

## Descripción

Este laboratorio implementa el patrón de sincronización por barrera en Java.
El programa ejecuta N hilos que realizan una tarea a velocidades distintas.
El objetivo es calcular el tiempo promedio de ejecución **solo cuando el último hilo haya terminado**.

---

## Punto 2 — Ejecución sin sincronización

### ¿Qué hace el programa original?

El hilo principal inicia los 20 hilos y de inmediato intenta leer sus resultados,
sin esperar a que terminen. Como los hilos apenas empezaron, `resultado` vale `0` en todos.

### Evidencia

![Ejecución sin sincronización](img/punto2.png)

### Resultado obtenido

```
El tiempo promedio de la ejecución fue de: 0
```

### ¿Por qué es incorrecto?

El hilo principal no espera a que los hilos trabajadores terminen. Cuando llama
`getResultado()`, los hilos aún están ejecutándose y su campo `resultado` no ha sido
asignado, por lo que devuelve `0`. El promedio de veinte ceros es cero.

---

## Punto 3 — Solución: sincronización por barrera con `join()`

Se agregó un segundo ciclo entre el `start()` y la lectura de resultados.
Cada llamada a `hilos[i].join()` hace que el hilo principal se pause hasta que
ese hilo termine su ejecución. El principal solo avanza cuando **todos** han terminado.

```java
for (int i = 0; i < numHilos; i++) {
    hilos[i].start();
}

// Barrera: espera a que todos los hilos terminen
for (int i = 0; i < numHilos; i++) {
    try {
        hilos[i].join();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

for (int i = 0; i < numHilos; i++) {
    tiempoPromedio += hilos[i].getResultado();
}
```

---

## Punto 4 — Verificación con sincronización

### Evidencia

![Ejecución con sincronización](img/punto4.png)

### Resultado obtenido

```
El tiempo promedio de la ejecución fue de: XXXX
```

### ¿Por qué es correcto ahora?

El hilo principal se bloquea en el ciclo de `join()` hasta que el último hilo
termina. Solo entonces lee los valores de `resultado`, que ya han sido asignados
correctamente por cada hilo al final de su ejecución.

---

## Conclusión

El patrón de barrera garantiza que un punto de sincronización sea alcanzado por
todos los participantes antes de continuar. En Java, `Thread.join()` es la
implementación más directa de este patrón para un conjunto fijo de hilos.
