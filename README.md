# [Airflow](https://airflow.apache.org/) systemd

Install airflow server as systemd daemon under linux. This run with your regular linux user.

## Requirements

* miniconda
* mariadb >= 10.2.7


## Setup airflow

**Step 1**: Clone repo.

```bash
$ cd ~
$ git clone https://github.com/adrianmarino/airflow-systemd.git
$ mv airflow-systemd airflow
$ cd airflow
```

**Step 2**: Install mysql dev tools.

```bash
$ sudo apt install libmysqlclient-dev
```
 
**Step 3**: Create conda environment required to run airflow.

```bash
$ conda env create -f environment.yml
```

**Step 4**: Copy service file user level systemd config path:

```bash
$ cp airflow.service ~/.config/systemd/user/
```

**Step 5**: Import required common shell env variables:

```bash
$ echo "source ~/airflow/.shell.airflowrc" >> ~/.bashrc 
or
$ echo "source ~/airflow/.shell.airflowrc" >> ~/.zshrc
```

**Step 6**: Replace `__HOME__` with your home path.

```bash
$ sed -i 's/__HOME__/YOUR_HOME_PATH/g' $HOME/airflow/airflow.cfg
```

**Step 7**: Refresh systemd daemon with updated config.

```bash
$ systemctl --user daemon-reload
```

**Step 8**: Start service on boot.

```bash
$ systemctl --user enable airflow
```

**Step 9**: Create database and user used by airflow server to access to database schema.

```bash
CREATE DATABASE airflow_db;
CREATE USER airflow_user;
GRANT ALL PRIVILEGES ON airflow_db.* TO 'airflow_user'@localhost IDENTIFIED BY 'airflow_pass';
FLUSH PRIVILEGES;
```

**Step 10**: Config db connection string under `airflow.cfg`: 

```init
sql_alchemy_conn = mysql://airflow_user:airflow_pass@localhost/airflow_db
```

**Step 11**: Initialize db:

```bash
$ airflow db init
```

**Step 12**: Create an admin airflow webserver user:

```bash
$ airflow users create \
  --username username \
  --firstname Fistname \
  --lastname LastName \
  --role Admin \
  --email my.email@gmail.com
```

**Step 13**: Check miniconda home path in `config.conf`.

```bash
CONDA_PATH="/opt/miniconda3"
```

**Step 14**: Start airflow as systemd daemon.

```bash
$ systemctl --user start airflow
```

**Step 15**: create a `~/airflow/dags` directory where will all dags be stored.

