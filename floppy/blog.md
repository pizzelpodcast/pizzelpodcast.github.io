# Un podcast en diskettes

Pedro, febrero de 2025

En enero de 2025 sorteamos entre nuestros oyentes varios pares de diskettes
coneniendo nuestro episodio 100.

Ese humilde par de diskettes contiene las 3+ horas de audio del episodio, [un
reproductor web](), un episodio exclusivo, y varios extras más.

Entre desafíos técnicos, de diseño, logísticos y de procrastinación esto se
convirtió en un proyecto de casi un año, cuyos detalles les paso a contar.

Spoilers para nerds impacientes: Codec2, Emscripten

## Quiénes somos

Pizzel es un podcast en español de temática libre, pero que gravita órbitas
ñoñas: hablamos seguido de ciencia y tecnología, casi nunca con autoridad, pero
siempre con genuino interés.

Lo integramos Javier, Pedro, y ocasionalmente Julie (hermana de Pedro).

## ¿Por qué diskettes?

A principios de 2024 alcanzamos nuestro episodio 100, que por la ocasión
terminó siendo también nuestro episodio más largo: casi 3h20m de contenido.

Para hacerlo más masticable lo partimos en 2 entregas, lo cual nos evocó un
poco las tiernas épocas en que los juegos de PC venían en múltiples diskettes
etiquetados "disco 1 de 2", etc. (que no se noten tanto nuestros años)

Ese concepto derivó en este posteo promocional del episodio en Instagram:

https://www.instagram.com/p/C1mQBy3vgqR/?img_index=1
https://www.instagram.com/p/C1mQBy3vgqR/?img_index=2

Pero quizá por la nostalgia de volver a tocar diskettes en una era que los
relega a poco más que un anacrónico ícono de "guardar archivo", germinó una
idea: ¿qué tal si esos diskettes realmente contuvieran el episodio?

Terminé tomándomelo como un desafío. Al poco tiempo anuncié el eventual sorteo
en el podcast. Ya no había vuelta atrás.

Por otro lado, aunque varios otros podcasts del mundo lanzaron episodios en
medios físicos, como casettes, vinilos, incluso un [cilindro de
cera](https://www.youtube.com/watch?v=WQ_-OVwS2ak), hasta donde sabíamos ningún
podcast lo había hecho en diskettes, por lo que estaba sobre la mesa la
oportunidad de ser los primeros. Más sobre eso luego.

## Un piano en una caja de fósforos

El primer y más obvio problema al querer calzar un podcast en un diskette es su
capacidad: apenas 1,44MB.

Cada una de las dos mitades de nuestro episodio en MP3 de 96kbps pesa aprox.
70MB. Necesitaríamos al menos 98 diskettes para hacer entrar ambas partes en
esa calidad. Ni Microsoft se animó a tanto, que en 1995 distribuía Office en
unos rechonchos 32 diskettes.

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

En el baile de limbo de codificación de audio Opus le gana a MP3, pero solo se
agacha hasta los 6kbps. Reemplazando los números en la cuenta de arriba nos da:

* 6kbps ⨉ 6000 segundos = 36.000 kilobits
* 36.000 kilobits ÷ 8 = 4500 kilobytes (KB)
* 4500KB ÷ 1000 ≈ 4,5MB

Todavía necesitaríamos al menos 7 diskettes.

Haciendo el camino inverso podemos encontrar qué bitrate necesitaríamos para
que entre en solo 2 diskettes:

Calculamos la capacidad en bits del diskette:

* 1,44MB (1.474.560 bytes) × 8 bits = 11.796.480 bits

Y de ahí el bitrate:

* 11.796.480 bits ÷ 6000 segundos ≈ 1,97kbps

Así que necesitamos un formato que pueda codificar audio en menos de ~2kbps
para poder hacer entrar 100 minutos en 1 diskette.

Por suerte, después de hurgar en internet un buen rato di a parar con un
formato muy poco conocido llamado
[Codec2](https://www.rowetel.com/?page_id=452), comúnmente usado por
radioaficionados para radio digital de bajo ancho de banda, que puede codificar
audio en el espectro de 700bps (bits por segundo) a 3200bps, y es de código
abierto. Prometedor.

Experimentando con Codec2 un poco concluí que 1200bps es el bitrate con mejor
balance tamaño/calidad, aunque hablando siempre de una calidad de audio
comparable a dos latas conectadas por hilo de matambre desde ultratumba, pero
archivos de aproximadamente 900KB por cada mitad de episodio, lo que entra
perfectamente en un diskette dejando hasta espacio de sobra.

## Codec2

Primer problema resuelto, pero cuando dije que Codec2 es poco conocido no era
broma, hasta el legendario VLC, famoso por reproducir video en 4K hasta de un
ladrillo de hormigón, se encoje de hombros frente a un archivo Codec2. Podemos
comprimir un podcast para entrar en un diskette, ¿pero de qué sirve tiene si
nadie puede abrirlo?

Así que la próxima incógnita a resolver es si podría entrar un reproductor de
Codec2 en los ~500KB que sobran después de los ~900KB del audio. Y además, cómo
hacer que ese reproductor funcione en cualquier sistema.

Esa última pregunta se responde fácil: hagamos un reproductor web,
multi-plataforma por naturaleza.

Con eso en mente busqué alguna implementación de Codec2 para web, y para mi
suerte encontré
[codec2-emscripten](https://github.com/rameshvarun/codec2-emscripten), una
versión de Codec2 compilada a WebAssembly (usando Emscripten) por Varun Ramesh.

El decodificador Codec2 compilado a WASM pesa unos 900KB, y sumados a unos
140KB de JavaScript de Emscripten estamos hablando de arriba de 1MB, que
obviamente no entran en nuestro presupuesto de 500KB, pero comprimiéndolos se
reducen a unos 400KB, lo cual entra bien y nos deja unos cómodos 100KB para
trabajar la interfaz HTML del reproductor.

Sin embargo a este punto me encontré con otro obstáculo: para cargar un
programa WASM en una página web es necesario levantar el binario desde
JavaScript usando `fetch()` o similar, que está condicionado por
[CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), el perro
guardián de la web que ladra especialmente fuerte al levantar HTML del sistema
de archivos como en nuestro caso. En otras palabras: no podemos cargar WASM de
forma usual, tenemos que encontrar una alternativa (léase: un hack).

La solución es embeber el WASM en nuestro JavaScript en forma de data-URI,
haciéndolo "inimputable" frente a CORS, pero esto viene con una penalización:
tenemos que codificar el WASM usando Base64, lo que hincha el archivo a
aproximadamente 4/3 de su tamaño original, que para nuestro WASM de 900KB
significa llevarlo a casi 1,2MB.

Para resolver esto último probé varios sistemas de compresión, y finalmente
decidí que 7Zip tiene la mejor combinación de nivel de compresión y
popularidad.

## Diseño de la etiqueta

Además del aspecto técnico, también pasé bastante tiempo ocupándome de la
estética de los diskettes en sí.

# Mucho texto, dame la versión corta

Sacamos un episodio en dos diskettes, cada uno con 100 minutos de audio en
formato Codec2, que codifica voz humana en tamaño diminuto, aunque suena como
una radio de ultratumba. Además, los diskettes incluyen un reproductor de
Codec2 hecho en HTML+JavaScript, un episodio exclusivo, y algo más de contenido
adicional.
