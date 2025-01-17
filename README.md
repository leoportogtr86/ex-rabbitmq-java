# Guia de Operações Básicas com RabbitMQ e Java

RabbitMQ é um software de mensagem que implementa o protocolo Advanced Message Queuing Protocol (AMQP). Ele é utilizado
para envio de mensagens entre diferentes sistemas de forma assíncrona. Este guia irá demonstrar como realizar operações
básicas com RabbitMQ utilizando a linguagem Java.

## Pré-requisitos

Antes de começar, você precisará de:

1. **Java Development Kit (JDK)**: Certifique-se de ter o JDK instalado na sua máquina.
2. **RabbitMQ**: Instale e configure o RabbitMQ na sua máquina. Você pode seguir as instruções oficiais
   no [site do RabbitMQ](https://www.rabbitmq.com/download.html).
3. **Biblioteca Java para RabbitMQ**: Adicione a biblioteca `amqp-client` ao seu projeto. Se estiver usando Maven,
   adicione a seguinte dependência ao seu `pom.xml`:

```xml

<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.13.0</version>
</dependency>
```

## Passo 1: Configuração do RabbitMQ

Primeiramente, certifique-se de que o RabbitMQ está em execução. Você pode verificar isso acessando o console de
gerenciamento do RabbitMQ em `http://localhost:15672`. O login padrão é `guest` e a senha também é `guest`.

## Passo 2: Enviando uma Mensagem

Vamos começar criando uma classe Java que envia uma mensagem para uma fila RabbitMQ.

### Código Java para Enviar Mensagens

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

public class Send {

    private final static String QUEUE_NAME = "hello";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        try (Connection connection = factory.newConnection();
             Channel channel = connection.createChannel()) {
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            String message = "Hello World!";
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
            System.out.println(" [x] Sent '" + message + "'");
        }
    }
}
```

### Explicação do Código

1. **ConnectionFactory**: Configura a fábrica de conexões para conectar ao servidor RabbitMQ.
2. **Connection**: Estabelece uma conexão com o servidor RabbitMQ.
3. **Channel**: Cria um canal através do qual as mensagens serão enviadas.
4. **queueDeclare**: Declara a fila `hello`. Se a fila não existir, ela será criada.
5. **basicPublish**: Envia a mensagem "Hello World!" para a fila `hello`.

## Passo 3: Recebendo uma Mensagem

Agora, vamos criar uma classe Java que recebe a mensagem da fila RabbitMQ.

### Código Java para Receber Mensagens

```java
import com.rabbitmq.client.*;

public class Recv {

    private final static String QUEUE_NAME = "hello";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        try (Connection connection = factory.newConnection();
             Channel channel = connection.createChannel()) {
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

            DeliverCallback deliverCallback = (consumerTag, delivery) -> {
                String message = new String(delivery.getBody(), "UTF-8");
                System.out.println(" [x] Received '" + message + "'");
            };
            channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> {
            });
        }
    }
}
```

### Explicação do Código

1. **ConnectionFactory**: Configura a fábrica de conexões para conectar ao servidor RabbitMQ.
2. **Connection**: Estabelece uma conexão com o servidor RabbitMQ.
3. **Channel**: Cria um canal através do qual as mensagens serão recebidas.
4. **queueDeclare**: Declara a fila `hello`. Se a fila não existir, ela será criada.
5. **basicConsume**: Consome mensagens da fila `hello` e imprime-as no console.

## Conclusão

Neste guia, demonstramos como configurar o RabbitMQ, enviar e receber mensagens usando Java. Estas operações básicas são
essenciais para começar a utilizar o RabbitMQ em seus projetos. Para operações mais avançadas, consulte
a [documentação oficial do RabbitMQ](https://www.rabbitmq.com/documentation.html).