# Hack The Box - Spookifier Write-up

## Descripción del Challenge

> There's a new trend of an application that generates a spooky name for you. Users of that application later discovered that their real names were also magically changed, causing havoc in their life. Could you help bring down this application?

El objetivo es investigar y explotar vulnerabilidades en esta aplicación.

---

![Iniciamos la maquina](Imagenes/1-Reconocimiento.jpg)

## Enumeración Inicial

Iniciamos el challenge accediendo a la dirección proporcionada:

```
http://83.136.254.165:36669
```
![Vista Web de la pagina](Imagenes/2-Reconocimiento.jpg)

La aplicación permite ingresar un nombre, el cual es modificado automáticamente por el navegador antes de ser mostrado.

![Nombre ingresado](Imagenes/3-Test%20Funcionalidad.jpg)

---

## Prueba de XSS

Para verificar si la aplicación es vulnerable a Cross-Site Scripting (XSS), ingresamos el siguiente payload:

```html
<script>alert(0)</script>
```

![Test XSS](Imagenes/4-Test%20XSS.jpg)

Al ejecutarlo, observamos que la alerta se dispara, lo que confirma la vulnerabilidad XSS.

![Resultado XSS](Imagenes/5-Resultado%20XSS.jpg)

---

## Análisis de la Aplicación

Tras inspeccionar los archivos descargados inicialmente, encontramos que la aplicación está dockerizada y desarrollada en **Python** utilizando **Flask** y **Mako templates**.

![Inspeccion Archivos descargados](Imagenes/6-Inspeccion%20Archivos%20Descargados.jpg)

Mako es un motor de plantillas utilizado en aplicaciones Python, lo que nos lleva a investigar posibles vulnerabilidades de **Server-Side Template Injection (SSTI)**.

![Que es Mako?](Imagenes/7-Que%20es%20Mako.jpg)

---

## Explotación de SSTI

Una buena referencia para encontrar payloads es el repositorio [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings).

![SSTI Mako GitHub?](Imagenes/8-SSTI%20Mako.jpg)

Probamos con el siguiente payload para verificar si el servidor evalúa expresiones en Mako:

```mako
${self.module.cache.util.os.system("id")}
```

![Test de Payload](Imagenes/9-Test%20Funcionalidad%20Payload.jpg)

Sin embargo, el resultado devuelto es `0`, lo que indica que el comando `id` no se ejecutó correctamente.

![Resultado de Payload](Imagenes/10-Resultado%201er%20Payload.jpg)

### Ajuste del Payload

Para corregirlo, modificamos el payload utilizando `popen` para leer la salida del comando:

```mako
${self.module.cache.util.os.popen('id').read()}
```

![Modificacion de Payload](Imagenes/11-Modificacion%20Payload.jpg)

Esto devuelve correctamente la información del usuario, confirmando que estamos ejecutando comandos en el servidor con permisos de **root**.

![Payload Modificado](Imagenes/12-Test%20Payload%20Modificado.jpg)

---

## Obtención de la Flag

Gracias al análisis previo del contenedor Docker, sabemos la ubicación de la flag. Procedemos a leer su contenido con el siguiente payload:

![Ubicacion Archivo Flag.txt](Imagenes/13-Ubicacion%20Archivo%20Flag.jpg)

```mako
${self.module.cache.util.os.popen('cat /flag.txt').read()}
```

Al ejecutarlo, obtenemos la flag correctamente.

![Obtencion de la Flag](Imagenes/14-Visualizacion%20Archivo%20Flag.jpg)

---

## Conclusión

En este challenge:

1. Identificamos una vulnerabilidad **XSS** en la aplicación.
2. Descubrimos que usa **Mako templates**, lo que nos llevó a explorar **SSTI**.
3. Explotamos la vulnerabilidad SSTI para ejecutar comandos en el servidor.
4. Finalmente, accedimos al archivo `flag.txt` y completamos el desafío.

Este reto demuestra la importancia de validar correctamente la entrada del usuario y reforzar la seguridad en la manipulación de plantillas dinámicas en aplicaciones web.

---

> **Nota:** Este write-up es únicamente con fines educativos. Siempre solicita permiso antes de realizar pruebas de seguridad en cualquier sistema.
