# Evidencias del laboratorio

Coloca aquí las capturas y utiliza estos nombres para que la documentación sea fácil de revisar:

| Archivo sugerido | Contenido |
|---|---|
| `01-pg-primary-instalado.png` | Inicio de sesión o `hostname` de `pg-primary` |
| `02-pg-standby-instalado.png` | Inicio de sesión o `hostname` de `pg-standby` |
| `03-red-primary.png` | `ip -br addr` en `pg-primary` |
| `04-red-standby.png` | `ip -br addr` en `pg-standby` |
| `05-ping-entre-servidores.png` | Ping exitoso y sin pérdida |
| `06-postgresql-primary.png` | Versión y clúster de PostgreSQL en `pg-primary` |
| `07-postgresql-standby.png` | Versión y clúster de PostgreSQL en `pg-standby` |
| `08-estado-streaming.png` | Vista de `pg_stat_replication` |
| `09-dato-replicado.png` | Fila `Replicación funcionando` en la réplica |
| `10-standby-solo-lectura.png` | Escritura rechazada en la réplica |
| `11-promocion-standby.png` | Tiempo de promoción y `pg_is_in_recovery() = f` |
| `12-failover-exitoso.png` | Fila `Failover exitoso` |
| `13-replicacion-inversa.png` | `192.168.56.10`, `streaming`, `async` |

No incluyas contraseñas, claves, direcciones públicas ni otra información sensible en las capturas.
