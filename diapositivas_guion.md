# GUION TÉCNICO DE SUSTENTACIÓN ORAL Y ESTRUCTURA DE DIAPOSITIVAS
**Asignatura:** Matemáticas Discretas (MAT 4021 - MSc. Iván Gómez, Universidad del Norte)  
**Modalidad:** Sustentación Oral en Equipo de 3 Integrantes  
**Tiempo Total:** Exactamente 10 Minutos (Evaluación Individual: 40% de la Nota Final)  
**Proyecto:** Optimización del Despacho de Refuerzo en el Corredor Universitario (Cra 51B) mediante Grafos y Perceptrón de Rosenblatt.  
**Plataforma Interactiva:** Streamlit Dashboard (`app.py`) + Script Matemático de Consola (`main.py`).

---

## ⏱️ CRONOMETRAJE ESTRICTO (10 MINUTOS)

```
0:00 ──────────────── 3:00 ────────────────── 6:00 ────────────────── 9:00 ── 10:00
│  ESTUDIANTE 1 (3 min)   │   ESTUDIANTE 2 (3 min)   │   ESTUDIANTE 3 (3 min)   │ Q&A  │
│ Contexto, Cra 51B,      │ BFS, Distancias D,       │ Perceptrón, Heaviside,   │ Cierre
│ Red 15 Nodos + Cierres, │ Freeman, Empates 0.5,    │ Traza Épocas, Hiperplano │ y Q&A
│ Subgrafo 6 Nodos & A    │ Normalización CB'        │ y Demo Streamlit Impacto │ Blindaje
```

---

## ESTRUCTURA DE DIAPOSITIVAS Y GUIÓN PASO A PASO

### DIAPOSITIVA 1: Portada y Presentación del Problema (0:00 - 0:40)
- **Título en Pantalla:** Optimización del Despacho de Buses en el Corredor Universitario (Cra 51B) mediante Grafos Geodésicos y Clasificación Lineal Discreta.
- **Subtítulo:** Arquitectura de Doble Escala (6 Nodos Conocidos + 15 Nodos Reales) y Dashboard Interactivo.
- **Autores:** Estudiante 1, Estudiante 2, Estudiante 3.
- **Voz (Estudiante 1):**
  > "Buenos días, profesor Iván Gómez y compañeros. Nuestro proyecto aborda una problemática que vivimos a diario: el colapso del transporte en el Corredor Universitario de la Carrera 51B debido a las obras civiles de 'La Gran Vía'. Más de 45.000 usuarios sufren demoras de hasta 50 minutos esperando buses de Sobusa, Coolitoral o Transmetro. Nuestro objetivo fue modelar esta red mediante teoría formal de grafos y diseñar un Perceptrón simple de Rosenblatt desde cero en NumPy que identifique exactamente qué paraderos demandan despacho urgente de buses de refuerzo en vacío."

---

### DIAPOSITIVA 2: Problemática y Metodología de Doble Escala (0:40 - 1:40)
- **Contenido en Pantalla:**
  - Diagnóstico de movilidad: reducción de calzadas, sobrecupo $>65\%$, esperas de 35 a 50 min.
  - Metodología de Doble Escala:
    1. *Subgrafo de 6 Nodos:* Prueba de escritorio con resultado conocido exigida por la rúbrica.
    2. *Red de 15 Nodos:* Modelo arterial completo con simulación de cierres viales y dashboard en vivo.
- **Voz (Estudiante 1):**
  > "Para garantizar un trabajo académicamente riguroso y con impacto real, implementamos una metodología de doble escala:
  > En primer lugar, diseñamos un subgrafo diamante de 6 nodos para satisfacer la rúbrica de 'prueba con resultado conocido', donde cada cálculo se puede auditar a mano.
  > En segundo lugar, escalamos el modelo a 15 paraderos reales sobre la Carrera 51B, integrando un simulador de impacto antes vs. después y un dashboard en Streamlit para la toma de decisiones en tiempo real."

---

### DIAPOSITIVA 3: Red Escalada del Corredor (15 Nodos) y Cierre Vial (1:40 - 2:30)
- **Contenido en Pantalla:**
  - 15 macro-paraderos desde Circunvalar hasta Puerto Colombia.
  - Simulación de cierre vial en La Gran Vía (arista Portoazul $\leftrightarrow$ UniNorte P4).
  - Tabla comparativa de intermediación $C_B'$ ante cierre vial: Colegio San José salta de $0.016$ a $\mathbf{0.240}$ ($+0.224$).
