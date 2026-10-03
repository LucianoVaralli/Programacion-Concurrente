# Primer Parcial - 3ra Fecha

## 1. SEMÁFOROS

Existe una sala de cine 3D, a las que asisten N personas a ver una película. Antes de entrar a la sala, los asistentes deben retirar los anteojos 3D en la máquina repartidora que se encuentra en la entrada. Se debe simular el uso de la máquina repartidora de anteojos 3D, con capacidad para A anteojos (A < N). Además, existe un repositor encargado de reponer los anteojos en la máquina cuando se agotan. Los usuarios usan la máquina según el orden de llegada. Cuando les toca usarla, sacan un par de anteojos y luego se dirigen a la sala. En caso de que la máquina se quede sin anteojos, entonces le debe avisar al repositor para que cargue nuevamente la máquina en forma completa. Luego de la recarga, saca un par de anteojos y se retira. Implemente un programa que permita resolver el problema anterior usando **SEMÁFOROS**.

> **Nota:** maximizar la concurrencia; la reposición de anteojos no debe impedir que otros asistentes puedan agregarse a la fila.

### Resolución

```cpp
int anteojos = A;
Sem mutex = 1;
Sem sem_repositor = 0;
Sem termine = 0;

Process asistente[id: 0 .. N-1] {

    P(mutex);
    if(cantLentes == 0) {
        V(sem_repositor);
        P(termine);
    }
    cantLentes++;
    V(mutex);

}

Process repositor {
    while(true) {
        P(sem_repositor);
        cantLentes = A;
        V(termine);
    }
}
```

---

## 2. MONITORES

En el Registro de la Propiedad se pueden realizar 4 trámites administrativos diferentes. Para cada trámite, hay un puesto de atención específico. Existen 100 personas que se dirigen a la oficina para resolver un trámite particular. La persona deja su trámite en el puesto correspondiente y espera a que le entreguen el resultado. El puesto atiende a las personas que le corresponden de acuerdo con el orden de llegada. Implemente un programa que permita resolver el problema anterior usando **MONITORES**.

> **Notas:** maximizar la concurrencia; todos los procesos deben terminar; la función `obtenerPuesto()` retorna el número de puesto al que la persona debe dirigirse para su trámite; la función `obtenerTrámite()` retorna el trámite a realizar; la función `procesarTrámite(t)` procesa el trámite recibido como entrada y retorna su resultado.

### Resolución

```cpp
Process persona[id: 0 .. 99] {
    int puesto = obtenerPuesto();
    text tramite = obtenerTrámite();
    text resultado = " .. ";
    admin[puesto].realizarTramite(id, tramite, resultado);
}

Process empleado[id: 0 .. 3] {
    text tramite;
    text resultado;
    id id_cli;
    for i: 0 .. 99 {
        admin[id].sig(id_cli,tramite);
        resultado = procesarTrámite(tramite);
        admin[id].termino(id_cli,resultado)
    }
}

Monitor admin[id: 0 .. 3] {
    cond llegue, termine;
    text resultados[100];
    Cola cola;

    procedure realizarTramite(id_p: IN int, t: IN text, res: OUT text) {
        cola.push((id,t));
        signal(llegue);
        wait(termine);
        res = resultados[id];
    }

    procedure sig(id: OUT int, t: OUT text) {
        if(!cola.isEmply()) wait(llegue);
        cola.pop(id,tramite);
    }

    procedure termino(id: IN int, res: IN text) {
        resultados[id] = res;
        signal(termine);
    }

}
```
