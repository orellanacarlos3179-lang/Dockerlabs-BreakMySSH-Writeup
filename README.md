# Resumen Técnico: Resolución de Máquina BreakMySSH (Dockerlabs)

## Descripción General
* **Plataforma:** Dockerlabs
* **Máquina:** BreakMySSH
* **Dificultad:** Muy Fácil
* **Objetivo:** Obtener acceso con privilegios de root.
* **Vector de Ataque:** Fuerza bruta contra el servicio OpenSSH (Puerto 22) seguido de un acceso directo con la cuenta de superusuario.

---

## Flujo de Trabajo (Paso a Paso)

### 1. Despliegue de la Máquina
Se inicia el laboratorio en el entorno local utilizando el script de autodeploy de Dockerlabs.

```bash
sudo auto_deploy breakmyssh.tar
