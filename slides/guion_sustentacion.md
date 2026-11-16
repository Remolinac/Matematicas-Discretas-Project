# Guion de sustentación — 10 minutos, 3 integrantes

**Proyecto:** Despacho inteligente de buses de refuerzo en el Corredor Universitario (Cra. 51B)
**Curso:** Matemáticas Discretas (MAT 4021) · Prof. MSc Iván Gómez · Universidad del Norte
**Integrantes:** César Berrio · Samuel Ortega · Gabriela Campo

Elijan quién es la Persona 1, 2 y 3. Cada bloque está pensado para leerse en voz alta a ritmo tranquilo (≈130 palabras por minuto).

| Persona | Diapositivas | Tema | Tiempo |
|---|---|---|---|
| **Persona 1** | 1, 2 y 3 | Problema, impacto, objetivo y datos | ≈ 2:30 |
| **Persona 2** | 4, 5 y 6 | Modelo matemático, programa y validación | ≈ 3:15 |
| **Persona 3** | 7, 8 y demo | Resultados, conclusiones y demostración en vivo | ≈ 3:45 |
| | | **Total** | **≈ 9:30** (quedan 30 s de margen) |

> **Antes de empezar:** tengan `python main.py` **ya corriendo** con el navegador abierto en `http://localhost:8080` en otra ventana. Así la demo arranca sin esperas. La consola también queda con las tablas impresas por si el profesor pregunta.

---

## PERSONA 1 — Problema, impacto y datos (≈ 2:30)

### Diapositiva 1 · Portada (≈ 0:20)

> Buenos días, profesor. Somos César Berrio, Samuel Ortega y Gabriela Campo. Nuestro proyecto se llama **"Despacho inteligente de buses de refuerzo en el Corredor Universitario de la Carrera 51B"**. En pocas palabras: usamos grafos, centralidad de Freeman y un perceptrón para decidir a qué paraderos mandar buses extra mientras duran las obras de La Gran Vía.

### Diapositiva 2 · El problema (≈ 1:10)

> Empecemos por el problema. La Carrera 51B es el corredor que une Barranquilla con Puerto Colombia, y por ahí se mueven **más de 45.000 personas al día**. La mayoría somos nosotros: estudiantes, docentes y trabajadores de UniNorte, la Universidad del Atlántico, la Libre, la Antonio Nariño y varios colegios.
>
> Con las obras de La Gran Vía se cerraron calzadas en tramos clave como Portoazul y la zona de UniNorte. Según el Área Metropolitana de Barranquilla, en hora pico la espera pasó de **12–15 minutos a 35–50 minutos**, y **más del 65 % de las veces el bus llega lleno** y la gente no puede subir. Esto afecta las rutas de Sobusa, Coolitoral y el alimentador U30 de Transmetro.
>
> A la derecha está cómo cumplimos las cuatro condiciones de un problema de impacto: **afecta** a más de 45 mil personas; **hay evidencia** con datos del AMB; **se puede mejorar**, porque medimos espera y porcentaje de rechazo antes y después; y **se puede modelar** con un grafo, BFS, centralidad de Freeman y un perceptrón.

### Diapositiva 3 · Objetivo, datos y métrica (≈ 1:00)

> Nuestro objetivo es concreto: **decidir en qué paraderos conviene inyectar buses de refuerzo vacíos**. A esos paraderos les asignamos la clase y = 1. La decisión no depende solo de cuánta gente hay, sino también de **qué tan estratégico es el paradero dentro de la red**.
>
> Los datos: modelamos **15 paraderos reales** del corredor, con sus coordenadas, desde la Circunvalar hasta la entrada de Puerto Colombia, conectados por 20 tramos viales. Cada paradero tiene una **afluencia normalizada entre 0 y 1**, que es una estimación nuestra. Además armamos un **subgrafo de 6 nodos** que resolvimos a mano para validar el programa. Y simulamos 10 horas pico con 4 buses de refuerzo de 40 puestos.
>
> Para comparar antes y después medimos tres cosas: el **tiempo medio de espera**, el **porcentaje de pasajeros que no pueden subir** por sobrecupo y las **horas-pasajero ahorradas**. "Antes" es el despacho actual con frecuencia fija de 4 buses por hora; "después" es el refuerzo dirigido por nuestro modelo. Ahora mi compañero(a) explica el modelo matemático.

---

## PERSONA 2 — Modelo, programa y validación (≈ 3:15)

### Diapositiva 4 · Modelación (≈ 1:15)

