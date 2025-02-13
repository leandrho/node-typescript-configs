
Cómo usar **Docker** y **Docker Compose** para crear contenedores de base de datos PostgreSQL.

---

## 1️ **Instalar Docker y Docker Compose**
Antes de comenzar, asegúrate de que tienes Docker y Docker Compose instalados en tu sistema.

###  **Instalar Docker**
- En **Linux**:
  ```sh
  sudo apt update
  sudo apt install docker.io
  sudo systemctl enable --now docker
  ```
- En **Mac/Windows**:
  Descarga e instala Docker Desktop desde [https://www.docker.com/](https://www.docker.com/).

Para verificar que Docker está funcionando:
```sh
docker --version
```

###  **Instalar Docker Compose**
Docker Compose ya está incluido en Docker Desktop.
Si estás en Linux, instálalo manualmente:
```sh
sudo apt install docker-compose
```
Verifica que está instalado:
```sh
docker-compose --version
```

---

## 2️ **Correr PostgreSQL con Docker**
Si solo quieres ejecutar PostgreSQL en un contenedor simple, usa este comando:
```sh
docker run -d \
  --name postgres-container \
  -e POSTGRES_USER=myuser \
  -e POSTGRES_PASSWORD=mypassword \
  -e POSTGRES_DB=mydatabase \
  -p 5432:5432 \
  postgres:latest
```
**Explicación de los parámetros**:
- `-d` → Corre el contenedor en segundo plano.
- `--name postgres-container` → Asigna un nombre al contenedor.
- `-e POSTGRES_USER=myuser` → Usuario de la base de datos.
- `-e POSTGRES_PASSWORD=mypassword` → Contraseña del usuario.
- `-e POSTGRES_DB=mydatabase` → Nombre de la base de datos.
- `-p 5432:5432` → Mapea el puerto 5432 del host al contenedor.
- `postgres:latest` → Imagen oficial de PostgreSQL en su última versión.

Para verificar que el contenedor está corriendo:
```sh
docker ps
```
Para conectarte a PostgreSQL dentro del contenedor:
```sh
docker exec -it postgres-container psql -U myuser -d mydatabase
```

---

## 3️ **Usar Docker Compose para PostgreSQL**
Si prefieres una configuración más estructurada, usa **Docker Compose**.
Crea un archivo `docker-compose.yml`:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:latest
    container_name: postgres-container
    restart: always
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

**Explicación**:
- `services.postgres` → Define el servicio PostgreSQL.
- `image: postgres:latest` → Usa la última versión de PostgreSQL.
- `container_name: postgres-container` → Nombre del contenedor.
- `restart: always` → Reinicia el contenedor automáticamente si se detiene.
- `environment` → Variables de entorno para configurar la base de datos.
- `ports: "5432:5432"` → Expone PostgreSQL en el puerto 5432.
- `volumes` → Usa un volumen llamado `postgres_data` para persistencia.

### **Ejecutar con Docker Compose**
En la misma carpeta donde guardaste el `docker-compose.yml`, ejecuta:
```sh
docker-compose up -d
```
Esto levantará el contenedor con PostgreSQL.

Para ver los contenedores corriendo:
```sh
docker-compose ps
```

Para detener los contenedores:
```sh
docker-compose down
```
Si quieres eliminar los volúmenes también:
```sh
docker-compose down -v
```

---

## 4️ **Conectarse a PostgreSQL desde una app**
Si tienes **pgAdmin, DBeaver** o una aplicación que use PostgreSQL, usa estos datos para conectarte:

- **Host:** `localhost`
- **Puerto:** `5432`
- **Usuario:** `myuser`
- **Contraseña:** `mypassword`
- **Base de datos:** `mydatabase`

Si corres PostgreSQL dentro de Docker en otra máquina, usa su dirección IP en vez de `localhost`.

---

## 5️ **Añadir pgAdmin (Opcional)**
Si quieres administrar PostgreSQL visualmente, agrega `pgadmin` a `docker-compose.yml`:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:latest
    container_name: postgres-container
    restart: always
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin-container
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: adminpassword
    ports:
      - "5050:80"

volumes:
  postgres_data:
```

Después de correr `docker-compose up -d`, entra a **pgAdmin** en:
[http://localhost:5050](http://localhost:5050)
Usuario: `admin@example.com`
Contraseña: `adminpassword`

---

## 6️ **Backup y Restore en Docker**
Para hacer un **backup** de la base de datos:
```sh
docker exec -t postgres-container pg_dump -U myuser -d mydatabase > backup.sql
```

Para **restaurar** un backup:
```sh
cat backup.sql | docker exec -i postgres-container psql -U myuser -d mydatabase
```

---

## 7️ **Eliminar Contenedores**
Si quieres eliminar el contenedor PostgreSQL sin borrar los datos:
```sh
docker rm -f postgres-container
```

Si quieres borrar todo (incluyendo datos):
```sh
docker volume rm postgres_data
```

---

## **Conclusión**
Ahora tienes un entorno PostgreSQL funcionando con Docker.
Puedes usar un solo contenedor o estructurarlo con `docker-compose.yml` y añadir herramientas como pgAdmin.

