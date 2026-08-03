# Configuración y prueba de la replicación

## Usuario de replicación

En `pg-primary` se creó un rol exclusivo:

```sql
CREATE ROLE replicator WITH REPLICATION LOGIN;
\password replicator
```

La contraseña no se almacena en este repositorio.

## Parámetros del principal

```bash
sudo -u postgres psql -c "ALTER SYSTEM SET listen_addresses = '*';"
sudo -u postgres psql -c "ALTER SYSTEM SET wal_level = 'replica';"
sudo -u postgres psql -c "ALTER SYSTEM SET max_wal_senders = 10;"
sudo -u postgres psql -c "ALTER SYSTEM SET max_replication_slots = 10;"
```

En `/etc/postgresql/16/main/pg_hba.conf` se autorizó únicamente la IP de la réplica:

```text
host    replication    replicator    192.168.56.11/32    scram-sha-256
```

Después se reinició PostgreSQL:

```bash
sudo systemctl restart postgresql
```

## Creación de la réplica

El clúster vacío de `pg-standby` se detuvo y conservó como respaldo. A continuación se obtuvo una copia base desde `pg-primary`:

```bash
sudo -u postgres pg_basebackup -h 192.168.56.10 -D /var/lib/postgresql/16/main -U replicator -P -R -X stream -W
```

La opción `-R` creó la señal de réplica y guardó los parámetros de conexión. La opción `-X stream` transmitió los WAL necesarios durante la copia.

## Verificación técnica

En `pg-standby`:

```bash
sudo -u postgres psql -c "SELECT pg_is_in_recovery();"
```

Resultado: `t`.

En `pg-primary`:

```bash
sudo -u postgres psql -c "SELECT client_addr, state, sync_state FROM pg_stat_replication;"
```

Resultado observado:

```text
192.168.56.11 | streaming | async
```

## Prueba funcional

En `pg-primary`:

```bash
sudo -u postgres createdb laboratorio
sudo -u postgres psql -d laboratorio -c "CREATE TABLE prueba (id SERIAL PRIMARY KEY, mensaje TEXT, creado_en TIMESTAMP DEFAULT CURRENT_TIMESTAMP);"
sudo -u postgres psql -d laboratorio -c "INSERT INTO prueba (mensaje) VALUES ('Replicación funcionando');"
```

En `pg-standby`:

```bash
sudo -u postgres psql -d laboratorio -c "SELECT * FROM prueba;"
```

La fila `Replicación funcionando` apareció correctamente. Una prueba de `INSERT` en la réplica fue rechazada por tratarse de un servidor de solo lectura, que era el comportamiento esperado.

## Evidencias sugeridas

- `evidencias/08-estado-streaming.png`
- `evidencias/09-dato-replicado.png`
- `evidencias/10-standby-solo-lectura.png`
