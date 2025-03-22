# Un podcast en diskettes

Pedro, febrero de 2025

En enero de 2025 sorteamos entre nuestros oyentes más fieles unos 12 pares de
diskettes -sí, de esos que no ves desde el año 2000- coneniendo nuestro
episodio №100 para conmemorar el hito de los cien episodios. Por trivial que
suene fue un proyecto de casi un año de trabajo, que en este artículo voy a
contarte en desgastante detalle. ¡Pero no huyas! Al final hay un resumen para
impacientes, y [por acá podés ver una demo]() del contenido de los disquettes.

## Quiénes somos

Pizzel es un humilde podcast de temática libre pero que gravita la órbita ñoña:
hablamos seguido de ciencia y tecnología, casi nunca con autoridad, pero
siempre con ganas de aprender y compartir.

Lo integramos Javier, Pedro, y ocasionalmente Julie (hermana de Pedro).

## ¿Por qué diskettes?

A principios de 2024 alcanzamos la primera centena de episodios. Por la ocasión
decidimos hacer un episodio extra largo, resultado en casi 3h20m de contenido.
Para facilitarle la digestión a nuestra audiencia lo partimos en 2 entregas, lo
cual nos remontó a los tiempos en que los videojuegos de PC venían en varios
diskettes marcados "disco 1 de 2", etc.

Ese nos llevó a armar este posteo promocional en Instagram:

https://www.instagram.com/p/C1mQBy3vgqR/?img_index=1
https://www.instagram.com/p/C1mQBy3vgqR/?img_index=2

Pero quizá por la nostalgia que significó volver a tocar diskettes en una era
que los relega a poco más que un anacrónico ícono de "Guardar archivo", nació
una idea: ¿qué tal si esos diskettes realmente contuvieran el episodio?

Al poco tiempo anuncié el desafío personal y el eventual sorteo en el podcast.
Ya no había vuelta atrás.

Por otro lado, varios otros podcasts han lanzado episodios en medios físicos a
forma conmemorativa, como casettes, vinilos, incluso un [cilindro de
cera](https://www.youtube.com/watch?v=WQ_-OVwS2ak), pero hasta donde sepamos
somos el primer podcast en usar diskettes. Más sobre eso luego.

## Haciendo entrar un piano en una caja de fósforos

El primer y más obvio problema que te la da en la nariz al querer calzar un
podcast en un diskette es su capacidad: unos módicos 1,44MB.

Cada uno de las dos mitades del ep. 100 en MP3 de 96kbps pesa aprox. 70MB.
Necesitaríamos al menos 98 diskettes para hacer entrar ambas en esa calidad. Ni
Microsoft se animó a tanto, que en 1995 distribuía Office en unos rechonchos 32
diskettes.

'Ta, ¿pero qué tal si le bajamos la calidad al MP3 a lo bestia?

La calidad más baja que soporta un MP3 es 8kbps, osea, 8 kilobits por segundo.
Con ese dato podemos hacer la siguiente cuenta:

* 1h40m son 100 minutos, o 6000 segundos (100⨉60s)
* 8kbps ⨉ 6000 segundos = 48.000 kilobits
* 48.000 kilobits ÷ 8 = 6000 kilobytes (kb) (1 byte = 8 bits)
* 6000kb ÷ 1000 = 6MB

Nos acerca bastante, pero en esta calidad todavía necesitamos al menos 4,5
diskettes por cada mitad, al menos 9 en total.

¿Y otro formato de audio con mejor compresión, como Opus?

En el juego de limbo de codificación de audio Opus le gana a MP3, pero solo
logra bajar hasta los 6kbps. Reemplazando los números en la cuenta de arriba
nos da:

* 6kbps ⨉ 6000 segundos = 36.000 kilobits
* 36.000 kilobits ÷ 8 = 4500 kilobytes (KB)
* 4500KB ÷ 1000 ≈ 4,5MB

Todavía necesitaríamos al menos 7 diskettes.

Haciendo el camino inverso podemos encontrar qué bitrate (kbps) necesitaríamos
para que entre en solo 2 diskettes:

Calculamos la capacidad en bits del diskette:

* 1,44MB (1.474.560 bytes) × 8 bits = 11.796.480 bits

Y de ahí el bitrate:

* 11.796.480 bits ÷ 6000 segundos ≈ 1,97kbps

Así que necesitamos un formato que pueda codificar audio en menos de ~2kbps
para poder hacer entrar 100 minutos en 1 diskette.

Por suerte, después de hurgar en internet un rato di a parar con un formato muy
poco conocido llamado [Codec2](https://www.rowetel.com/?page_id=452),
comúnmente usado por radioaficionados para radio digital de bajo ancho de
banda, que puede codificar audio en el espectro de 700bps (bits por segundo) a
3200bps, y es de código abierto. Prometedor.

Experimentando con Codec2 un poco concluí que 1200bps es el bitrate con mejor
balance tamaño/calidad, aunque hablando siempre de una calidad de audio
comparable a dos latas conectadas por hilo de matambre desde la ultratumba,
pero archivos de aproximadamente 900KB por cada mitad de episodio, lo que entra
perfectamente en un diskette dejando espacio de sobra.

# Codec2

Primer problema resuelto, pero cuando dije que Codec2 es poco conocido no era
broma, hasta el legendario VLC, famoso por reproducir Shrek 2 hasta de un
ladrillo de hormigón, se encoje de hombros al presentarle un archivo Codec2.
Podemos comprimir un podcast para entrar en un diskette, ¿pero qué gracia tiene
si nadie puede abrirlo?

Así que el segundo problema a resolver es: ¿puedo hacer entrar un reproductor
de Codec2 en los ~500KB que sobran después de los ~900KB del audio? ¿Y puedo
hacer que ese reproductor funcione en cualquier sistema?

La segunda pregunta se responde fácil: si el reproductor funciona en un browser
web, entonces no importa qué sistema operativo se use.

Con eso en mente busqué si existe alguna implementación de Codec2 para web, y
para mi suerte encontré
[codec2-emscripten](https://github.com/rameshvarun/codec2-emscripten), una
versión de Codec2 compilada a WebAssembly usando Emscripten por Varun Ramesh.

# Mucho texto, dame la versión corta

Sacamos un episodio en dos diskettes, cada uno con 100 minutos de audio en
formato Codec2, que codifica voz humana en tamaño diminuto, aunque suena como
una radio de ultratumba. Además, los diskettes incluyen un reproductor de
Codec2 hecho en HTML+JavaScript, un episodio exclusivo, y algo más de contenido
adicional.
