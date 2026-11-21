# Ficha automática de mercado y entorno para evaluar inmuebles en Medellín
Repositorio para el desarrollo de las actividades de la Hackatón del curso Análisis de Datos Básico del programa Estud-IA 2026.

## Situación que buscamos resolver
Antes de adquirir una propiedad necesitamos entender bien su contexto: cómo se comportan los precios por metro cuadrado de unidades similares según su conservación, qué tan buena es la conexión con el transporte, qué servicios y equipamientos hay cerca y qué regulación urbanística aplica. Esa evidencia hoy está fragmentada entre plataformas inmobiliarias y fuentes geoespaciales oficiales. Además, llega en formatos incompatibles, con repeticiones y vacíos, y no existe una ficha única que integre todo por inmueble.

## ¿Por qué conviene atenderlo?
Reunir esos datos a mano consume tiempo y reduce cuántas oportunidades podemos revisar, sobre todo en un sector con poca transparencia donde las opciones atractivas se pierden pronto. También aumenta el riesgo de dejar cabos sueltos —comparables repetidos o normas urbanísticas no consideradas— y de aplicar criterios cambiantes entre una evaluación y otra, lo que resta rigor y agilidad a la decisión de invertir.

### 1. Contexto empresarial

#### 1.1. ¿Qué organización es y a qué sector pertenece?
La organización es Venture One SAS, una empresa de proyectos inmobiliarios de Medellín. Pertenece al sector inmobiliario, específicamente a la inversión en finca raíz. Se dedican a identificar, adquirir y renovar inmuebles para venta o renta, atendiendo a inversionistas privados y compradores finales.

#### 1.2. ¿Qué ocurre hoy y cómo gestiona la situación?
Hoy en día, la información necesaria para evaluar los inmuebles (precios por m², transporte, equipamientos, norma urbanística) está dispersa en múltiples portales y geoportales, con formatos distintos, duplicados y datos incompletos. Se gestiona de forma manual: buscan comparables uno por uno, consultan MapGIS, toman capturas de pantalla y consolidan todo en hojas de cálculo con formatos que varían en cada análisis.

#### 1.3. ¿Por qué necesita resolverla y a quién afecta?
Necesitan resolverlo porque el proceso manual es lento, limita el número de oportunidades que pueden analizar y genera omisiones (como comparables duplicados o restricciones normativas ignoradas). Además, los criterios varían según la persona, afectando la calidad y velocidad de la decisión. Afecta directamente al equipo de inversión y a la dirección de la empresa, y de forma indirecta a los aliados que esperan respuestas rápidas.

#### 1.4. ¿Dónde ocurre y con qué frecuencia?
Ocurre en el proceso de análisis de oportunidades de inversión, específicamente en la etapa previa a la decisión de compra (cuando se reúne y depura la información). La frecuencia es cada vez que entra una nueva oportunidad de inversión.


### 2. El problema que vamos a abordar

#### 2.1. ¿Cuál es el reto y quién vive el problema?
El reto es lograr consolidar de manera ágil y estandarizada la información de mercado y entorno de cada inmueble en una sola vista, superando la dispersión y desorganización de los datos actuales. Quienes viven el problema son principalmente el equipo de inversión de Venture One.

#### 2.2. ¿Qué sabemos con evidencia y qué suponemos?
##### 2.2.1. Con evidencia 
Sabemos que los datos actuales tienen duplicados, formatos distintos y datos incompletos. Sabemos que existen recursos disponibles (bases de datos en CSV con errores, capas geográficas en GeoJSON) y que el proceso manual actual es ineficiente, lento y propenso a errores.
##### 2.2.2. Suponemos
Que el uso de herramientas de análisis de datos y la limpieza y cruce de datos permitirán reducir drásticamente los tiempos de análisis, eliminar los errores humanos y mejorar la transparencia en la toma de decisiones.

#### 2.3. ¿Qué preguntas siguen sin resolver?
Cómo cruzar y deduplicar la información de portales y mapas, qué hacer con los datos faltantes, y cómo estandarizar los formatos para que el análisis no dependa de la persona que lo realice.