- **Voz (Estudiante 1):**
  > "Al modelar la red completa de 15 paraderos con 20 aristas, podemos simular eventos reales de infraestructura. Cuando las obras de La Gran Vía cierran la calzada directa entre Portoazul y UniNorte Puerta 4, el algoritmo BFS recalcula de inmediato las rutas mínimas.
  > Como pueden observar en la tabla, la intermediación de Colegio San José ($P05$) salta de 0.016 a 0.240, absorbiendo todo el flujo desviado. La red no colapsa, pero transfiere la vulnerabilidad hacia los bypasses vecinales de Villa Santos y San José."

---

### DIAPOSITIVA 4: Caso de Prueba Conocido - Subgrafo Diamante (2:30 - 3:00)
- **Contenido en Pantalla:**
  - Vértices: $v_1$ (Campestre), $v_2$ (Portoazul), $v_3$ (UniNorte P4), $v_4$ (Makro), $v_5$ (UniAtlántico), $v_6$ (Retorno Sur).
  - Ciclo $C_4$ $\{v_1, v_2, v_3, v_4\}$ y nodo articulador $v_4$ (grado 3).
  - Matriz de adyacencia binaria simétrica $A(G) \in \{0, 1\}^{6 \times 6}$.
- **Voz (Estudiante 1):**
  > "Para la demostración analítica formal, nos concentramos en el subgrafo de 6 nodos. Noten que $\{v_1, v_2, v_3, v_4\}$ forma una estructura diamante o ciclo $C_4$, que refleja las dos rutas paralelas habilitadas por obras. Su matriz de adyacencia binaria $A$ es simétrica y de diagonal nula, con Makro ($v_4$) exhibiendo grado 3 como nodo de articulación. Cedo la palabra a mi compañero para explicar el cómputo de distancias y centralidades."

---

### DIAPOSITIVA 5: Algoritmo BFS por Capas y Matriz de Distancias $D$ (3:00 - 4:00)
- **Contenido en Pantalla:**
  - Exploración BFS por capas: $L_k = \{u \in V \mid d(s, u) = k\}$.
  - Matriz de distancias $D \in \mathbb{N}^{6 \times 6}$ generada por el script.
- **Voz (Estudiante 2):**
  > "Gracias. Para calcular la distancia geodésica $d(u,v)$ implementamos el algoritmo de Búsqueda en Anchura (BFS) por capas desde cada vértice raíz. Al no existir pesos en las aristas, el BFS garantiza determinismo matemático absoluto.
  > Obtenemos la matriz simétrica $D$. Por ejemplo, la distancia entre Campestre ($v_1$) y Retorno ($v_6$) es de exactamente 4 aristas geodésicas. Además, el algoritmo preserva una estructura de múltiples predecesores para registrar caminos concurrentes de igual longitud mínima."

---

### DIAPOSITIVA 6: Intermediación de Freeman y Manejo de Empates (4:00 - 5:00)
- **Contenido en Pantalla:**
  - Fórmula de Freeman: $C_B(v) = \sum_{s < t} \frac{\sigma_{st}(v)}{\sigma_{st}}$.
  - Par $(v_1, v_4)$ con dos caminos de longitud 2: $v_1-v_2-v_4$ y $v_1-v_3-v_4$.
  - Multiplicidad $\sigma_{v_1 v_4} = 2 \implies \frac{\sigma_{v_1 v_4}(v_2)}{\sigma_{v_1 v_4}} = \frac{1}{2} = \mathbf{0.50}$.
  - Normalización formal: $\binom{5}{2} = 10 \implies C_B'(v) = C_B(v) / 10$.
- **Voz (Estudiante 2):**
  > "Aquí resolvemos un punto crucial de la teoría discreta: los caminos empatados.
  > Entre Campestre ($v_1$) y Makro ($v_4$) existen dos caminos mínimos de longitud 2: uno por Portoazul ($v_2$) y otro por UniNorte ($v_3$). Como ambos son geodésicos, $\sigma_{v_1 v_4} = 2$. Al evaluar a Portoazul, este solo participa en una de las dos rutas, por lo que su aporte es exactamente $1/2 = 0.50$.
  > En total existen exactamente 8 aportes fraccionarios de 0.50 en la red: los pares hacia Makro, UniAtlántico y Retorno Sur aportan $3 \times 0.50 = 1.50$ tanto a Portoazul como a UniNorte, mientras que el par $(v_2, v_3)$ aporta $0.50$ a Campestre y a Makro.
  > Para normalizar $C_B$ en el intervalo $[0, 1]$, dividimos entre la combinación de pares desordenados $\binom{n-1}{2} = \binom{5}{2} = 10$. Así, Makro obtiene $C_B' = 0.65$ al ser el puente indispensable de la red."

