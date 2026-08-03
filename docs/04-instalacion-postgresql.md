# Instalación de PostgreSQL 16

## Consideración de versión

Ubuntu Server 26.04 LTS incluye una versión de PostgreSQL distinta en sus repositorios de distribución. Para conservar PostgreSQL 16, definido para el laboratorio, se utilizó el repositorio APT oficial del proyecto PostgreSQL.

## Instalación en ambos servidores

```bash
sudo apt install -y postgresql-common ca-certificates
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
sudo apt update
sudo apt install -y postgresql-16
```

## Verificación

```bash
psql --version
sudo systemctl is-active postgresql
sudo pg_lsclusters
```

El resultado confirmó:

- Cliente `psql` versión 16.
- Servicio PostgreSQL activo.
- Clúster `16 main` en el puerto `5432`.

## Evidencias sugeridas

- `evidencias/06-postgresql-primary.png`
- `evidencias/07-postgresql-standby.png`