> Gracias. Modelamos el corredor como un **grafo no dirigido G = (V, E)**: cada vértice es un paradero y cada arista es un tramo de vía por el que pasa el bus. A la izquierda está el subgrafo de 6 nodos. Fíjense en el rombo naranja: es un **ciclo C4**, que representa las dos calzadas paralelas alrededor de la zona de obras.
>
> A partir de la matriz de adyacencia A hacemos **BFS por capas** desde cada nodo. Eso nos da la matriz de distancias D y, algo clave, el número de caminos más cortos σ entre cada par.
>
> Con eso calculamos la **intermediación de Freeman**: para cada paradero v, sumamos qué fracción de los caminos más cortos entre todos los demás pares pasa por v. Si un paradero es "puente", su C_B es alto. Lo normalizamos dividiendo entre "n−1 en 2" para que quede entre 0 y 1.
>
> Luego entra el **perceptrón de Rosenblatt**. Recibe dos entradas: la intermediación normalizada C_B' y la afluencia x₂. Calcula z = w·x + b y, si z ≥ 0, responde 1, refuerzo; si no, 0. Cuando se equivoca corrige los pesos con la regla Δw = η·e·x. Usamos η = 0.5.

### Diapositiva 5 · Arquitectura del ejecutable (≈ 0:50)

> Así fluye el programa. **Entrada:** las aristas del grafo, la afluencia de cada paradero y si hay o no cierre vial. Luego el **BFS** calcula distancias y número de caminos; con eso, el módulo de **Freeman** saca las centralidades; el **perceptrón** clasifica cada paradero; y la **salida** es la lista de paraderos que necesitan refuerzo, junto con la simulación de antes y después.
>
> Todo está en **Python con NumPy**, y los algoritmos los escribimos nosotros, sin librerías de grafos ni de machine learning. El núcleo matemático está en `motor_discreto.py` y se ejecuta todo con un solo comando, `python main.py`. Ese comando imprime las matrices y tablas en consola y abre una interfaz web local que vamos a mostrar en la demo.

### Diapositiva 6 · Validación con resultado conocido (≈ 1:10)

> Para comprobar que el programa calcula bien, usamos el caso de 6 nodos, que **resolvimos primero a mano**. Por ejemplo, entre v1 y v4 hay **dos caminos más cortos de longitud 2**: uno pasa por v2 y otro por v3. Entonces cada uno recibe la mitad, 0.5. En total aparecen **8 aportes de 0.5**. v4, que es Makro, es el gran puente: recibe 0.5 más 6 caminos completos, o sea **6.5**, que normalizado entre 10 da **0.65**.
>
> En la tabla, la columna "esperado" es lo que calculamos a mano y la columna "programa" es lo que imprime el ejecutable: **coinciden en los 6 nodos**.
>
> El perceptrón también lo seguimos a mano: en la época 1 se equivoca 3 veces, corrige los pesos y en la **época 2 ya tiene error cero**. A la derecha está la recta que aprendió: **0.2·C_B' + 0.6·afluencia − 0.5 = 0**. Los cuadros rojos, UniNorte Puerta 4 y Makro, quedan arriba de la recta, que es la zona de refuerzo; los demás quedan abajo. Con esto validado, pasamos a la red completa.

---

## PERSONA 3 — Resultados, conclusiones y demo (≈ 3:45)

### Diapositiva 7 · Resultados en la red de 15 paraderos (≈ 1:10)

> Gracias. Aplicamos el perceptrón entrenado a los **15 paraderos reales**, y marcó refuerzo en cuatro: **Makro, UniNorte Puerta 4, Universidad Libre y Universidad del Atlántico**.
>
> En la gráfica, las barras grises son la espera actual y las naranjas la espera con refuerzo dirigido. En UniNorte Puerta 4, por ejemplo, la espera baja de **43.4 a 15.5 minutos**. En promedio, sobre los 15 paraderos, pasa de **28.8 a 12.4 minutos**, una reducción del **56.8 %**. El porcentaje de gente que no puede subir baja de **39.4 % a 20.3 %**, casi la mitad.
>
> En las 10 horas pico simuladas eso equivale a **2.526 pasajeros más que logran subir** y unas **3.394 horas-pasajero de espera ahorradas**.
>
> Además simulamos el **cierre del tramo Portoazul–UniNorte Puerta 4**. Al quitar esa arista, la intermediación del Colegio San José sube de 0.016 a 0.240: el tráfico se desvía por ahí, y el modelo lo detecta sin que nadie se lo diga.

### Diapositiva 8 · Conclusiones y limitaciones (≈ 0:35)

