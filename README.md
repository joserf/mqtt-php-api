# MQTT - PHP | API

Uma biblioteca para facilitar, desenvolvida para trabalhar com `PHP7` e `PHP8`.

Instalação:

```shell
composer require joserf/mqtt-php-api
```

## Índice

* [Requisitos mínimos](#requisitos-mínimos)
* [Como usar](#como-usar)
  * [Publish](#publish)
  * [Subscribe](#subscribe)  
* [Exemplos de uso](#exemplos-de-uso)

## Requisitos mínimos

* `php` >= 7.4

## Como usar

Um exemplo de publicação muito básico usando QoS 0 requer apenas três etapas: conectar, publicar e desconectar

Alterar os campos conforme exemplo abaixo, alterando o IP e porta.

### Publish

```php
$server   = '192.168.*.*';
$port     = 1883;
$clientId = 'test-publisher';

$mqtt = new \PhpMqtt\Client\MqttClient($server, $port, $clientId);
$mqtt->connect();
$mqtt->publish('test/publisher', 'JRF API Teste!', 0);
$mqtt->disconnect();
```

Se você não quiser passar um `$clientId`, um aleatório será gerado para você. Isso basicamente forçará uma sessão limpa implicitamente.

### Subscribe

A assinatura é um pouco mais complexa do que a publicação, pois requer a execução de um loop de eventos que lê, analisa e manipula as mensagens do broker:

```php
$server   = '192.168.*.*';
$port     = 1883;
$clientId = 'test-subscriber';

$mqtt = new \PhpMqtt\Client\MqttClient($server, $port, $clientId);
$mqtt->connect();
$mqtt->subscribe('test/subscriber', function ($topic, $message, $retained, $matchedWildcards) {
    echo sprintf("Received message on topic [%s]: %s\n", $topic, $message);
}, 0);
$mqtt->loop(true);
$mqtt->disconnect();
```

### Exemplos de uso

> Arquivo `exemplo.php` completo.

> *Podemos efetuar o teste via web: `php -S localhost:8000/exemplo.php`

```php
<?php

require_once __DIR__ . '/vendor/autoload.php';

error_reporting(E_ALL);

use \PhpMqtt\Client\MqttClient;
use \PhpMqtt\Client\ConnectionSettings;

$server   = '192.168.*.*';
$port     = 1883;
$clientId = rand(5, 15);
$clean_session = true;
$mqtt_version = MqttClient::MQTT_3_1_1;

$connectionSettings = (new ConnectionSettings)
  ->setKeepAliveInterval(60)
  ->setLastWillMessage('client disconnect')
  ->setLastWillQualityOfService(1);

$mqtt = new MqttClient($server, $port, $clientId, $mqtt_version);

$mqtt->connect($connectionSettings, $clean_session);
printf("client connected\n");

$mqtt->subscribe('mqtt/test', function ($topic, $message) {
    printf("Received message on topic [%s]: %s\n", $topic, $message);
}, 0);

$mqtt->loop(true);

?>
```

