1. Resolver con SEMÁFOROS el siguiente problema. En una planta verificadora de vehículos, existen 7 estaciones donde
se dirigen 150 vehículos para ser verificados. Cuando un vehículo llega a la planta, el coordinador de la planta le
indica a qué estación debe dirigirse. El coordinador selecciona la estación que tenga menos vehículos asignados en ese
momento. Una vez que el vehículo sabe qué estación le fue asignada, se dirige a la misma y espera a que lo llamen
para verificar. Luego de la revisión, la estación le entrega un comprobante que indica si pasó la revisión o no. Más allá
del resultado, el vehículo se retira de la planta. Nota: maximizar la concurrencia. 

Process vehiculo[id: 0 .. 149] {
    P(mutex_cola);
    cola.push(id);
    V(mutex_cola);
    V(llegue):

    P(espera_estacion[id]);

    int estacion = estaciones[id];

    P(mutex_estacion[estacion]);
    colaEstacion[estacion].push(id)
    V(mutex_estacion[estacion]):

    V(atenderme_estacion[estacion]);
    P(esperando_llamado[id]);

    text comprobante = comprobantes[id];

    P(mutex_estaciones);
    cant_estaciones[estacion_Min]--;
    V(mutex_estaciones);
}

Process coordinador {
    int id_V;
    int estacion_Min;
    while(true) {
        P(llegue);

        P(mutex_cola);
        id_V = cola.pop();
        V(mutex_cola);

        P(mutex_estaciones);
        estacion_Min = Min(cant_estaciones)
        cant_estaciones[estacion_Min]++;
        V(mutex_estaciones);

        estaciones[id_V] = estacion_Min;
        P(espera_estacion[id_V]);
    }
}

Process estacion[id: 0 .. 6] {
    int id_aux;
    while(true) {
        P(atenderme_estacion[id]);

        P(mutex_estacion[id]);
        id_aux = colaEstacion[id].pop();
        V(mutex_estacion[id]);

        comprobantes[id_aux] = VerificarVehiculo(id_aux);

        V(esperando_llamado[id_aux]);
    }
}

2. Resolver con MONITORES el siguiente problema. En un sistema operativo se ejecutan 20 procesos que
periódicamente realizan cierto cómputo mediante la función Procesar(). Los resultados de dicha función son
persistidos en un archivo, para lo que se requiere de acceso al subsistema de E/S. Sólo un proceso a la vez puede hacer
uso del subsistema de E/S, y el acceso al mismo se define por la prioridad del proceso (menor valor indica mayor
prioridad).


Process proceso[id: 0 .. 19] {
    int prioridad = Prioridad();
    int resul;
    while(true) {
        resul = Procesar(); 
        sistemaOperativo.llegue(id,prioridad);
        Persistir(resul);
        sistemaOperativo.salir();
    }
}

Monitor sistemaOperativo {

    Cola[int, int] cola;
    cond esperando[20];
    int esperando = 0
    bool libre = true;

    procedure llegue(id: IN int, prio: IN int) {
        
        if(!libre) {
            cola.push((id,prio));
            esperando++;
            wait(esperando[id]);
        } else {
            libre = false;
        }

    }

    procedure salir() {
        int id;
        if(esperando > 0) {
            cola.pop(id);
            esperando--;
            signal(esperando[id]);
        } else {
            libre = true;
        }

    }

}