#### 2.4. ¿Qué resultado necesita el usuario?
El usuario necesita una ficha automática de mercado y entorno por cada inmueble: una vista consolidada que integre los precios de comparables (limpios y sin duplicados), datos del entorno (transporte, equipamientos, POT) y que deje registro de los datos faltantes. En resumen, necesitan una herramienta que garantice una decisión de inversión rápida, estandarizada y basada en datos confiables.


### 3. La pregunta que orientará las ideas
¿Cómo podríamos ayudar al equipo de inversión de Venture One a generar un tablero que consolide los datos de mercado y entorno, para agilizar y estandarizar sus decisiones de compra de inmuebles?

### 4. Ideación
Usando la método SCAMPER para la ideación de una solución a nuestro problema, tenemos lo siguiente:

#### S - Sustituir 
¿Qué cambiaríamos?
- Cambiar el trabajo manual: En vez de buscar casa por casa en internet y tomar capturas de pantalla, podemos usar un código en Python (en Google Colab) que lea el archivo CSV y lo junte con los mapas.
- Cambiar la diversificación por la estandarización: En lugar de que cada persona decida si la casa es buena a su manera, podemos crear una fórmula que puntúe cada inmueble para saber si es buena inversión.
- Cambiar el Excel por un tablero: Dejar de usar hojas de cálculo y crear un dashboard que muestre todo de forma ordenada.

#### C - Combinar 
¿Qué uniríamos?
- Juntar los precios con los mapas: Poner en una sola pantalla los datos de las casas (precio, área, habitaciones) junto con los mapas de Medellín (estaciones de metro, parques, normas del POT). 
- Juntar precios para comparar: Usar el archivo extra de otras plataformas para comparar si el precio de la casa que estamos viendo es caro o barato frente al resto del mercado.

#### A - Adaptar 
¿Qué adaptaríamos de otros lados?
- Copiar la idea de las estrellas: Como cuando calificas un restaurante o un celular, podemos ponerle estrellas o un semáforo a cada casa según qué tan buena sea para invertir.
- Copiar la idea de Google Maps: Hacer un mapa interactivo donde puedas hacer clic en una ubicación y automáticamente se abra una ventanita con toda su información y si tiene problemas con las normas.
- Copiar la idea de un "chequeo médico": Hacer una ficha donde le digamos al inversionista qué datos le faltan a esa casa y qué riesgos tiene.

#### M - Modificar 
¿Qué le modificaríamos?
- Clasificar la información en Base de Datos para una búsqueda de inmuebles más eficiente y segura 
- Realizar una actualización con  precios reales del inmueble, de acuerdo a lo que demanda el mercado.

#### P - Proponer otros usos 
¿A quién más le sirve? 
- Para los aliados: Darles una versión sencilla para que ellos mismos revisen si la casa que nos quieren vender cumple con lo básico antes de enviárnosla.
- Para los compradores: Usar la información del mapa (como la cercanía al metro) como un folleto para convencer a los clientes de comprar la casa ya renovada.
- Para los jefes: Usar el tablero como un reporte para mostrar cuántas oportunidades analizamos a la semana y cuántas descartamos.

#### E - Eliminar 
¿Qué quitaríamos?
- Quitar los duplicados: Hacer que el código detecte y borre automáticamente las casas que están repetidas en diferentes anuncios.
- Quitar el copiar y pegar: Dejar de buscar a mano en los portales y usar solo los archivos base que nos dieron para trabajar más rápido.
- Quitar las minucias: En el tablero, quitar toda la información aburrida del anuncio y dejar solo lo importante (precio, área, si se puede construir, cercanía al transporte).

#### R - Reorganizar 
¿Qué cambiaríamos de orden?
- Cambiar el orden de los pasos: En vez de buscar, luego pegar en Excel y luego analizar, el nuevo orden sería: 1. Subir los datos, 2. El código los limpia y les pone nota, 3. El tablero nos muestra solo los que sirven.
- Organizar por problemas: En vez de ver el tablero como una lista interminable de casas, separarlo por pestañas: "Casas con datos incompletos", "Casas con problemas de normas", y "Mejores oportunidades". Así es más fácil saber qué revisar primero.


### 5. La propuesta de MVP
**FALTANTE**
