# Creación de las máquinas virtuales

## Configuración utilizada

Se crearon dos máquinas independientes en VirtualBox:

| Parámetro | `pg-primary` | `pg-standby` |
|---|---|---|
| Sistema operativo | Ubuntu Server 26.04 LTS | Ubuntu Server 26.04 LTS |
| Memoria RAM | 2048 MB | 2048 MB |
| Procesadores | 2 | 2 |
| Disco virtual | 25 GB dinámico | 25 GB dinámico |
| EFI | Deshabilitado | Deshabilitado |
| OpenSSH Server | Instalado | Instalado |

La instalación desatendida de VirtualBox se dejó desmarcada para definir manualmente el nombre de cada servidor y seleccionar OpenSSH.

## Instalación del sistema

Durante la instalación se configuraron los nombres:

- `pg-primary`
- `pg-standby`

La identidad de cada VM se comprobó con:

```bash
hostname
```

## Resultado

Ambas máquinas iniciaron correctamente y permitieron iniciar sesión con el usuario administrador creado durante la instalación.

## Evidencias sugeridas

- `evidencias/01-pg-primary-instalado.png`
- `evidencias/02-pg-standby-instalado.png`
