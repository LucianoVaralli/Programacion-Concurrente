# Parcial 2022

## 1. SEMÁFOROS

Existen 15 sensores de temperatura y 2 módulos centrales de procesamiento. Un sensor mide la temperatura cada cierto tiempo (función `medir()`), la envía al módulo central para que le indique qué acción debe hacer (un número del 1 al 10) (función `determinar()` para el módulo central) y la hace (función `realizar()`). Los módulos atienden las mediciones por orden de llegada.

### Resolución

```cpp
sem mutex_cola = 1;
sem atendeme = 0;
sem espera[S] = ([S],0);
int hacer[S];

Process sensor[id: 0 .. 14] {
    int temperatura;
    while(true) {
        temperatura = medir();
        P(mutex_cola);
        cola.push((id,temperatura));
        V(mutex_cola);
        V(atendeme);
        P(espera[id]);
        realizar(hacer[id]);
    }
}

Process modulo[id: 0 .. 1] {
    int temp, id_S;
    while(true) {
        P(atendeme);
        P(mutex_cola);
        cola.pop(temp,id);
        V(mutex_cola);
        hacer[id_S] = determinar(temp);
        V(espera[id_S]);
    }
}
```

---

## 2. SEMÁFOROS

Resolver con **SEMÁFOROS** el siguiente problema.

En un restorán trabajan C cocineros y M mozos. De forma repetida, los cocineros preparan un plato y lo dejan listo en la bandeja de platos terminados, mientras que los mozos toman los platos de esta bandeja para repartirlos entre los comensales. Tanto los cocineros como los mozos trabajan de a un plato por vez. Modele el funcionamiento del restorán considerando que la bandeja de platos listos puede almacenar hasta P platos. No es necesario modelar a los comensales ni que los procesos terminen.

### Resolución

```cpp
sem Listo = 1;
sem mutex_C = 1;
sem mutex_M = 1;
sem PlatoListo = 0;
int listos = 1;
int Usando = 1;
int platos[P];

Process cocinero[id: 0 .. C-1] {
    int plato;
    while(true) {
        plato = CocinandoPlato();
        P(Listo);
        platos[listos] = plato;
        P(mutex_C);
        listos = (listos + 1) mod P;
        V(mutex_C);
        V(PlatoListo);
    }
}

Process mozos[id: 0 .. M-1] {
    while(true) {
        P(PlatoListo);
        platos[Usando] = plato;
        P(mutex_M);
        Usando = (Usando + 1) mod P;
        V(mutex_M);
        V(Listo);
    }
}
```

---

## 3. MONITORES

Una boletería vende E entradas para un partido, y hay P personas (P>E) que quieren comprar. Se las atiende por orden de llegada y la función `vender()` simula la venta. La boletería debe informarle a la persona que no hay más entradas disponibles o devolverle el número de entrada si pudo hacer la compra.

### Resolución

```cpp
// (sin resolución)
```

---

## 4. MONITORES

Por un puente turístico puede pasar sólo un auto a la vez. Hay N autos que quieren pasar (función `pasar()`) y lo hacen por orden de llegada.

### Resolución

```cpp
// (sin resolución)
```
