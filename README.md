# Lab-3 Problema del coctel 
# INTRODUCCIÓN
En el presente laboratorio se explora el "problema de la fiesta de cóctel", que consiste en simular una conversación en un entorno ruidoso con múltiples fuentes de sonido. Utilizando tres micrófonos (uno por integrante del grupo) con el fin de captar por medio de celulares a diversas distancias el sonido y percibido calcular el SNR de cada una de las señales, adicionalmente se realiza un análisis espectral con la tranformada rápida de fourier para cada señal, separando el ruido de la señal por medio de filtrado.
# PASO A PASO:
1. Configuración del sistema:
- Conectar los tres micrófonos al sistema de adquisición de datos.
- Ubicarlos en la sala de manera que cada uno capture una combinación diferente de sonidos.
- Colocar a tres personas en posiciones fijas y orientadas en distintas direcciones para simular un ambiente con varias fuentes de sonido (como en una fiesta).

2. Captura de la señal:
- Pedir a cada persona que diga una frase diferente al mismo tiempo mientras los micrófonos graban.
- Guardar las grabaciones para analizarlas después.
- Medir la cantidad de ruido en el ambiente (SNR). Si alguna señal tiene mucho ruido, repetir la medición y explicar por qué ocurrió.

3. Procesamiento de señales:
- Analizar cómo varía el sonido con el tiempo y en frecuencia.
- Investigar y aplicar técnicas para separar las voces, como el Análisis de Componentes Independientes (ICA).

4. Evaluación de resultados
- Comparar la señal aislada con la original. . Usar métricas como la relación señal/ruido (SNR) para medir qué tan bien se logró separar cada voz

<img width="867" height="763" alt="image" src="https://github.com/user-attachments/assets/c83c244a-214b-4311-a6e4-5b76289cd4b6" />
Se observa la disposición de tres dispositivos (celulares) formando un triángulo dentro de una sala de laboratorio, con sus distancias medidas (2.23 m, 2.11 m y 2.27 m). Estos micrófonos captarán el sonido de las personas ubicadas estratégicamente, quienes emitirán frases distintas para simular un ambiente con múltiples fuentes sonoras. Durante la captura de la señal, se registrarán las voces y se medirá la relación señal/ruido (SNR) para evaluar la calidad del audio.

# Comparacion tecnicas ICA y Beamforming
El Análisis de Componentes Independientes (ICA) y el Beamforming son técnicas de procesamiento de señales con enfoques distintos.
- El ICA se usa para separar señales mezcladas en sus fuentes originales, basándose en la independencia estadística de cada una. Es común en neurociencia (EEG, MEG) y en procesamiento de audio para eliminar ruido y aislar señales específicas.
- El Beamforming, en cambio, mejora la captación o transmisión de señales en una dirección concreta, ajustando fase y amplitud. Se aplica en telecomunicaciones, radares y estudios cerebrales para localizar con precisión la actividad neuronal.
- En resumen, ICA separa señales superpuestas, mientras que Beamforming mejora la señal enfocándose en una dirección específica.

# Explicacion código Python:
- Importación de librerías:

```
import os  
import numpy as np
import matplotlib.pyplot as plt
from scipy.io import wavfile
from scipy.signal import butter, lfilter
from sklearn.decomposition import FastICA

```
os Para manejar rutas y verificar la existencia de archivos. numpy→ Para cálculos numéricos y procesamiento de señales.

NumPy para cálculos numéricos.

Matplotlib tpara graficar las señales.

scipy.io.wavfile para leer archivos de audio en formato WAV

scipy.signal.lfilter Para aplicar un filtro pasa banda y eliminar el ruido fuera del rango de voz.

sklearn.decomposition.FastICA Para aplicar Análisis de Componentes Independientes (ICA) y separar la voz del ruido.

- Definición de la ruta de la carpeta y archivos:
```
ruta_carpeta = r"C:\Users\Nicole\OneDrive\Pictures\Screenshots\SEXTO SEMESTRE\LABS SEÑALES\Lab3.3"
archivos = ["Martin.wav", "Gabi.wav", "Majo.wav", "ambiente.wav"]
```
Se define la ruta donde están los archivos de audio y se almacenan en ruta_carpeta. Luego, archivos es una lista con los nombres de los archivos de audio a procesar.

- Cálculo de la potencia de las señales.
 ```
potencias = {}

for archivo in archivos:
    try:
        sample_rate, audio_data = wavfile.read(f"{ruta_carpeta}\\{archivo}")

        if len(audio_data.shape) > 1:
            audio_data = audio_data[:, 0]  # Convertir a mono si es estéreo

        audio_data = audio_data.astype(np.float32)  # Convertir a flotante
        potencias[archivo] = np.mean(audio_data ** 2)  # Cálculo de potencia

    except FileNotFoundError:
        print(f"Error: No se encontró el archivo {archivo}.")
    except ValueError:
        print(f"Error: Archivo {archivo} no es un WAV válido.")
    except Exception as e:
        print(f"Error al procesar {archivo}: {e}")
``` 
Inicializa potencias = {} para almacenar la potencia de cada señal. Itera sobre cada archivo en archivosy: Se carga el archivo WAV con wavfile.read(), obteniendo: sample_rate: la frecuencia de muestreo en Hz. audio_data:los datos de la señal. (se toma solo el primer canal audio_data[:, 0]). Convierte la señal a flotante ( np.float32) para cálculos precisos.

<img width="302" alt="image" src="https://github.com/user-attachments/assets/a2eb43b4-3fdc-42ed-9535-07d5298779c7" />

Manejo de errores :

Si el archivo no existe, muestra Error: No se encontró el archivo.
Si el archivo no es válido, muestra Error: Archivo no es un WAV válido.

