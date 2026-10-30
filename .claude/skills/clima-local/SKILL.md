---
name: clima-local
description: Consulta el clima actual con la API de Open-Meteo y lo reporta en una línea en español, con temperatura, sensación térmica, hora de la medición y fuente. Úsala siempre que el usuario pregunte por el clima, la temperatura, la sensación térmica o si hace calor o frío, ya sea en su localidad (por defecto, Mérida, Yucatán) o en otra ciudad, aunque no mencione la skill. También responde a /clima-local.
---

# Clima local

Reporta el clima actual a partir de una consulta real a Open-Meteo. El valor de esta skill es que el dato siempre proviene de la API, así que nunca lo estimes ni lo completes de memoria.

## 1. Determinar la ubicación

- Sin ciudad indicada: usa Mérida, Yucatán (`latitude=20.97`, `longitude=-89.62`, `timezone=America%2FMerida`).
- Con otra ciudad: obtén sus coordenadas y zona horaria con el geocodificador, sustituyendo los espacios del nombre por `+`:

  ```bash
  curl -s --max-time 20 "https://geocoding-api.open-meteo.com/v1/search?name=CIUDAD&count=1&language=es&format=json"
  ```

  Toma `latitude`, `longitude` y `timezone` de `results[0]`, y codifica la `/` de la zona horaria como `%2F`. Comprueba que `name`, `admin1` y `country` correspondan a lo pedido, porque hay ciudades homónimas. Si no hay `results`, di que no se encontró la ciudad.

## 2. Consultar el clima

Ejecuta con Bash, sustituyendo los valores si la ubicación no es la predeterminada:

```bash
curl -s --max-time 20 "https://api.open-meteo.com/v1/forecast?latitude=20.97&longitude=-89.62&current=temperature_2m,apparent_temperature&timezone=America%2FMerida"
```

Lee `current.temperature_2m`, `current.apparent_temperature` y `current.time`.

## 3. Reportar

Responde en una sola línea, con este formato:

`Mérida, Yucatán: 29.5 °C, sensación térmica de 35.0 °C, medición de las 11:15 del 30 de octubre de 2026 (hora local), fuente: Open-Meteo.`

## Si la consulta falla

Si `curl` falla o agota el tiempo, la salida está vacía, la respuesta trae `"error": true` o falta alguno de los campos, di que no se pudo obtener el clima e indica el motivo. No des cifras de consultas anteriores ni estimaciones: un dato inventado parece real y el usuario no podría distinguirlo.
