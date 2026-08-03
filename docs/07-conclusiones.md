# Conclusiones

## Resultado general

El laboratorio cumplió su objetivo. Se configuró replicación física asíncrona entre dos servidores PostgreSQL 16, se verificó la sincronización, se ejecutó un failover manual y se recuperó la protección mediante la reconstrucción del servidor anterior como nueva réplica.

## Aprendizajes principales

- Los registros WAL permiten mantener una réplica física actualizada.
- `pg_hba.conf` debe autorizar explícitamente al usuario y a la IP de replicación.
- `pg_basebackup -R` simplifica la creación de una réplica.
- Una réplica física permanece en modo de solo lectura hasta su promoción.
- Después de promover una réplica, el antiguo principal no debe reiniciarse sin reconstrucción o reintegración controlada, porque existe riesgo de *split-brain*.
- El failover recupera la capacidad de escritura, pero los clientes también necesitarían cambiar su destino al nuevo principal.

## Limitaciones

- El failover es manual.
- No existe una IP virtual o proxy que redirija automáticamente a los clientes.
- La replicación es asíncrona y puede existir pérdida de transacciones recientes si el principal falla antes de transmitirlas.
- Solo se utilizan dos nodos, sin consenso ni testigo.
- La solución no automatiza el *fencing* del nodo anterior.

## Mejoras futuras

- Incorporar Patroni y etcd o Consul para automatizar la elección del principal.
- Añadir HAProxy o una IP virtual para ofrecer un punto de conexión estable.
- Definir objetivos de recuperación RTO y RPO y repetir las pruebas varias veces.
- Configurar monitoreo, alertas y archivado de WAL.
- Probar `pg_rewind` como alternativa de reintegración cuando se cumplan sus requisitos.
- Automatizar las pruebas y respaldos.

## Estado final

El proyecto quedó operativo con `pg-standby` como principal y `pg-primary` como réplica en estado `streaming`.
