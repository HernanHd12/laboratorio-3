# laboratorio-3

Análisis de la Señal EMG:
Este laboratorio tiene como objetivo analizar la señal electromiográfica (EMG) de un músculo durante la fatiga muscular. El proceso se divide en varias etapas, desde la preparación del sujeto hasta el análisis espectral de la señal.

## Procedimientos:
•	Pedir al sujeto que realice una contracción muscular continua hasta llegar a la fatiga.
•	Registrar la señal EMG en tiempo real durante todo el proceso.
#### Código:
  import numpy as np
  import matplotlib.pyplot as plt
  from scipy.signal import butter, filtfilt

# Función para leer una señal biológica desde un archivo txt
	def leer_senal_desde_txt(nombre_archivo):
	senal = []
	with open(nombre_archivo, 'r') as archivo:
	for linea in archivo:
	try:
	valores = linea.split(',')
	for valor in valores:
	senal.append(float(valor.strip()))
	except ValueError:
	print(f"Línea no numérica ignorada: {linea.strip()}")
	continue
	return np.array(senal)

# Leer la señal desde el archivo

	nombre_archivo = 'C:/Users/herna/OneDrive/Escritorio/lab señales/senalO.txt'
	senal = leer_senal_desde_txt(nombre_archivo)
	3. Filtrado de la Señal
	Procedimientos:
	•	Aplicar un filtro pasa altas para eliminar componentes de baja frecuencia (ruido asociado a la línea base o al movimiento).
	•	Utilizar un filtro pasa bajas para eliminar frecuencias altas no deseadas, como el ruido electromagnético o de interferencia de alta frecuencia.

[![Figure-2024-11-15-101438.png](https://i.postimg.cc/LsYQHkk5/Figure-2024-11-15-101438.png)](https://postimg.cc/7C4n92Wr)


# Función para aplicar un filtro Butterworth
	def aplicar_filtro(senal, tipo, fc, fs, orden=5):
	   nyquist = 0.5 * fs
	  normal_fc = fc / nyquist if isinstance(fc, (int, float)) else [f / nyquist for f in fc]
	   b, a = butter(orden, normal_fc, btype=tipo, analog=False)
	   senal_filtrada = filtfilt(b, a, senal)
	   return senal_filtrada

# Parámetros de la señal
frecuencia_muestreo = 250  # Frecuencia de muestreo en Hz

# Aplicar filtros pasa altos y pasa bajos en serie
	fc_pasa_bajos = 50  # Frecuencia de corte del filtro pasa bajos en Hz
	fc_pasa_altos = 1   # Frecuencia de corte del filtro pasa altos en Hz

	senal_filtrada = aplicar_filtro(senal, 'high', fc_pasa_altos, frecuencia_muestreo)
	senal_filtrada = aplicar_filtro(senal_filtrada, 'low', fc_pasa_bajos, frecuencia_muestreo)
[![Figure-2024-11-15-101503.png](https://i.postimg.cc/KY6q4KQN/Figure-2024-11-15-101503.png)](https://postimg.cc/Y4xfsC5G)
4. Aventanamiento
Procedimientos:
•	Dividir la señal registrada en ventanas de tiempo, usando una técnica de aventanamiento como la ventana de Hamming o Hanning.
•	Realizar el análisis espectral de cada ventana utilizando la Transformada de Fourier (FFT) para obtener el espectro de frecuencias en intervalos específicos de la señal EMG.

# Aplicar ventana de Hanning a la señal filtrada
ventana_hanning = np.hanning(len(senal_filtrada))
senal_filtrada_hanning = senal_filtrada * ventana_hanning

[![Figure-2024-11-15-101511.png](https://i.postimg.cc/C1bv8FQV/Figure-2024-11-15-101511.png)](https://postimg.cc/G8hJw1RM)

# Dividir la señal en ventanas
duracion_ventana = 1  # Duración de cada ventana en segundos
muestras_por_ventana = duracion_ventana * frecuencia_muestreo
num_ventanas = len(senal) // muestras_por_ventana

# Función para realizar la Transformada de Fourier (FFT)
	def realizar_fft(senal, fs):
	   n = len(senal)
	   k = np.arange(n)
	   T = n / fs
	   frq = k / T  # Dos lados del rango de frecuencia
	   frq = frq[range(n // 2)]  # Un lado del rango de frecuencia
	   Y = np.fft.fft(senal) / n  # FFT normalizada
	   Y = Y[range(n // 2)]
	   return frq, abs(Y)

5. Análisis Espectral

### Procedimientos:
•	Observar cómo cambia el espectro de la señal en cada ventana conforme se acerca la fatiga muscular.
•	Evaluar la disminución de la frecuencia mediana en cada ventana como indicador de la fatiga.
•	Implementar una prueba de hipótesis para verificar si el cambio en la mediana es significativo estadísticamente.
# Función para calcular la frecuencia mediana
	def frecuencia_mediana(espectro, frq):
	   espectro_acumulado = np.cumsum(espectro)
	   mediana_idx = np.where(espectro_acumulado >= espect

