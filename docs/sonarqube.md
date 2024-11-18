# **Cómo configurar SonarQube desde cero y realizar análisis de código**

Este documento describe paso a paso cómo configurar SonarQube en un entorno Ubuntu 22.04 LTS utilizando Docker y realizar análisis de código. Está destinado a cualquier persona que desee implementar SonarQube de manera sencilla. 

---

## **Requisitos previos**

- **Virtual Machine (VM)**: Debes tener acceso a una VM de cualquier proveedor para poder montar el servidor de SonarQube allí. También puedes realizar análisis de código localmente, revisa este [vídeo](https://www.youtube.com/watch?v=6vdRvz_LnbQ).
- **Docker**: Asegúrate de tener Docker instalado en el sistema. Si no lo tienes, instala Docker Engine siguiendo las instrucciones oficiales: [Instalar Docker en Ubuntu](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

---

## **Comprendiendo SonarQube**

SonarQube es una plataforma para el análisis continuo de código que permite inspeccionar la calidad del código y detectar problemas. Sus componentes principales son:

- **SonarQube Server**: Almacena métricas y resultados en una base de datos.
- **SonarQube Scanner**: Analiza el código fuente localmente y envía los resultados al servidor.

![Arquitectura de SonarQube](https://raw.githubusercontent.com/software2uis/.github/main/profile/sonarqube-architecture.webp)

---

## **Pasos para configurar SonarQube**

### **1. Crear y conectarse a un Droplet en DigitalOcean (o cualquier otra VM)**

El programa de educación de GitHub ofrece **$200 dólares en créditos** para usar en DigitalOcean. Puedes solicitar acceso desde [GitHub Education](https://education.github.com/pack).

#### **Paso 1: Crear un Droplet**

1. Inicia sesión en [DigitalOcean](https://www.digitalocean.com/).
2. Haz clic en **Create** > **Droplets**.
3. Configura tu Droplet:
   - **Imagen**: Ubuntu 22.04 LTS.
   - **Plan**: Selecciona el plan básico ($4/mes).
   - **Autenticación**: Elige **SSH Key** y añade tu clave pública.
     - Si no tienes una clave SSH, genera una con:

       ```bash
       ssh-keygen -t rsa -b 4096 -C "tu_correo@example.com"
       ```

     - La clave pública se encuentra en `~/.ssh/id_rsa.pub`.

4. Haz clic en **Create Droplet** y espera a que esté listo.

#### **Paso 2: Conectarse al Droplet**

1. Obtén la **dirección IP pública** de tu Droplet.
2. Conéctate vía SSH usando:

   ```bash
   ssh -i ~/.ssh/id_rsa root@DIRECCION_IP_DEL_DROPLET
   ```

   - Reemplaza `DIRECCION_IP_DEL_DROPLET` con la IP de tu Droplet.

3. Si aparece una advertencia de host desconocido, escribe `yes` para continuar.

#### **Paso 3: Solución de problemas**

- Si recibes un mensaje de **"REMOTE HOST IDENTIFICATION HAS CHANGED!"**, ejecuta:

  ```bash
  ssh-keygen -f "~/.ssh/known_hosts" -R "DIRECCION_IP_DEL_DROPLET"
  ```

---

### **2. Instalar Docker**

Ejecuta los siguientes comandos para instalar Docker:

```bash
sudo apt-get update -y
sudo apt-get install -y \
  ca-certificates \
  curl \
  gnupg \
  lsb-release

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update -y
sudo apt-get install -y \
  docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

---

### **3. Instalar SonarQube**

#### **Paso 1: Descargar la imagen de SonarQube**

```bash
docker pull sonarqube
```

#### **Paso 2: Ejecutar la base de datos PostgreSQL**

```bash
docker run -d --name sonarqube-db \
  -e POSTGRES_USER=sonar \
  -e POSTGRES_PASSWORD=sonar \
  -e POSTGRES_DB=sonarqube \
  postgres:alpine
```

#### **Paso 3: Ejecutar SonarQube**

```bash
docker run -d --name sonarqube -p 9000:9000 \
  --link sonarqube-db:db \
  -e SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonarqube \
  -e SONAR_JDBC_USERNAME=sonar \
  -e SONAR_JDBC_PASSWORD=sonar \
  sonarqube
```

##### **Nota**: Si el contenedor de SonarQube se detiene automáticamente, es posible que necesites ajustar `vm.max_map_count`.

##### **Solución al error de `vm.max_map_count`**

Elasticsearch, parte de SonarQube, requiere que `vm.max_map_count` sea al menos **262144**.

1. Establece el valor temporalmente:

   ```bash
   sudo sysctl -w vm.max_map_count=262144
   ```

2. Para hacerlo permanente, añade la siguiente línea al archivo `/etc/sysctl.conf`:

   ```bash
   echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf
   ```

3. Verifica el valor:

   ```bash
   sysctl vm.max_map_count
   ```

4. Reinicia el contenedor de SonarQube si es necesario:

   ```bash
   docker start sonarqube
   ```

---

### **4. Verificar que los contenedores estén en ejecución**

```bash
docker ps
```

Deberías ver ambos contenedores `sonarqube` y `sonarqube-db` en ejecución.

---

### **5. Acceder a SonarQube desde el navegador**

#### **Paso 1: Configurar el firewall**

En el Droplet:

1. Habilita UFW si no lo has hecho:

   ```bash
   sudo ufw enable
   ```

2. Permite conexiones en los puertos necesarios:

   ```bash
   sudo ufw allow 22
   sudo ufw allow 9000
   sudo ufw reload
   ```

En DigitalOcean:

1. Ve a **Networking** > **Firewalls**.
2. Asegúrate de que el firewall permite tráfico entrante en el puerto 9000 (TCP).

#### **Paso 2: Acceder a SonarQube**

- En tu navegador, visita: `http://DIRECCION_IP_DEL_DROPLET:9000`
- Inicia sesión con:
  - **Usuario**: `admin`
  - **Contraseña**: `admin`
- Cambia la contraseña cuando se te solicite.

---

## **Integrar SonarQube con GitHub**

### **1. Crear una GitHub App**

Sigue las instrucciones para registrar una GitHub App:

- [Registrar una GitHub App](https://docs.github.com/es/apps/creating-github-apps/registering-a-github-app)

Asigna los siguientes permisos a la App:

- **Repository permissions**:
  - Contents: Read-only
  - Metadata: Read-only
  - Pull requests: Read & write
  - Commit statuses: Read & write
- **Organization permissions** (si aplicable):
  - Members: Read-only

Más detalles en la documentación de SonarQube: [Configuración de GitHub App](https://docs.sonarsource.com/sonarqube/latest/devops-platform-integration/github-integration/setting-up-at-global-level/setting-up-github-app/)

### **2. Añadir la GitHub App a SonarQube**

En SonarQube:

1. Ve a **Administración > Configuración General > Integración con DevOps Platform > GitHub**.
2. Configura los detalles de tu GitHub App:
   - **Application ID**
   - **Private Key**
   - **Webhook Secret**

### **3. Importar repositorios desde GitHub**

1. En SonarQube, ve a **Proyectos > Crear Proyecto**.
2. Selecciona **Importar desde GitHub**.
3. Autoriza a SonarQube para acceder a tu cuenta u organización.
4. Selecciona los repositorios que deseas analizar.

### **4. Configurar proyectos para Clean as You Code**

- Se recomienda utilizar la configuración de **Reference Branch** si trabajas con ramas de características.
- Esto permitirá comparar cambios con la rama principal y mantener un código de alta calidad.

---

## **Configurar el análisis de código**

### **1. Generar un Token en SonarQube**

- Ve a **Mi Cuenta > Tokens de Seguridad**.
- Genera un nuevo token y guárdalo para usarlo en GitHub.

### **2. Configurar secretos en GitHub**

Para cada repositorio (o a nivel de organización):

1. Ve a **Settings > Secrets and variables > Actions**.
2. Añade los siguientes secretos:
   - `SONAR_TOKEN`: Tu token de SonarQube.
   - `SONAR_HOST_URL`: La URL de tu servidor SonarQube (`http://DIRECCION_IP_DEL_DROPLET:9000`).

### **3. Configurar GitHub Actions**

##### **Nota**: Estos .yml los provee SonarQube, no es obligatorio usar los siguientes, revisa este [vídeo](https://youtu.be/6zvBuZr8CeI?t=333).

#### **Para Micro Front Ends (MFE)**

Crea un archivo de workflow `.github/workflows/build.yml` con el siguiente contenido:

```yaml
name: Build

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  build:
    name: Build and analyze
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Deshabilitar clones superficiales para mejor análisis
      - uses: sonarsource/sonarqube-scan-action@v3
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
```

#### **Para Microservicios (MSV) con MongoDB**

1. Asegúrate de que la variable `MONGODBURI` esté definida en los secretos de GitHub.

2. En `application.properties`:

   ```
   spring.data.mongodb.uri=${MONGODBURI}
   ```

3. Archivo de workflow `.github/workflows/build.yml`:

```yaml
name: Build

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build and analyze
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: 17
          distribution: 'zulu'
      - name: Cache SonarQube packages
        uses: actions/cache@v4
        with:
          path: ~/.sonar/cache
          key: ${{ runner.os }}-sonar
      - name: Cache Maven packages
        uses: actions/cache@v4
        with:
          path: ~/.m2
          key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
      - name: Build and analyze
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
          MONGODBURI: ${{ secrets.MONGODBURI }}
        run: mvn -B verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
          -Dsonar.projectKey=TU_PROYECTO -Dsonar.projectName='Nombre del Proyecto'
```

### **5. Ejecutar el workflow y verificar**

- Realiza un commit en tu repositorio para activar el workflow de GitHub Actions.
- Verifica que el análisis se ejecuta correctamente y los resultados aparecen en SonarQube.

---

¡Con estos pasos, has configurado exitosamente SonarQube desde cero y establecido análisis de código continuo para tus proyectos en GitHub!

---
