<div align="center">
  <p>
    <h3>RabbitMQ in GitHub Actions</h3>
  </p>
  <p>Start a RabbitMQ message broker in your GitHub Actions.</p>
</div>

---

## Introduction

This GitHub Action starts a RabbitMQ message broker as Docker container.
The published ports, TLS certificates and the RabbitMQ configuration can be adjusted as needed.

This is useful when running tests against an MQTT broker.

## Usage

```yaml
name: Run tests

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Git checkout
        uses: actions/checkout@v2
  
      - name: Start RabbitMQ
        uses: namoshek/rabbitmq-github-action@v1
        with:
          version: '3.8.9'
          ports: '1883:1883 8883:8883'
          certificates: ${{ github.workspace }}/.ci/tls-certificates
          config: ${{ github.workspace }}/.ci/rabbitmq.conf
          definitions: ${{ github.workspace }}/.ci/definitions.json
          container-name: 'mqtt'
  
      - run: test
```

Currently, the following parameters are supported:

| Parameter | Default  | Description |
|-----------|----------|-------------|
| `version` | `latest` | An image tag of the [rabbitmq](`https://hub.docker.com/_/rabbitmq`) Docker image |
| `ports`   | `1883:1883`   | Port mappings in a [host]:[container] format, delimited by spaces (example: "1883:1883 8883:8883") |
| `certificates` | -   | Absolute path to a directory containing certificate files which can be referenced in the config (the folder is mounted under `/rabbitmq-certs` in the container) |
| `config`  | -        | Absolute path to a custom `rabbitmq.conf` configuration file to use |
| `definitions` | -    | Absolute path to a custom `definitions.json` definition file to use (requires using a `x.y.z-management` image version or enabling the `rabbitmq_management` plugin) |
| `plugins` | - | A comma separated list of plugins to enable (overrides the image default) |
| `container-name` | `rabbitmq` | The name of the spawned Docker container (can be used as hostname when accessed from other containers) |

All parameters are optional. If no `certificates` directory path is given, no volume is mounted. If no `config` is given, the default RabbitMQ config is used. If no `definitions` file path is given, no definitions are loaded on startup.

## License

This action is published under the [MIT license](LICENSE).
