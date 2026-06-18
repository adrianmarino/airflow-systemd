# [Airflow](https://airflow.apache.org/) systemd (v3.x compatible)

Install airflow server as systemd daemon under linux. This run with your regular linux user.

## Requirements

* **Miniconda**
* **MySQL 8.0** (Requerido para Airflow 3.x. MariaDB no es compatible con las migraciones de XCom).
* **Docker** (Para levantar MySQL 8 de forma aislada).

## Infraestructura de Base de Datos

El sistema utiliza MySQL 8.0 corriendo en un contenedor Docker:
* **Puerto Host:** 3307
* **Imagen:** `mysql:8.0`
* **Persistencia:** El contenedor está configurado con `--restart unless-stopped` para sobrevivir a reinicios del sistema.

## Setup airflow

**Step 1**: Clone repo.

```bash
$ cd ~
$ git clone https://github.com/adrianmarino/airflow-systemd.git
$ mv airflow-systemd airflow
$ cd airflow
```

**Step 2**: Levantar Base de Datos (MySQL 8).

```bash
$ docker run -d \
  --name airflow_mysql8 \
  --restart unless-stopped \
  -e MYSQL_ROOT_PASSWORD=root_pass \
  -e MYSQL_DATABASE=airflow_db \
  -e MYSQL_USER=airflow_user \
  -e MYSQL_PASSWORD=lv3jg6 \
  -p 3307:3306 \
  mysql:8.0 \
  --default-authentication-plugin=mysql_native_password
```
 
**Step 3**: Create conda environment required to run airflow.

```bash
$ conda env create -f environment.yml
```

**Step 4**: Copy service file user level systemd config path:

```bash
$ cp airflow.service ~/.config/systemd/user/
```

**Step 5**: Refresh systemd daemon with updated config.

```bash
$ systemctl --user daemon-reload
```

**Step 6**: Start service on boot.

```bash
$ systemctl --user enable airflow
```

**Step 7**: Config db connection string under `airflow.cfg`: 

```ini
sql_alchemy_conn = mysql://airflow_user:lv3jg6@127.0.0.1:3307/airflow_db
```

**Step 8**: Initialize db:

```bash
$ airflow db migrate
```

**Step 9**: Start airflow as systemd daemon.

```bash
$ systemctl --user start airflow
```

## Notas Airflow 3.x
* El comando `webserver` ha sido reemplazado por `api-server`.
* El script `bin/start` gestiona automáticamente la ejecución de `scheduler` y `api-server`.
