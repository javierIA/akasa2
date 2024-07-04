Claro, aquí tienes un script de Bash para correr el contenedor Docker con la configuración que hemos discutido. Este script también verifica si el directorio y los archivos necesarios existen antes de ejecutar el contenedor.

1. **Crea el script**: Guarda el siguiente contenido en un archivo llamado `run_akasa_docker.sh` en tu directorio `$HOME`.

```sh
#!/bin/bash

# Directorio de configuración
CONFIG_DIR="$HOME/akasa/config"

# Archivo de configuración
CONFIG_FILE="$CONFIG_DIR/akasa-config.yaml"

# Verificar si el directorio de configuración existe
if [ ! -d "$CONFIG_DIR" ]; then
  echo "Creando directorio de configuración en $CONFIG_DIR"
  mkdir -p "$CONFIG_DIR"
fi

# Crear un archivo de configuración mínimo si no existe
if [ ! -f "$CONFIG_FILE" ]; then
  echo "Creando archivo de configuración mínimo en $CONFIG_FILE"
  cat <<EOL > "$CONFIG_FILE"
# Network Listeners
listeners:
  mqtt:
    addr: 127.0.0.1:1883
    reuse_port: false

# Authentication
auth:
  enable: true
  anonymous:
    allow: true

# Scram Users (required field)
scram_users: {}

# MQTT Settings
check_v310_client_id_length: false
max_allowed_qos: 2
inflight_timeout: 60
max_inflight_client: 10
max_inflight_server: 10
max_in_mem_pending_messages: 256
min_keep_alive: 10
max_keep_alive: 65535
retain_available: true
shared_subscription_available: true
subscription_id_available: true
wildcard_subscription_available: true

# Hooks
hook:
  enable_before_connect: true
  enable_after_connect: true
  enable_publish: true
  enable_subscribe: true
  enable_unsubscribe: true
EOL
fi

# Ejecutar el contenedor Docker
sudo docker run --init -it --rm -p 1883:1883 -v "$CONFIG_DIR":/opt thewawar/akasa:0.1.1 akasa start --config /opt/akasa-config.yaml
```

2. **Dar permisos de ejecución al script**:

```sh
chmod +x $HOME/run_akasa_docker.sh
```

3. **Ejecutar el script**:

```sh
$HOME/run_akasa_docker.sh
```

Este script hace lo siguiente:

1. Verifica si el directorio de configuración `$HOME/akasa/config` existe; si no, lo crea.
2. Verifica si el archivo de configuración `akasa-config.yaml` existe; si no, lo crea con una configuración mínima.
3. Ejecuta el contenedor Docker con la configuración especificada.

Al ejecutar este script, el contenedor Docker se iniciará con la configuración mínima necesaria y el cliente MQTT podrá conectarse al servidor.