> Concluimos tres cosas. Primero, la **intermediación de Freeman encuentra paraderos puente** que no se ven mirando solo la cantidad de gente. Segundo, el **perceptrón converge en dos épocas** y da una regla lineal fácil de explicar. Tercero, dirigir los refuerzos **reduce la espera a menos de la mitad** en la simulación.
>
> También tenemos claras las limitaciones: la afluencia es **estimada**, no viene de conteos reales; el grafo **no tiene pesos** de tiempo de viaje; el simulador es simplificado y el perceptrón se entrenó con solo 6 muestras. Y con el cierre vial la clasificación no cambia: San José se acerca a la frontera, pero queda justo por debajo. Ahora sí, vamos a la demostración.

### Demostración en vivo (≈ 2:00)

Pasos sugeridos (la web ya debe estar abierta en `localhost:8080`):

1. **Pestaña "Grafo / Centralidades" (6 nodos)** — *(≈ 0:30)*
   > Este es el caso de 6 nodos. Si hago clic en v1, se ven las capas del BFS. Aquí está la tabla con los mismos valores de la diapositiva y los 8 caminos empatados de 0.5.
2. **Pestaña "Perceptrón"** — *(≈ 0:30)*
   > Aquí entrenamos el perceptrón en vivo. Con η = 0.5 y sesgo −1 converge en la época 2. Se ve la recta separando los paraderos rojos de los azules.
3. **Pestaña "Mapa del corredor" (15 paraderos)** — *(≈ 0:40)*
   > Este es el corredor real sobre el mapa. Activo el **cierre vial de La Gran Vía**… y el programa recalcula el BFS y las centralidades al instante: San José sube en intermediación.
4. **Pestaña "Impacto"** — *(≈ 0:20)*
   > Y aquí la comparación antes vs. después: 28.8 contra 12.4 minutos de espera.

**Cierre (cualquiera de los tres, ≈ 0:10):**
> Eso es todo. Muchas gracias. Quedamos atentos a sus preguntas.

---

## Preparación para preguntas (todos deben poder responder)

El 40 % de la nota es individual y el profesor puede preguntarle a cualquiera. Respuestas cortas para repasar:

**¿Por qué intermediación y no solo grado o cercanía?**
El grado solo cuenta vecinos, y la cercanía mide qué tan cerca está un paradero de todos los demás. La intermediación mide cuántos caminos más cortos **pasan por** el paradero, y eso es justo lo que causa que un bus llegue lleno: es un paradero de paso obligado.

**¿Qué pasa cuando hay dos caminos más cortos empatados?**
Cada camino aporta σ_st(v)/σ_st. Si hay 2 geodésicas y v está en una, recibe 1/2. Por eso aparecen los 8 aportes de 0.5 en el caso de 6 nodos.

**¿Por qué se normaliza entre C(n−1, 2)?**
Es el número de pares (s, t) que no incluyen a v en un grafo no dirigido. Para n = 6 es C(5, 2) = 10. Así C_B' queda entre 0 y 1 y es comparable con la afluencia.

**¿Por qué BFS y no Dijkstra?**
El grafo no tiene pesos: cada tramo cuenta como 1. En ese caso BFS ya da los caminos más cortos y es más simple (O(V+E) por fuente). Si agregáramos tiempos de viaje como pesos, usaríamos Dijkstra (es una mejora futura).

**¿Por qué un perceptrón?**
Porque los datos son linealmente separables (lo comprueba la convergencia con error 0), el resultado es una recta interpretable y es el modelo de redes neuronales del curso. El teorema de convergencia del perceptrón garantiza que termina si los datos son separables.

**¿Qué significan los pesos w = [0.2, 0.6] y b = −0.5?**
La afluencia pesa tres veces más que la intermediación, y el sesgo −0.5 es el umbral: hace falta suficiente combinación de demanda y centralidad para justificar un bus extra.

**¿De dónde salen los datos?**
Los 15 paraderos y sus coordenadas son reales. La afluencia es una estimación normalizada que hicimos nosotros, y la simulación de colas es determinista y está justificada en el informe. La cifra de 45.000 usuarios y los tiempos de espera vienen del AMB (2023).

**¿Por qué con el cierre vial no cambian los paraderos de refuerzo?**
El cierre sí cambia la red: San José pasa de C_B' = 0.016 a 0.240. Pero su afluencia es 0.60, y z = 0.2·0.240 + 0.6·0.60 − 0.5 = −0.092 < 0, así que queda justo debajo de la frontera. Si la afluencia de San José llegara a 0.76 o más, el modelo lo marcaría como refuerzo.

**¿Cómo se valida el programa?**
Con el caso de 6 nodos, cuyo resultado calculamos a mano (centralidades y las dos épocas del perceptrón), y comprobando que la salida del programa coincide número por número.
