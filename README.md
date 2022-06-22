# [Airflow](https://airflow.apache.org/) systemd

Install airflow server as systemd daemon under linux. This run with your regular linux user.

## Requirements

* miniconda
* mariadb/mysql

## Setup airflow

**Step 1**: Clone repo.

```bash
$ cd ~
$ git clone https://github.com/adrianmarino/airflow-systemd.git
$ mv airflow-systemd airflow
$ cd airflow
```

**Step 2**: Create conda environment required to run airflow.

```bash
$ conda env update -f environment.yml
```

**Step 3**: Copy service file user level systemd config path:

```bash
$ cp airflow.service ~/.config/systemd/user/
```

**Step 4**: Import required common shell env variables:

```bash
$ echo "source ~/airflow/.shell.airflowrc" >> ~/.bashrc 
or
$ echo "source ~/airflow/.shell.airflowrc" >> ~/.zshrc
```

**Step 5**: Refresh systemd daemon with updated config.

```bash
$ systemctl --user daemon-reload
```

**Step 6**: Start service on boot.

```bash
$ systemctl --user enable airflow
```

**Step 7**: Start airflow as systemd daemon.

```bash
$ systemctl --user start airflow
```

**Step 8**: create a `~/airflow/dags` directory where will all dags be stored.

## Config file

`config.conf`:
```bash
CONDA_PATH="/opt/miniconda3"
ENV="airflow"
PORT="9090"
```

## Setup database

**Step 1**: Create database and an user used by airflow server to access to database.

```bash
CREATE DATABASE airflow_db;
CREATE USER airflow_user;
ALTER USER 'airflow_user'@'localhost' IDENTIFIED BY 'airflow_pass';
GRANT ALL PRIVILEGES ON airflow_db.* TO 'airflow_user'@'%';
FLUSH PRIVILEGES;
```

**Step 2**: Config db connection string under `airflow.cfg`: 

```init
sql_alchemy_conn = mysql://airflow_user:airflow_pass@localhost/airflow_db
```

**Step 3**: Initialize db:

```bash
$ airflow initdb
```

**Step 3**: Create an admin airflow webserver user:

```bash
$ airflow users create --username username --firstname Fistname --lastname LastName --role Admin --email my.email@gmail.com
```
