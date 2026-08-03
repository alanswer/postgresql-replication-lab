# Failover manual y recuperación

## Escenario

Se simuló la caída del servidor principal deteniendo PostgreSQL en `pg-primary`:

```bash
date --iso-8601=seconds
sudo systemctl stop postgresql
```

El servidor permaneció detenido para evitar que dos nodos aceptaran escrituras simultáneamente.

## Promoción de la réplica

En `pg-standby`:

```bash
time sudo -u postgres psql -c "SELECT pg_promote(wait => true);"
sudo -u postgres psql -c "SELECT pg_is_in_recovery();"
```

`pg_is_in_recovery()` devolvió `f`, confirmando que `pg-standby` dejó el modo de recuperación y pasó a ser el nuevo principal.

real    0m0.390s
user    0m0.005s
sys     0m0.011s

## Validación de escritura

En el nuevo principal `pg-standby`:

```bash
sudo -u postgres psql -d laboratorio -c "INSERT INTO prueba (mensaje) VALUES ('Failover exitoso');"
sudo -u postgres psql -d laboratorio -c "SELECT * FROM prueba ORDER BY id;"
```

La aparición de `Failover exitoso` confirmó que el servicio volvió a aceptar escrituras.

## Reconstrucción del servidor anterior

En el nuevo principal se autorizó a `pg-primary` en `pg_hba.conf`:

```text
host    replication    replicator    192.168.56.10/32    scram-sha-256
```

La configuración se recargó con:

```bash
sudo -u postgres psql -c "SELECT pg_reload_conf();"
```

Con PostgreSQL todavía detenido en el antiguo `pg-primary`, su directorio se conservó como respaldo y se creó una nueva copia desde `pg-standby`:

```bash
sudo mv /var/lib/postgresql/16/main /var/lib/postgresql/16/main.antes-failover
sudo install -d -o postgres -g postgres -m 700 /var/lib/postgresql/16/main
sudo -u postgres pg_basebackup -h 192.168.56.11 -D /var/lib/postgresql/16/main -U replicator -P -R -X stream -W
sudo pg_ctlcluster 16 main start
```

## Verificación final

En `pg-primary`:

```bash
sudo -u postgres psql -c "SELECT pg_is_in_recovery();"
```

Resultado: `t`.

En `pg-standby`:

```bash
sudo -u postgres psql -c "SELECT client_addr, state, sync_state FROM pg_stat_replication;"
```

Resultado observado:

```text
192.168.56.10 | streaming | async
```

## Topología final

| Servidor | Función final |
|---|---|
| `pg-standby` (`192.168.56.11`) | Principal |
| `pg-primary` (`192.168.56.10`) | Réplica |

## Evidencias sugeridas

- `evidencias/11-promocion-standby.png`
- `evidencias/12-failover-exitoso.png`
- `evidencias/13-replicacion-inversa.png`