- Definir la potencia del ruido ambiente:
```  
potencia_ruido = potencias.get("ambiente.wav", None)

```
Se extrae la potencia del archivo ambiente.wav(considerado como ruido) para usarlo en el cálculo del SNR .

- Cálculo del SNR y generación de gráficos:
  
```
for archivo in archivos:
    try:
        sample_rate, audio_data = wavfile.read(f"{ruta_carpeta}\\{archivo}")

        if len(audio_data.shape) > 1:
            audio_data = audio_data[:, 0]

        audio_data = audio_data.astype(np.float32)

        potencia_senal = potencias.get(archivo, None)

        if potencia_ruido is not None and potencia_ruido > 0:
            if archivo == "ambiente.wav":
                snr_db = 0  
            else:
                snr_db = 10 * np.log10(potencia_senal / potencia_ruido) if potencia_senal > potencia_ruido else float('-inf')
        else:
            snr_db = float('-inf')

        print(f"SNR de {archivo}: {snr_db:.2f} dB")

```
Explicación del cálculo de SNR (Relación señal-ruido)
El SNR se calcula con la fórmula:

<img width="212" alt="image" src="https://github.com/user-attachments/assets/c2078987-e550-40b7-a2d7-2dc8785f6bce" />


Si archivo == "ambiente.wav", el SNR es 0 dB porque es la referencia de ruido.
Si la potencia de la señal es menor que la del ruido, se devuelve -inf(SNR indefinido).

- Normalización de la señal y graficado:
  
```
audio_data_norm = audio_data / np.max(np.abs(audio_data))  # Normalizar
        tiempo = np.arange(len(audio_data_norm)) / sample_rate  # Eje de tiempo

        plt.figure(figsize=(12, 5))

        # === Gráfica en el dominio del tiempo ===
        plt.subplot(1, 2, 1)
        plt.plot(tiempo, audio_data_norm, color=colores_tiempo[archivo], alpha=0.7)
        plt.xlabel("Tiempo (s)")
        plt.ylabel("Amplitud")
        plt.title(f"Señal Temporal - {archivo}")
        plt.grid(True, linestyle='--', alpha=0.6)
        plt.xlim(0, tiempo[-1])
```
- Normalización : Se divide la señal por su valor máximo para escalar entre -1y 1.
- Eje de tiempo : Se genera con np.arange(len(audio_data_norm)) / sample_rate.
- Se grafica la señal en el dominio del tiempo .

# Transformada de Fourier y gráfica en el dominio de la frecuencia:
 
```
N = len(audio_data_norm)
        fft_data = np.fft.fft(audio_data_norm)
        freqs = np.fft.fftfreq(N, d=1/sample_rate)

        fft_magnitude = np.abs(fft_data[:N//2])
        freqs = freqs[:N//2]

        plt.subplot(1, 2, 2)
        plt.plot(freqs, fft_magnitude, label=f"SNR: {snr_db:.2f} dB", 
                 color=colores_frecuencia[archivo], alpha=0.7)
        plt.xlabel("Frecuencia (Hz)")
        plt.ylabel("Magnitud")
        plt.title(f"Espectro de Frecuencias - {archivo}")
        plt.legend()
        plt.grid(True, linestyle='--', alpha=0.6)
        plt.xscale("log")
        plt.xlim(20, 10000)
```
- Se aplica la Transformada Rápida de Fourier (FFT) con np.fft.fft().
- Se extrae la mitad del espectro ( [:N//2]porque la FFT es simétrica).
- Se grafica el espectro en el dominio de la frecuencia en escala logarítmica.

# Mostrar gráficos:
  
```
plt.tight_layout()
        plt.show()
plt.tight_layout()

```
- Se cargarán los archivos de audio WAV.
- Se calcula la potencia de cada señal.
- Se calcula el SNR con referencia al ruido.
- Se normalizan las señales.
- Se grafican en el dominio del tiempo y frecuencia.
- El código permite comparar señales de audio en términos de energía y ruido con una visualización efectiva.

- *SNR gabi = 29.51 dB*
Este es un *SNR alto*, lo que indica que la señal está limpia y tiene poco ruido de fondo.
- *SNR martin = 29.84 dB*  
Muy similar a "gabi.wav", lo que indica una señal limpia y bien separada del ruido de fondo.
- *SNR Majo = 24.36 dB*
  
Este es un SNR más bajo que las señales anteriores, lo que indica que tiene *más ruido de fondo*
<img width="1280" height="581" alt="image" src="https://github.com/user-attachments/assets/2ae6db85-3c3b-4e1d-a2da-b629ccfd66d7" />

**gabi.wav** es una señal de voz con una buena relación señal-ruido y clara diferenciación en el espectro de frecuencias. Es adecuada para análisis de voz o procesamiento de señales de audio.

Presenta picos pronunciados en el rango *100 Hz - 1000 Hz*, característico de la voz humana.
La mayor parte de la energía se concentra en el rango de *bajas frecuencias (<1000 Hz)*, lo que es normal en señales de voz.

<img width="1280" height="520" alt="image" src="https://github.com/user-attachments/assets/643537dd-0ed9-495a-a19b-1bd69eee9e1c" />

martin.wav es otra señal de voz con buena calidad y alta relación señal-ruido. Su espectro de frecuencia muestra una mayor riqueza de armónicos, lo que podría interpretarse como una voz con más variaciones tonales.

<img width="1280" height="527" alt="image" src="https://github.com/user-attachments/assets/4b756807-325b-48c8-bdbc-3cc314a497cf" />
Majo.wav también es una señal de voz, pero con más ruido ambiental que las anteriores. Si se desea mejorar la calidad, se podría aplicar un filtro de reducción de ruido.



