# Primer Parcial - 1ra Fecha

## 1. SEMÁFOROS

Se debe simular el uso de un sistema virtual de venta de entradas para un evento musical. El sistema cuenta con C cajeros virtuales que atienden indefinidamente. Sin embargo, como la venta de entradas comienza a una hora determinada, sólo atienden a partir del aviso de un Timer. Una vez que reciben dicho aviso, los cajeros atienden de acuerdo con el orden de llegada de los compradores. La atención consiste en recibir la solicitud del comprador (datos para el pago) y responderle si pudo comprar (o no) junto al comprobante de la operación. Para este evento se cuenta con E entradas y N compradores, donde cada comprador puede solicitar a lo sumo una entrada. Resuelva usando **SEMÁFOROS**.

### Resolución

```cpp
Cola cola;
Cola respuesta;
sem mutex_cola = 1;
sem mutex_entradas = 1;
sem llegue = 0;
sem arranco[C] = ([C],0);
sem esperando_atencion[N] = ([N],0);

int entradas = E;
bool resp_pudo[N];
Comprobante resp_comp[N];


Process cajero[id: 0 .. C-1] {
    int id_comprador;
    double pago_comprador;
    bool pudo_aux;
    Comprobante comp_aux;

    P(arranco[id]);

    While(true) {
        P(llegue);

        P(mutex_cola);
        cola.pop(id_comprador,pago_comprador);
        V(mutex_cola);

        P(mutex_entradas);
        if (entradas > 0) {

            pudo_aux = procesarPago(pago_comprador);
            if (pudo_aux == true) {
                entradas--;
                comp_aux = "Ticket y Recibo";
            }
        } else {

            pudo_aux = false;
            comp_aux = "Agotado";
        }
        V(mutex_entradas);

        resp_pudo[id_comprador] = pudo_aux;
        resp_comp[id_comprador] = comp_aux;

        V(esperando_atencion[id_comprador]);

    }
}

Process comprador[id: 0 .. N-1] {
    double pago = Pago();
    Comprobante mi_comp;
    bool pude_comprar;

    P(mutex_cola);
    cola.push(id,pago);
    V(mutex_cola);

    V(llegue);
    P(esperando_atencion[id]);

    pude_comprar = resp_pudo[id];
    mi_comp = resp_comp[id];

}

Process Reloj {
    wait();
    for i: 0 .. C-1 {
        V(arranco[i]);
    }
}
```

---

## 2. MONITORES

Existen N personas que desean acceder a un mirador al borde del lago Nahuel Huapi en Bariloche. Como el mirador es angosto, sólo puede ser usado por una persona a la vez. Resuelva con **MONITORES** los dos casos siguientes:

### a) El acceso al mirador es por orden de llegada

```cpp
Process persona[id 0..N-1] {
    admin.llegue();
    //mirando
    admin.sali();
}

Monitor admin {
    cond espernado;
    int esperando = 0;
    bool libre = libre;

    procedure llegue() {
        if(!libre) {
            esperando++;
            wait(esperando);
        } else {
            libre = false;
        }
    }

    procedure sali() {
        if(esperando > 0) {
            esperando--;
            signal(esperando);
        } else {
            libre = true;
        }
    }

}
```

### b) El acceso al mirador es por orden de llegada, pero dando prioridad a los mayores de 60 años

```cpp
Process persona[id 0..N-1] {
    admin.llegue(id,edad);
    //mirando
    admin.sali(id);
}

Monitor admin {
    Cola colaMayores, colaMenores;
    cond espernado[N];
    int esperando = 0;
    bool libre = libre;

    procedure llegue(id: in int, edad: in int) {
        if(!libre) {
            if (edad >= 60) {
                colaMayores.push(id);
            } else {
                colaMenores.push(id);
            }
            esperando++;
            wait(esperando[id]);
        } else {
            libre = false;
        }
    }

    procedure sali() {
        if(esperando > 0) {
            int id_aux;
            if(!colaMayores.isEmply()) {
                colaMayores.pop(id_aux);
            } else {
                colaMenores.pop(id_aux);
            }
            esperando--;
            signal(esperando[id_aux]);
        } else {
            libre = true;
        }
    }

}
```
