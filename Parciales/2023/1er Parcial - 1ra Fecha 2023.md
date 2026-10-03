1. Resolver con SEMÁFOROS los problemas siguientes:

a) En una estación de trenes, asisten P personas que deben realizar una carga de su tarjeta SUBE, en la terminal disponible. La terminal es utilizada en forma exclusiva por cada persona de acuerdo con el orden de llegada. Implemente una solución utilizando únicamente procesos Persona. Nota: la función UsarTerminal() le permite cargar la SUBE en la terminal disponible.

sem espera[P] = ([P],0), mutex = 1;
bool libre = true;
Cola cola;

Process personas[id: 0 .. P-1] {

    P(mutex)
    if(!libre) {
        cola.push(id);
        V(mutex);
        P(espera[id]);
    } else {
        libre = false;
        V(mutex);
    }

    UsarTerminal()

    P(mutex)
    if(!cola.isEmply() {
        int aux = cola.pop()
        V(espera[aux]);
    } else {
        libre = true;
    }
    V(mutex);

}

b) Resuelva el mismo problema anterior pero ahora considerando que hay T terminales disponibles. Las personas realizan una única fila y la carga la realizan en la primera terminal que se libere. Recuerde que sólo debe emplear procesos Persona. Nota: la función UsarTerminal(t) le permite cargar la SUBE en la terminal t.

sem espera[P] = ([P],0), mutex = 1;
Cola colaP, libres;
int terminales[P];

Process personas[id: 0 .. P-1] {
    int t;
    P(mutex)
    if(libres.isEmply()) {
        colaP.push(id);
        V(mutex);
        P(espera[id]);
        //  guardo la terminal que me asignaron
        t = terminales[id];
    } else {   
        t = libres.pop();
        V(mutex);
    }

    UsarTerminal(t);

    P(mutex)
    if(!colaP.isEmply()) {
        int aux = colaP.pop()
        terminales[aux] = t;
        V(espera[aux]);
    } else {
        libres.push(t);
    }
    V(mutex);

}


2. Resolver con MONITORES el siguiente problema. En una elección estudiantil, se utiliza una máquina para voto electrónico. Existen N Personas que votan y una Autoridad de Mesa que les da acceso a la máquina de acuerdo con el orden de llegada, aunque ancianos y embarazadas tienen prioridad sobre el resto. La máquina de voto sólo puede ser usada por una persona a la vez. Nota: la función Votar() permite usar la máquina.

Process personas[id: 0 .. N-1] {
    int edad = "..";
    bool embarazada = " .. ";
    admin.llegue(id,edad,embarazada);
    Votar()
    admin.termine();
}

Process Autoridad {
    for i: 0 .. N-1 {
        admin.atender();
    }
}

Monitor admin {
    ColaOrdenada cola;
    cond atendeme, usando, esperando[N]

    Procedure llegue(id: IN int, edad: IN int, estoyEmbarazada: IN bool) {
        cola.push((id,edad,estoyEmbarazada));
        signal(atendeme);
        wait(esperando[id]);
    }

    Procedure atender() {
        int id_P;
        if (cola.isEmply()) wait(atendeme);
        cola.pop(id_P);
        signal(esperando[id_P]);
        wait(usando);
    }

    procedure termine() {
        signal(usando);
    }
}