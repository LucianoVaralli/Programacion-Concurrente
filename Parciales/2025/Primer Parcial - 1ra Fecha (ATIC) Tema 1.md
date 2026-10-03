1.

char A[1000000];
sem mutex_total[2] = ([2],1);
int cantBuscar = 1000000 div 4;

process Worker[id: 0 .. 3] {
    int totales[2] = ([2],0) // posicion 0 para F y posicion 1 para C
    int ini = id * cantBuscar;
    int fin = (ini + cantBuscar - 1)
    for i: ini .. fin {
        if(a[i] == "F") {
            P(mutex_total[id]);
            totales[i]++;
            V(mutex_total[id]);
        } else {
            P(mutex_total[id]);
            totales[i]++;
            V(mutex_total[id]);
        }
    }
    P(mutex);
    termine++;
    if (termine == 4) {
        for j: 0 .. 3 {
            V(listo);
        }
    }
    V(mutex);
    P(listo);
    writeln("F: " + totales[0] + " C: " + totales[1]); // Imprime cada uno su cantidad
}



3.

process Camion[id: 0 ... 29] {
    int contenido = ..; // 0 para maiz y 1 para girasol
    administracion[contenido].Llegue();
    //depositando
    administracion[contenido].Saliendo();
}

process Empleado[id: 0 .. 1] {
    int contenido = ..; // 0 para maiz y 1 para girasol se elige 1 por cada empleado
    for i: 0 .. 14 {
        administracion[contenido].atender();
    }
}

monitor administracion[id: 0 .. 1] {
    cond atendeme, continua, termine
    int esperando = 0;

    procedure Llegue() {
        esperando++
        signal(atendeme);
        wait(continua);
    }

    procedure atender() {
        if(esperando == 0) wait(atendeme)
        esperando --
        signal(continua);
        wait(termine);
    }

    procedure Saliendo() {
        signal(termine);
    }

}