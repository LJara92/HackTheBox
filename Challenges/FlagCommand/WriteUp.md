# 🏕️ Hack The Box Challenge: FlagCommand

> _"Embark on the Dimensional Escape Quest where you wake up in a mysterious forest maze... Will you conquer the enchanted maze or be lost forever?"_

---

## 🎯 Descripción del reto

FlagCommand es un reto tipo *web* interactivo, ambientado en una especie de terminal dentro de una historia. El objetivo es encontrar una flag escondida usando comandos ocultos en una interfaz estilo aventura de texto.

---

## 🌐 Paso 1: Acceder a la aplicación

Iniciamos la máquina y accedemos a la IP y puerto proporcionados:

```
http://83.136.252.13:47575
```

![Iniciamos la maquina](Imagenes/Imagen1.jpg)


Al abrir la página, se nos presenta una interfaz con una historia de introducción y una consola interactiva en la parte inferior.

![Visualizacion pagina](Imagenes/Imagen2.jpg)

---

## 🧪 Paso 2: Pruebas iniciales

Intentamos comandos comunes como:

```
whoami
ls
$(id)
```

![Comandos basicos](Imagenes/Imagen3.jpg)

Pero la terminal responde que esos comandos no están disponibles. Usamos `help` para ver qué opciones están disponibles.

```bash
>> help
```

Se listan algunos comandos como:
- `start`
- `info`
- `clear`
- `restart`

![Comandos basicos](Imagenes/Imagen4.jpg)

---

## 🎮 Paso 3: Iniciar el juego

Usamos `start` para comenzar la aventura:

```bash
>> start
```
El juego muestra un entorno tipo historia de texto con opciones como:

```
HEAD NORTH
HEAD SOUTH
HEAD EAST
HEAD WEST
```

Ninguna acción parece llevarnos a la flag, así que comenzamos a investigar más a fondo usando las herramientas de desarrollo del navegador.

![Iniciamos juego](Imagenes/Imagen5.jpg)

---

## 🕵️ Paso 4: Inspección con DevTools

Presionamos `F12` y revisamos las pestañas:

### 📁 **Sources:**
Vemos el código del juego y algunas constantes interesantes como `GAME_WON`, pero no contiene la flag.

![F12](Imagenes/Imagen7.jpg)

### 🌐 **Network:**
Aquí encontramos algo clave: una petición a una API `OPTIONS`, lo cual indica que hay una API que podríamos investigar.

![Network](Imagenes/Imagen8.jpg)
![Comando secreto](Imagenes/Imagen9.jpg)

Si abrimos la direccion de la API en una nueva prestaña, encontramos comandos no listados anteriormente, incluyendo un **comando secreto**:

```text
"secret": [
  "Blip-blop, in a pickle with a hiccup! Shmiggity-shmack"
]
```

![Abrimos la API](Imagenes/Imagen10.jpg)

---

## 🔐 Paso 5: Usar el comando secreto

Volvemos a la consola del juego e introducimos el comando:

```bash
>> Blip-blop, in a pickle with a hiccup! Shmiggity-shmack
```

![Flag obtenida](Imagenes/Imagen11.jpg)

🎉 ¡Aparece un archivo nuevo en el panel de red llamado `monitor`! Dentro del `Preview` de su respuesta, encontramos la flag:

```bash
HTB{D3v310p3r_t0015_4z3_b35t__t0015_wh4t_de_y}
```

![Final](Imagenes/Imagen12.jpg)

---

## 🧠 Lecciones aprendidas

- Siempre inspeccionar la red y el código fuente en retos tipo web.
- Las APIs a menudo contienen rutas, datos o comandos ocultos.
- En este caso, la flag solo podía obtenerse al descubrir e invocar un comando oculto.

---

## 🧰 Herramientas utilizadas

- Navegador + DevTools (F12)
- Curiosidad e intuición 🕵️‍♂️

---

## 📁 Recursos

- [Hack The Box](https://www.hackthebox.com/)

---

_Repositorio completo disponible en:_

👉 [GitHub - HTB Write-ups](https://github.com/LJara92/HackTheBox/tree/main)

