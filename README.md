# Sonido estéreo y ficheros WAVE

## Eduard Peñas Balart 

## El formato WAVE

El formato WAVE es uno de los más extendidos para el almacenamiento y transmisión
de señales de audio. En el fondo, se trata de un tipo particular de fichero
[RIFF](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (*Resource
Interchange File Format*), utilizado no sólo para señales de audio sino también para señales de
otros tipos, como las imágenes estáticas o en movimiento, o secuencias MIDI (aunque, en el caso
del MIDI, con pequeñas diferencias que los hacen incompatibles).

La base de los ficheros RIFF es el uso de *cachos* (*chunks*, en inglés). Cada cacho,
o subcacho, está encabezado por una cadena de cuatro caracteres ASCII, que indica el tipo del cacho,
seguido por un entero sin signo de cuatro bytes, que indica el tamaño en bytes de lo que queda de
cacho sin contar la cadena inicial y el propio tamaño. A continuación, y en función del tipo de
cacho, se colocan los datos que lo forman.

Todo fichero RIFF incluye un primer cacho que lo identifica como tal y que empieza por la cadena
`'RIFF'`. A continuación, después del tamaño del cacho y en otra cadena de cuatro caracteres,
se indica el tipo concreto de información que contiene el fichero. En el caso concreto de los
ficheros de audio WAVE, esta cadena es igual a `'WAVE'`, y el cacho debe contener dos
*subcachos*: el primero, de nombre `'fmt '`, proporciona la información de cómo está
codificada la señal. Por ejemplo, si es PCM lineal, ADPCM, etc., o si es monofónica o estéreo. El
segundo subcacho, de nombre `'data'`, incluye las muestras de la señal.

Dispone de una descripción detallada del formato WAVE en la página
[WAVE PCM soundfile format](http://soundfile.sapp.org/doc/WaveFormat/) de Soundfile.

## Audio estéreo

La mayor parte de los animales, incluidos los del género *homo sapiens sapiens* sanos y completos,
están dotados de dos órganos que actúan como transductores acústico-sensoriales (es decir, tienen dos
*oídos*). Esta duplicidad orgánica permite al bicho, entre otras cosas, determinar la dirección de
origen del sonido. En el caso de la señal de música, además, la duplicidad proporciona una sensación
de *amplitud espacial*, de realismo y de confort acústico.

En un principio, los equipos de reproducción de audio no tenían en cuenta estos efectos y sólo permitían
almacenar y reproducir una única señal para los dos oídos. Es el llamado *sonido monofónico* o
*monoaural*. Una alternativa al sonido monofónico es el *estereofónico* o, simplemente, *estéreo*. En
él, se usan dos señales independientes, destinadas a ser reproducidas a ambos lados del oyente: los
llamados *canal izquierdo* (**L**) y *derecho* (**R**).

Aunque los primeros experimentos con sonido estereofónico datan de finales del siglo XIX, los primeros
equipos y grabaciones de este tipo no se popularizaron hasta los años 1950 y 1960. En aquel tiempo, la
gestión de los dos canales era muy rudimentaria. Por ejemplo, los instrumentos se repartían entre los
dos canales, con unos sonando exclusivamente a la izquierda y el resto a la derecha. Es el caso de las
primeras grabaciones en estéreo de los Beatles: las versiones en alemán de los singles *She loves you*
y *I want to hold your hand*. Así, en esta última (de la que dispone de un fichero en Atenea con sus
primeros treinta segundos, [Komm, gib mir deine Hand](wav/komm.wav)), la mayor parte de los instrumentos
suenan por el canal derecho, mientras que las voces y las características palmas lo hacen por el izquierdo.

Un problema habitual en los primeros años del sonido estereofónico, y aún vigente hoy en día, es que no
todos los equipos son capaces de reproducir los dos canales por separado. La solución comúnmente
adoptada consiste en no almacenar cada canal por separado, sino en la forma semisuma, $(L+R)/2$, y
semidiferencia, $(L-R)/2$, y de tal modo que los equipos monofónicos sólo accedan a la primera de ellas.
De este modo, estos equipos pueden reproducir una señal completa, formada por la suma de los dos
canales, y los estereofónicos pueden reconstruir los dos canales estéreo.

Por ejemplo, en la radio FM estéreo, la señal, de ancho de banda 15 kHz, se transmite del modo siguiente:

- En banda base, $0\le f\le 15$ kHz, se transmite la suma de los dos canales, $L+R$. Esta es la señal
  que son capaces de reproducir los equipos monofónicos.

- La señal diferencia, $L-R$, se transmite modulada en amplitud con una frecuencia de portadora
  $f_m = 38$ kHz.

  - Por tanto, ocupa la banda $23 \mathrm{kHz}\le f\le 53 \mathrm{kHz}$, que sólo es accedida por los
    equipos estéreo, y, en el caso de colarse en un reproductor monofónico, ocupa la banda no audible.

- También se emite una sinusoide de $19 \mathrm{kHz}$, denominada *señal piloto*, que se usa para
  demodular síncronamente la señal diferencia.

- Finalmente, la señal de audio estéreo puede acompañarse de otras señales de señalización y servicio en
  frecuencias entre $55.35 \mathrm{kHz}$ y $94 \mathrm{kHz}$.

En los discos fonográficos, la semisuma de las señales está grabada del mismo modo que se haría en una
grabación monofónica, es decir, en la profundidad del surco; mientras que la semidiferencia se graba en el
desplazamiento a izquierda y derecha de la aguja. El resultado es que un reproductor mono, que sólo atiende
a la profundidad del surco, reproduce casi correctamente la señal monofónica, mientras que un reproductor
estéreo es capaz de separar los dos canales. Es posible que algo de la información de la semisuma se cuele
en el reproductor mono, pero, como su amplitud es muy pequeña, se manifestará como un ruido muy débil,
apenas perceptible.

En general, todos estos sistemas se basan en garantizar que el reproductor mono recibe correctamente la
semisuma de canales y que, si algo de la semidiferencia se cuela en la reproducción, sea en forma de un
ruido inaudible.

## Tareas a realizar

Escriba el fichero `estereo.py` que incluirá las funciones que permitirán el manejo de los canales de una
señal estéreo y su codificación/decodificación para compatibilizar ésta con sistemas monofónicos.

### Manejo de los canales de una señal estéreo

En un fichero WAVE estéreo con señales de 16 bits, cada muestra de cada canal se codifica con un entero de
dos bytes. La señal se almacena en el *cacho* `'data'` alternando, para cada muestra de $x[n]$, el valor
del canal izquierdo y el derecho:

<img src="img/est%C3%A9reo.png" width="380px">

#### Función `estereo2mono(ficEste, ficMono, canal=2)`

La función lee el fichero `ficEste`, que debe contener una señal estéreo, y escribe el fichero `ficMono`,
con una señal monofónica. El tipo concreto de señal que se almacenará en `ficMono` depende del argumento
`canal`:

- `canal=0`: Se almacena el canal izquierdo $L$.
- `canal=1`: Se almacena el canal derecho $R$.
- `canal=2`: Se almacena la semisuma $(L+R)/2$. Ha de ser la opción por defecto.
- `canal=3`: Se almacena la semidiferencia $(L-R)/2$.

#### Función `mono2estereo(ficIzq, ficDer, ficEste)`

Lee los ficheros `ficIzq` y `ficDer`, que contienen las señales monofónicas correspondientes a los canales
izquierdo y derecho, respectivamente, y construye con ellas una señal estéreo que almacena en el fichero
`ficEste`.

### Codificación estéreo usando los bits menos significativos

En la línea de los sistemas usados para codificar la información estéreo en señales de radio FM o en los
surcos de los discos fonográficos, podemos usar enteros de 32 bits para almacenar los dos canales de 16 bits:

- En los 16 bits más significativos se almacena la semisuma de los dos canales.

- En los 16 bits menos significativos se almacena la semidiferencia.

Los sistemas monofónicos sólo son capaces de manejar la señal de 32 bits. Esta señal es prácticamente
idéntica a la señal semisuma, ya que la semisuma ocupa los 16 bits más significativos. La señal
semidiferencia aparece como un ruido añadido a la señal, pero, como su amplitud es $2^{16}$ veces más
pequeña, será prácticamente inaudible (la relación señal a ruido es del orden de 90 dB).

Los sistemas estéreo son capaces de aislar las dos partes de la señal y, con ellas, reconstruir los dos
canales izquierdo y derecho.

<img src="img/est%C3%A9reo_cod.png" width="510px">

#### Función `codEstereo(ficEste, ficCod)`

Lee el fichero `ficEste`, que contiene una señal estéreo codificada con PCM lineal de 16 bits, y
construye con ellas una señal codificada con 32 bits que permita su reproducción tanto por sistemas
monofónicos como por sistemas estéreo preparados para ello.

#### Función `decEstereo(ficCod, ficEste)`

Lee el fichero `ficCod` con una señal monofónica de 32 bits en la que los 16 bits más significativos
contienen la semisuma de los dos canales de una señal estéreo y los 16 bits menos significativos la
semidiferencia, y escribe el fichero `ficEste` con los dos canales por separado en el formato de los
ficheros WAVE estéreo.

### Entrega

#### Fichero `estereo.py`

- El fichero debe incluir una cadena de documentación que incluirá el nombre del alumno y una descripción
  del contenido del fichero.

- Es muy recomendable escribir, además, sendas funciones que *empaqueten* y *desempaqueten* las cabeceras
  de los ficheros WAVE a partir de los datos contenidos en ellas.

- Aparte de `struct`, no se puede importar o usar ningún módulo externo.

- Se deben evitar los bucles. Se valorará el uso, cuando sea necesario, de *comprensiones*.

- Los ficheros se deben abrir y cerrar usando gestores de contexto.

- Las funciones deberán comprobar que los ficheros de entrada tienen el formato correcto y, en caso
  contrario, elevar la excepción correspondiente.

- Los ficheros resultantes deben ser reproducibles correctamente usando cualquier reproductor estándar;
  por ejemplo, el Windows Media Player o similar. Es probable, muy probable, que tenga que modificar los
  datos de las cabeceras de los ficheros para conseguirlo.

- Se valorará lo pythónico de la solución; en concreto, su claridad y sencillez, y el uso de los estándares
  marcados por PEP-ocho.

#### Comprobación del funcionamiento

Es responsabilidad del alumno comprobar que las distintas funciones realizan su cometido de manera correcta.
Para ello, se recomienda usar la canción [Komm, gib mir deine Hand](wav/komm.wav), suminstrada al efecto.
De todos modos, recuerde que, aunque sea en alemán, se trata de los Beatles, así que procure no destrozar
innecesariamente la canción.

#### Código desarrollado

Inserte a continuación el código de los métodos desarrollados en esta tarea, usando los comandos necesarios
para que se realice el realce sintáctico en Python del mismo (no vale insertar una imagen o una captura de
pantalla, debe hacerse en formato *markdown*).

##### Código de `estereo2mono()`
```python

def estereo2mono(ficEste, ficMono, canal=2):
    """
    Convierte un archivo estéreo WAVE de 16 bits a mono según el canal indicado.

    Parametros:
    ficEste -- ruta del archivo de entrada estéreo
    ficMono -- ruta del archivo de salida mono
    canal -- modo de conversión (0: izquierdo, 1: derecho, 2: semisuma, 3: semidiferencia)
    """
    with open(ficEste, 'rb') as f_in:
        cab = leer_cabecera_wave(f_in)
        if cab['num_channels'] != 2:
            raise ValueError('El archivo no es estéreo')
        datos = f_in.read(cab['data_size'])

    tam = cab['bits_per_sample'] // 8
    muestras = [datos[i:i+2*tam] for i in range(0, len(datos), 2*tam)]
    datos_mono = b''.join([procesar_muestra(m, tam, canal) for m in muestras])

    with open(ficMono, 'wb') as f_out:
        escribir_cabecera_wave(f_out, 1, cab['sample_rate'], cab['bits_per_sample'], len(datos_mono))
        f_out.write(datos_mono)
````
##### Pruebas de `estereo2mono()`

```python
# Canal izquierdo
estereo2mono('wav_komm.wav', 'canal_izquierdo.wav', canal=0)

# Canal derecho
estereo2mono('wav_komm.wav', 'canal_derecho.wav', canal=1)

# Semisuma (por defecto)
estereo2mono('wav_komm.wav', 'semisuma.wav')

# Semidiferencia
estereo2mono('wav_komm.wav', 'semidiferencia.wav', canal=3)
```
![Señal semisuma](img/semisuma.png)
![Señal semidiferencia](img/semidiferencia.png)
![Señal canal izquierdo](img/canal_izquierdo.png)
![Señal canal derecho](img/canal_derecho.png)
![Señal estereo original](img/wav_komm.png)

##### Código de `mono2estereo()`
```python
def mono2stereo(ficIzq, ficDer, ficEste):
    """
    Reconstruye un archivo estéreo a partir de dos archivos mono (izquierdo y derecho).

    Parametros:
    ficIzq -- archivo mono con el canal izquierdo
    ficDer -- archivo mono con el canal derecho
    ficEste -- archivo de salida estéreo reconstruido
    """
    with open(ficIzq, 'rb') as f_izq, open(ficDer, 'rb') as f_der:
        cab_izq = leer_cabecera_wave(f_izq)
        cab_der = leer_cabecera_wave(f_der)

        if cab_izq['num_channels'] != 1 or cab_der['num_channels'] != 1:
            raise ValueError('Ambos ficheros deben ser monofónicos')
        if cab_izq['sample_rate'] != cab_der['sample_rate']:
            raise ValueError('Las frecuencias de muestreo no coinciden')
        if cab_izq['bits_per_sample'] != cab_der['bits_per_sample']:
            raise ValueError('Los bits por muestra no coinciden')
        if cab_izq['data_size'] != cab_der['data_size']:
            raise ValueError('Las longitudes de los datos no coinciden')

        datos_izq = f_izq.read(cab_izq['data_size'])
        datos_der = f_der.read(cab_der['data_size'])

    tam = cab_izq['bits_per_sample'] // 8
    datos_stereo = b''.join([datos_izq[i:i+tam] + datos_der[i:i+tam] for i in range(0, len(datos_izq), tam)])

    with open(ficEste, 'wb') as f_out:
        escribir_cabecera_wave(f_out, 2, cab_izq['sample_rate'], cab_izq['bits_per_sample'], len(datos_stereo))
        f_out.write(datos_stereo)

```
##### Pruebas de `mono2estereo()`
```python
mono2stereo('canal_izquierdo.wav', 'canal_derecho.wav', 'reconstruido.wav')
```
![Señal reconstruido](img/reconstruido.png)

##### Código de `codEstereo()`
```python
def codEstereo(ficEste, ficCod):
    """
    Codifica una señal estéreo de 16 bits como una señal mono de 32 bits.
    La semisuma se guarda en los 16 bits altos y la semidiferencia en los 16 bits bajos.

    Parametros:
    ficEste -- archivo estéreo de entrada
    ficCod -- archivo mono de salida codificado
    """
    with open(ficEste, 'rb') as f_in:
        cab = leer_cabecera_wave(f_in)
        if cab['num_channels'] != 2 or cab['bits_per_sample'] != 16:
            raise ValueError('El fichero debe ser estéreo de 16 bits')
        datos = f_in.read(cab['data_size'])

    muestras = [struct.unpack('<hh', datos[i:i+4]) for i in range(0, len(datos), 4)]
    codificadas = [struct.pack('<I', (((L + R) // 2 & 0xFFFF) << 16) | ((L - R) // 2 & 0xFFFF)) for L, R in muestras]
    datos_cod = b''.join(codificadas)

    with open(ficCod, 'wb') as f_out:
        escribir_cabecera_wave(f_out, 1, cab['sample_rate'], 32, len(datos_cod))
        f_out.write(datos_cod)
```
##### Pruebas de `codEstereo()`
```pyhton
codEstereo('wav_komm.wav', 'codificado.wav')
```
![Detalles audio](img/pruebacod.png)
##### Código de `decEstereo()`
```python
def decEstereo(ficCod, ficEste):
    """
    Decodifica una señal mono de 32 bits en una señal estéreo de 16 bits por canal.

    Parametros:
    ficCod -- archivo mono de 32 bits codificado
    ficEste -- archivo de salida estéreo reconstruido
    """
    with open(ficCod, 'rb') as f_in:
        cab = leer_cabecera_wave(f_in)
        if cab['num_channels'] != 1 or cab['bits_per_sample'] != 32:
            raise ValueError('El fichero debe ser monofónico de 32 bits')
        datos = f_in.read(cab['data_size'])

    muestras = [struct.unpack('<I', datos[i:i+4])[0] for i in range(0, len(datos), 4)]
    reconstruidas = [
        struct.pack('<hh',
            saturar16((m >> 16) + int16(m & 0xFFFF)),
            saturar16((m >> 16) - int16(m & 0xFFFF))
        ) for m in muestras
    ]
    datos_estereo = b''.join(reconstruidas)

    with open(ficEste, 'wb') as f_out:
        escribir_cabecera_wave(f_out, 2, cab['sample_rate'], 16, len(datos_estereo))
        f_out.write(datos_estereo)
```
##### Pruebas de `decEstereo()`
````python
decEstereo('codificado.wav', 'recuperado.wav')
````
![Detalles audio](img/recuperacion.png)
#### Subida del resultado al repositorio GitHub y *pull-request*

La entrega se formalizará mediante *pull request* al repositorio de la tarea.

El fichero `README.md` deberá respetar las reglas de los ficheros Markdown y visualizarse correctamente en
el repositorio, incluyendo el realce sintáctico del código fuente insertado.
