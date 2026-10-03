# Primer Parcial - 2da Fecha

## 1. SEMÁFOROS

Resolver con **SEMÁFOROS** el siguiente problema.

La Clave Única de Identificación Tributaria (CUIT) es una clave que se utiliza en el sistema tributario de la República Argentina para poder identificar correctamente a las personas físicas o jurídicas. Consta de un total de once (11) cifras numéricas, siendo la última un dígito verificador (del 0 al 9). Una empresa cuenta con una lista de CUITs que debe procesar, debiendo informar la cantidad de CUITs por dígito verificador. Para ello, dispone de un software que emplea 5 workers, los cuales trabajan colaborativamente procesando de a una CUIT por vez cada uno. Al finalizar el procesamiento, el último worker en terminar debe informar los resultados del procesamiento.

> **Notas:** la función `obtenerDV(CUIT)` retorna el dígito verificador para la CUIT recibida como entrada. La lista de CUITs se almacena como una cola global y la solución debe maximizar la concurrencia.

### Resolución (versión 1)

```cpp
int termine;
int sumatoriaDVs[10];
sem mutex = 1;
sem suma_mutex[10] = ([10],1);
sem Informar = 1;
Cola colaCUITs;

Process worker[id: 0 .. 4] {
    int CUIT;
    int dv;
    P(mutex);
    while(!colaCUITs.isEmply()) {
        CUIT = colaCUITs.pop();
        V(mutex);
        dv = obtenerDV(CUIT)
        P(suma_mutex[dv]);
        sumatoriaDVs[dv]++;
        V(suma_mutex[dv]);
        P(mutex);
    }
    V(mutex)

    P(Informar);
    termine++;
    if(termine == 5) {
        for i: 0 .. 9 {
            Imprimir(sumatoriaDVs[i]);
        }
    }
    V(Informar);

}
```

### Resolución (versión 2)

```cpp
Cola cola;
sem mutex_cola = 1, suma= 1, termine[10] = ([10],1);
int lista_dv[10] = ([10],0), termine = 0;

Process Worker[id: 0 .. 4] {
    int cuit, dvM;

    P(mutex_cola);
    while (!cola.isEmply()) {
        cuit = cola.pop();
        dv = obtenerDV(cuit);
        V(mutex_cola);

        P(mutex_suma[dv]);
        lista_dv[dv]++;
        V(mutex_suma[dv]);

        P(mutex_cola);
    }
    V(mutex_cola);

    P(suma);
    termine++;
    if(termine == 5) {
        for i: 0 .. 4 {
            writeln(lista_dv[i]);
        }
    V(suma);
}
```

---

## 2. MONITORES

Resolver con **MONITORES** la siguiente situación.

En un negocio hay UN empleado que diseña tarjetas digitales. El empleado debe atender los pedidos de C clientes, de acuerdo con el orden en que se hacen los pedidos. El cliente envía las indicaciones, y el empleado en base a eso diseña la tarjeta y se la envía al cliente.

> **Notas:** maximizar la concurrencia; existe una función `HacerTarjeta(indicaciones)` que simula el armado de la tarjeta por parte del empleado; todos los procesos deben terminar su ejecución.

### Resolución (versión 1)

```cpp
Process Cliente[id: 0 .. C] {
    text indic = " .. ";
    text tarj;
    admin.pedido(id,indic,tarj);
}

Process Empleado {
    text tarj;
    for i: 0 .. C-1 {
        admin.sig(id,indicaciones);
        tarj = HacerTarjeta(indicaciones);
        admin.salir(id,tarj);
    }
}

Monitor admin {
    Cond atendeme, espera;
    text tarjetas[C];
    Cola cola;

    procedure pedido(id: IN int, indic IN text, tarj OUT text) {
        Cola.push((id,indic));
        signal(atendeme);
        wait(espera);
        tarj = tarjetas[id];
    }

    procedure sig(id: OUT int, indic OUT text) {
        if(Cola.isEmply()) wait(atendeme);
        Cola.pop(id,indic);
    }

    procedure salir(id: IN int, tarj IN text) {
        tarjetas[id] = tarj;
        signal(espera);
    }

}
```

### Resolución (versión 2)

```cpp
Process Cliente[id: 0 .. C-1] {
    text indicaciones = "...";
    text tarjeta = "...";
    Admin.pedido(id, indicaciones, tarjeta);
}

Process Empleado {
    text instruccion;
    text resultado;
    int id_Cli;
    for i: 0 .. C-1 {
        Admin.sig(id_Cli,instruccion);
        resultado = HacerTarjeta(instruccion);
        Admin.resul(id_Cli,resultado);
    }
}

Monitor Admin {

    Cola cola;
    cond atender, resultado;
    text tarjetas[C];

    procedure pedido(id: IN int, indica: IN text, tarjeta: OUT text) {
        cola.push((id,indica));
        signal(atender);
        wait(resultado);
        tarjeta = tarjetas[id]
    }

    procedure sig(id: OUT int, instruc: OUT text) {
        if(cola.isEmply) wait(atender);
        cola.pop(id,instruc);
    }

    procedure resul(id: IN int, resul: IN text)
        tarjetas[id] = tarjeta_aux;
        signal(resultado);
    }

}
```
