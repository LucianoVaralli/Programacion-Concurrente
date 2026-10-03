# 1er Parcial - 1ra Fecha 2023 (ATIC)

## 1. SEMÁFOROS

Resolver con **SEMÁFOROS** el siguiente problema.

Para un experimento se tiene una red con 15 controladores de temperatura y dos módulos centrales. Los controladores cada cierto tiempo toman la temperatura mediante la función `medir()` y la envía para que alguna de las centrales le indique qué debe hacer (número de 1 a 10), y luego realiza esa acción mediante la función `actuar()`. Las centrales atienden los pedidos de los controladores de acuerdo al orden de llegada, usando la función `determinar()` para determinar la acción que deberá hacer ese controlador (número de 1 a 10).

> **Nota:** el tiempo que espera cada controlador para tomar nuevamente la temperatura empieza a contar después de haber ejecutado la función `actuar()`.

### Resolución

```cpp
sem mutex_tem=1, avisar=0, esperando[15] = ([15],0);
Cola cola;
int relizar[15];

Procces controlador[id: 0 .. 14] {
    int temperatura
    int miAccion;
    while(true){
        temperatura = medir();

        P(mutex_tem);
        cola.push((id,temperatura))
        V(mutex_tem);
        V(avisar);
        P(esperando[id]);
        mi_accion = acciones[id];
        actuar(mi_accion);
        delay();
    }
}

Process modulo[id: 0 .. 1] {
    int id_contro, temp;
    while(true) {
        P(avisar);

        P(mutex_tem);
        cola.pop((id_contro,temp))
        V(mutex_tem);

        int rea = determinar(temp); /guarda el numero del 1 al 10
        relizar[id_contro] = rea;

        V(esperando[id_contro]);
    }
}
```

---

## 2. MONITORES

Resolver con **MONITORES** el siguiente problema.

Hay una boletería virtual que vende en forma online E entradas para un partido de fútbol a P personas (P > E) de acuerdo con el orden de llegada. Cuando la boletería atiende a una persona, si aún quedan entradas disponibles le envía el número de entrada vendida, sino le indica que no hay más entradas.

> **Nota:** suponga que existe la función `vender()` que simula la venta de la entrada.

### Resolución

```cpp
Process persona[id: 0 .. P-1] {
    int numEntrada;
    boleteria.llegue(id,numEntrada);

    if(numEntrada <> -1) {
        //consegui entrada
    } else {
        //no consegui entrada
    }
}

Process Empleado {
    int entradas = E
    int numE, id_P;
    for i: 0 .. P-1 {
        Admin.sig(id_P);
        if (entradas > 0) {
            numE = vender();
            entradas--;
        } else {
            numE = -1; // -1 funciona como bandera de "no hay entradas"
        }
        Admin.salir(id_P, numE)
    }
}

Monitor Admin {
    cond llegue, esperando;
    int Entradas[P];
    Cola cola

    procedure llegue(id: IN int, numE: OUT int) {
        cola.push(id);
        signal(llegue);
        wait(esperando);
        numE = Entradas[id];
    }

    procedue sig(id: OUT int) {
        if(cola.isEmply()) wait(llegue);
        cola.pop(id);
    }

    procedure salir(id: IN int, numE: IN int) {
        Entradas[id] = numE;
        signal(esperando);
    }

}
```

---

## 3. MONITORES

Resolver con **MONITORES** la siguiente situación.

En un camino turístico hay un puente por donde puede pasar un vehículo a la vez. Hay N autos que deben pasar por él de acuerdo con el orden de llegada.

> **Nota:** sólo se pueden usar los procesos Autos (y los monitores que sean necesarios); suponga que existe la función `pasar()` que simula el paso del auto por el puente.

### Resolución

```cpp
Process Auto[id: 0 .. N-1] {
    admin.llegue();
    pasar() //pasando
    admin.salir();
}

Monitor admin {
    bool libre = true;
    int esperando = 0;
    cond espera;

    procedure llegue() {
        if(!libre) {
            esperando++;
            wait(espera);
        } else {
            libre = false;
        }
    }

    procedure salir() {
        if(esperando > 0) {
            esperando--;
            signal(espera);
        } else {
            libre = true;
        }
    }

}
```
