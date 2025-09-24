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
-Importación de librerías:

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

