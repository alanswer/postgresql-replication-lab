# Laboratorio de replicación y failover con PostgreSQL 16

## Descripción

Este proyecto implementa un laboratorio de alta disponibilidad con PostgreSQL 16 sobre dos máquinas virtuales Ubuntu Server 26.04 LTS. Se configuró replicación física en *streaming*, se comprobó la sincronización de datos, se ejecutó un failover manual y se reconstruyó el servidor anterior como réplica.

## Objetivos

- Configurar dos servidores PostgreSQL comunicados por una red privada.
- Implementar replicación física asíncrona mediante registros WAL.
- Verificar que la réplica reciba los cambios y rechace escrituras.
- Simular la caída del servidor principal.
- Promover manualmente la réplica y comprobar que acepte escrituras.
- Restablecer la replicación después del failover.

## Tecnologías

- Oracle VirtualBox
- Ubuntu Server 26.04 LTS
- PostgreSQL 16
- Netplan
- OpenSSH Server
- Git y GitHub

## Topología inicial

| Servidor | Dirección IP | Función inicial |
|---|---:|---|
| `pg-primary` | `192.168.56.10` | Principal |
| `pg-standby` | `192.168.56.11` | Réplica |

## Topología final

Después del failover y la reconstrucción:

| Servidor | Dirección IP | Función final |
|---|---:|---|
| `pg-standby` | `192.168.56.11` | Nuevo principal |
| `pg-primary` | `192.168.56.10` | Nueva réplica |

## Resultados

- La conexión de replicación alcanzó el estado `streaming`.
- Los datos creados en el principal aparecieron en la réplica.
- La réplica rechazó escrituras mientras estaba en recuperación.
- `pg-standby` fue promovido correctamente y aceptó una nueva escritura.
- `pg-primary` fue reconstruido desde el nuevo principal.
- La replicación quedó restablecida en sentido inverso.

## Documentación

1. [Arquitectura](docs/01-arquitectura.md)
2. [Creación de las máquinas virtuales](docs/02-creacion-vms.md)
3. [Configuración de red](docs/03-configuracion-red.md)
4. [Instalación de PostgreSQL](docs/04-instalacion-postgresql.md)
5. [Configuración y prueba de la replicación](docs/05-replicacion.md)
6. [Failover y recuperación](docs/06-failover-recuperacion.md)
7. [Conclusiones](docs/07-conclusiones.md)

## Referencias

- [PostgreSQL 16: servidores de respaldo](https://www.postgresql.org/docs/16/warm-standby.html)
- [PostgreSQL 16: pg_basebackup](https://www.postgresql.org/docs/16/app-pgbasebackup.html)
- [Repositorio APT oficial de PostgreSQL](https://www.postgresql.org/download/linux/ubuntu/)
- [Netplan: direcciones IP estáticas](https://netplan.readthedocs.io/en/stable/using-static-ip-addresses/)
- [VirtualBox: redes virtuales](https://www.virtualbox.org/manual/topics/networkingdetails.html)