---

### DIAPOSITIVA 7: Tabla Consolidada de Centralidades y Espacio $\mathbb{R}^2$ (5:00 - 6:00)
- **Contenido en Pantalla:**
  - Tabla comparativa ($C_D, \sum d, C_C, C_B, C_B'$, Afluencia $x_2$ y Clase Real $y$).
  - Espacio de características $\mathbf{x} = [C_B', \text{Afluencia}_{norm}]^T$.
- **Voz (Estudiante 2):**
  > "En esta tabla consolidamos las variables. UniNorte ($v_3$) presenta una altísima afluencia de 0.95 pero una intermediación moderada de 0.15, mientras que Makro ($v_4$) combina alta afluencia (0.80) con la máxima intermediación del sistema (0.65).
  > El desafío formal consiste en encontrar una frontera de decisión matemática en $\mathbb{R}^2$ que pondere ambas variables de forma óptima y sin reglas arbitrarias. Doy paso a mi compañero para presentar el clasificador de Rosenblatt."

---

### DIAPOSITIVA 8: Modelo Perceptrón de Rosenblatt desde Cero (6:00 - 7:00)
- **Contenido en Pantalla:**
  - Ecuaciones fundamentales: $z = w_1 x_1 + w_2 x_2 + b$, $\hat{y} = \phi(z)$ (Heaviside).
  - Regla de actualización: $\Delta \mathbf{w} = \eta \cdot e \cdot \mathbf{x}$, $\Delta b = \eta \cdot e$ ($\eta = 0.5$).
  - Inicialización: $\mathbf{w}^{(0)} = [0, 0]^T, b^{(0)} = -1.0$ (Umbral de inercia operativa $\theta = 1.0$).
- **Voz (Estudiante 3):**
  > "Implementamos el Perceptrón original de Rosenblatt (1958) desde cero en NumPy, sin utilizar librerías de caja negra como Scikit-Learn o PyTorch. Es un clasificador lineal booleano perfectamente acorde al syllabus de matemáticas discretas.
  > Inicializamos los pesos en cero y el sesgo en $b = -1.0$. Este sesgo actúa como un umbral de inercia económica: ningún paradero recibe un bus de refuerzo a menos que la combinación ponderada de intermediación en el grafo y demanda de usuarios supere el costo logístico de despacho."

---

### DIAPOSITIVA 9: Prueba de Escritorio y Convergencia en Época 2 (7:00 - 8:00)
- **Contenido en Pantalla:**
  - Tabla de traza por época (Época 1: 3 errores; Época 2: 0 errores).
  - Hiperplano aprendido: $0.20 C_B' + 0.60 \text{Afluencia} - 0.50 = 0 \implies \text{Afluencia} = -0.3333 C_B' + 0.8333$.
- **Voz (Estudiante 3):**
  > "La prueba de escritorio demuestra la convergencia determinista del algoritmo. En la Época 1, el perceptrón comete 3 errores ($p_3, p_4, p_5$), ajustando los pesos a $[0.20, 0.60]^T$ y $b = -0.50$.
  > En la Época 2, se reevalúan las 6 muestras y el error acumulado es exactamente CERO. 
  > Observen que ambos pesos son positivos ($w_1, w_2 > 0$): la topología y la demanda colaboran positivamente hacia el refuerzo. Además, la pendiente negativa de la frontera demuestra que paraderos con alta intermediación como Makro requieren menor afluencia para activar el despacho, protegiendo a la red de un bloqueo arterial."

---

### DIAPOSITIVA 10: Demostración Interactiva Streamlit y Métricas de Impacto (8:00 - 9:00)
- **Contenido en Pantalla:**
  - Resumen de las 4 pestañas de la aplicación web (`app.py`).
  - Métricas de impacto:
    - Tiempo medio de espera: $28.8 \text{ min} \to 12.4 \text{ min}$ ($\downarrow 56.8\%$).
    - Espera en UniNorte Puerta 4: $44.5 \text{ min} \to 14.8 \text{ min}$ ($\downarrow 66.7\%$).
    - Rechazo por sobrecupo: $39.4\% \to 20.3\%$ ($\downarrow 48.6\%$).
- **Voz (Estudiante 3):**
  > "Para la sustentación oral, construimos una aplicación web en Streamlit organizada en 4 pestañas: mapa real de 15 paraderos con toggle de cierre vial, verificación del subgrafo de 6 nodos, entrenamiento en vivo del perceptrón con sliders y el simulador de colas antes vs. después.
  > Como demuestra la simulación, el despacho dinámico guiado por el perceptrón reduce el tiempo medio de espera de 28.8 a 12.4 minutos —un ahorro del 56.8%— y en UniNorte el rechazo por sobrecupo cae del 68.2% al 15.5%. Si el profesor lo desea, podemos interactuar con la aplicación en vivo."

---

### DIAPOSITIVA 11: Conclusiones y Defensa ante Preguntas (9:00 - 10:00)
- **Contenido en Pantalla:**
  - Conclusiones del proyecto.
  - Repositorio y reproducibilidad: `python main.py` y `streamlit run app.py`.
- **Voz (Equipo en conjunto / Estudiante 1):**
  > "En conclusión: demostramos que las matemáticas discretas y el álgebra lineal pura, sin recurrir a modelos opacos de caja negra, ofrecen soluciones deterministas, auditables y de alto impacto social para el Corredor Universitario de la Carrera 51B. Quedamos a total disposición de las preguntas del profesor Iván Gómez."

---

## 🎯 BANCO DE BLINDAJE: LAS 4 PREGUNTAS TRAMPA DE LA CÁTEDRA

### Pregunta Trampa 1
> **Profesor:** *"¿Por qué usaron un Perceptrón si son solo paraderos? ¿No bastaba con un simple `IF-THEN`?"*
- **Quién responde:** **Estudiante 3.**
- **Respuesta exacta:**  
  *"Profesor, un condicional `IF` impone cortes ortogonales rígidos y subjetivos a criterio del programador (por ejemplo, `si afluencia > 0.7`). El Perceptrón de Rosenblatt aprende el hiperplano óptimo oblicuo de forma objetiva, ponderando analíticamente dos variables de dimensiones físicas distintas (topología de red adimensional vs. tasa estocástica de usuarios) mediante minimización formal de errores discretos. Es una función booleana de umbral del cuerpo teórico de la materia."*

### Pregunta Trampa 2
> **Profesor:** *"¿Por qué plantearon 15 nodos si la prueba de escritorio es de 6 nodos?"*
- **Quién responde:** **Estudiante 1.**
- **Respuesta exacta:**  
  *"Adoptamos una arquitectura de doble escala para cumplir con dos exigencias clave: la rúbrica pide una 'prueba con resultado conocido' totalmente demostrable a mano, lo cual se cumple en el subgrafo diamante de 6 nodos. Sin embargo, para evaluar el impacto real en el Corredor Universitario y simular desvíos como los de La Gran Vía, escalamos el grafo a 15 paraderos reales, demostrando que nuestro motor BFS y Perceptrón generalizan deterministamente a cualquier tamaño de red."*

### Pregunta Trampa 3
> **Profesor:** *"En su BFS, si hay dos caminos más cortos hacia un nodo, ¿cómo evita que el algoritmo favorezca al primero que entró en la cola?"*
- **Quién responde:** **Estudiante 2.**
- **Respuesta exacta:**  
  *"No utilizamos una cola simple que marca nodos como visitados y descarta los empates. Nuestro algoritmo implementa una lista de predecesores simultáneos y un acumulador de multiplicidad $\sigma_{st}$. Cuando se descubre un nodo vecino a la misma distancia mínima que otra rama activa (como entre $v_1$ y $v_4$ pasando por $v_2$ y $v_3$), el predecesor se anexa a la lista y el contador $\sigma_{st}$ se incrementa. Al calcular $C_B$, dividimos formalmente entre $\sigma_{st}$, asignando exactamente la fracción $1/2 = 0.50$ a cada rama."*

### Pregunta Trampa 4
> **Profesor:** *"¿Cómo garantizan que el entrenamiento del perceptrón no entre en bucle infinito?"*
- **Quién responde:** **Estudiante 3.**
- **Respuesta exacta:**  
  *"Por el Teorema de Convergencia de Novikoff (1962), el Perceptrón tiene convergencia finita garantizada siempre que exista un margen lineal $\gamma > 0$. Verificamos geométricamente que el conjunto es estrictamente separable en $\mathbb{R}^2$ con un margen $\gamma \approx 0.12$. Además, el código incorpora una cota máxima `max_epochs=20` que detiene la ejecución si los datos violaran la separabilidad lineal."*
