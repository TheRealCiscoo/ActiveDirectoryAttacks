# NTLM/SMB Relay

# ¿Cómo surge un ataque NTLM/SMB Relay?

---

El SMB Relay es una técnica de ataque que se utiliza para realizar un ataque de tipo "man-in-the-middle". Funciona en el protocolo SMB (Server Message Block), que es un protocolo de red que permite compartir recursos como archivos e impresoras entre nodos en una red.

En un escenario de SMB Relay, el atacante se sitúa entre la víctima y el servidor de destino. Cuando la víctima intenta autenticarse en el servidor, el atacante captura la solicitud de autenticación y la reenvía al servidor. El servidor, pensando que la solicitud proviene de la víctima, responde con un desafío NTLM. El atacante captura este desafío y lo reenvía a la víctima.

La víctima, piensa que el desafío proviene del servidor, responde con el hash NTLM de su contraseña y el atacante captura esta respuesta. De esta manera, el atacante puede capturar el hash NTLM de la contraseña del usuario.

# Explotación

---

Para nuestro primer paso necesitamos poner a escuchar ese servidor malisioso que se mantenga a la espera de un intento de acceso a un recurso inexistente para solicitar al usuario que se autentique y entonces capturar el hash. Para esta tarea vamos a utilizar la herramienta `responder` y solo vamos a indicarle unos parámetros básicos.

Los parámetros son los siguientes: -I para indicar la interfaz por la que va a escuchar, -w se utiliza para activar un servidor WPAD y -d habilita las respuestas a las solicitudes DHCP broadcast.

![Untitled](images/Untitled.png)

Con estas simples opciones ya estaríamos a la escucha en la red. Ahora solo queda esperar a que un usuario intente acceder a algún recurso.

![Untitled](images/Untitled%201.png)

Ahora desde la máquina victima simularemos que el usuario intenta acceder a un recurso que no existe. presion

![Untitled](images/Untitled%202.png)

Aquí ya hemos capturado el hash NTLM del usuario **ketti.andrea**.

![Untitled](images/Untitled%203.png)

Ahora solo tendrémos que crackearlo.

# Crackeo de las contraseñas

---

Utilizaremos hashcat para crackear hash, el módulo para este hash del tipo Kerberos es el `5600`. Sustituimos HASH_FILE por la ruta de nuestro archivo hash y el DICTIONARY por la ruta del diccionario que vamos a usar para crackear el hash.

```bash
hashcat -a 0 -m 5600 HASH_FILE DICTIONARY -O
```

![Untitled](images/Untitled%204.png)