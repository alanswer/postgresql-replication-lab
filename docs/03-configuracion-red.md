# Configuración de red

## Diseño

Cada VM utiliza dos interfaces:

| Interfaz | Modo de VirtualBox | Uso |
|---|---|---|
| `enp0s3` | NAT | Acceso a Internet mediante DHCP |
| `enp0s8` | Solo-anfitrión | Comunicación privada con IP estática |

## Direcciones asignadas

| Servidor | Dirección en `enp0s8` |
|---|---:|
| `pg-primary` | `192.168.56.10/24` |
| `pg-standby` | `192.168.56.11/24` |

## Netplan en `pg-primary`

Archivo `/etc/netplan/00-installer-config.yaml`:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.10/24
```

## Netplan en `pg-standby`

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.11/24
```

La configuración se protegió y aplicó con:

```bash
sudo chmod 600 /etc/netplan/00-installer-config.yaml
sudo netplan try
```

No se configuró una puerta de enlace en `enp0s8`, porque la ruta a Internet corresponde a la interfaz NAT.

## Verificación

```bash
ip -br addr
ping -c 4 192.168.56.11  # desde pg-primary
ping -c 4 192.168.56.10  # desde pg-standby
```

Las pruebas obtuvieron respuesta entre ambos servidores sin pérdida de paquetes.

## Evidencias sugeridas

- `evidencias/03-red-primary.png`
- `evidencias/04-red-standby.png`
- `evidencias/05-ping-entre-servidores.png`
