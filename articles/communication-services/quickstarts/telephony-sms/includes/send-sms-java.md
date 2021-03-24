---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 2739079b67d80f3e4a9f367aaa58f6dcbbb650ca
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622220"
---
Comece a usar os Serviços de Comunicação do Azure usando a biblioteca de clientes de SMS do Java dos Serviços de Comunicação para enviar mensagens SMS.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK (Java Development Kit)](/java/azure/jdk/) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um número de telefone habilitado para SMS. [Obter um número de telefone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em um terminal ou janela de comando, execute `mvn -v` para verificar se o maven está instalado.
- Para exibir os números de telefone associados ao seu recurso dos Serviços de Comunicação, entre no [portal do Azure](https://portal.azure.com/), localize o recurso em questão e abra a guia **números de telefone** no painel de navegação esquerdo.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Abra o terminal ou a janela de comando e navegue até o diretório no qual você deseja criar o seu aplicativo Java. Execute o comando abaixo para gerar o projeto Java no modelo maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

A meta 'generate' criou um diretório com o mesmo nome que artifactId. Nesse diretório, o diretório **src/main/java** contém o código-fonte do projeto, o **src/test/java directory** contém a fonte de teste e o arquivo **pom.xml** é o Modelo de Objeto do Projeto (ou POM) do projeto.

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Adicione a dependência `azure-core-http-netty` ao seu arquivo **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Abra **/src/main/java/com/communication/quickstart/App.java** em um editor de texto, adicione diretivas de importação e remova a instrução `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes de SMS dos Serviços de Comunicação do Azure para Java.

| Name                                                             | Descrição                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Essa classe cria o SmsClient. Ela é fornecida com o ponto de extremidade, a credencial e um cliente http. |
| SmsClient                    | Essa classe é necessária para toda a funcionalidade de SMS. Ela é usada para enviar mensagens SMS.                |
| SmsSendResult                | Essa classe contém o resultado do serviço de SMS.                                          |
| SmsSendOptions               | Essa classe fornece opções para adicionar marcas personalizadas e configurar o relatório de entrega. Se deliveryReportEnabled for definido como verdadeiro, um evento será emitido quando a entrega for bem-sucedida|                           |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância de um `SmsClient` com a sua cadeia de conexão. (A credencial é a `Key` do portal do Azure). Saiba como [gerenciar a cadeia de conexão do recurso](../../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `main`:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

Você pode inicializar o cliente com qualquer cliente HTTP personalizado que implemente a interface `com.azure.core.http.HttpClient`. O código acima demonstra o uso do [cliente HTTP do Azure Core Netty](/java/api/overview/azure/core-http-netty-readme) fornecido por `azure-core`.

Você também pode fornecer toda a cadeia de conexão usando a função connectionString() em vez de fornecer o ponto de extremidade e a chave de acesso. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Enviar uma mensagem SMS 1:1

Para enviar uma mensagem SMS para um só destinatário, chame o método `send` no SmsClient com o número de telefone de um destinatário. Você também pode transmitir parâmetros opcionais para especificar se o relatório de entrega deve ser habilitado e para definir marcas personalizadas.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```
## <a name="send-a-1n-sms-message-with-options"></a>Enviar uma mensagem SMS 1:N com opções
Para enviar uma mensagem SMS para uma lista de destinatários, chame o método `send` com uma lista de números de telefone de destinatários. Você também pode transmitir parâmetros opcionais para especificar se o relatório de entrega deve ser habilitado e para definir marcas personalizadas.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Substitua `<from-phone-number>` pelo número de telefone habilitado para SMS associado ao seu recurso dos Serviços de Comunicação e `<to-phone-number>` por uma lista de números de telefone ou pelo número de telefone ao qual deseja enviar uma mensagem.

## <a name="optional-parameters"></a>Parâmetros Opcionais

O parâmetro `deliveryReportEnabled` é um parâmetro opcional que você pode usar para configurar o Relatório de Entrega. Isso é útil para cenários em que você deseja emitir eventos quando mensagens SMS são entregues. Confira o guia de início rápido [Manipular eventos de SMS](../handle-sms-events.md) para configurar o Relatório de Entrega das suas mensagens SMS.

O parâmetro `tag` é um parâmetro opcional que pode ser usado para aplicar uma marca ao relatório de entrega.

## <a name="run-the-code"></a>Executar o código

Navegue até o diretório que contém o arquivo **pom.xml** e compile o projeto usando o comando `mvn`.

```console

mvn compile

```

Em seguida, compile o pacote.

```console

mvn package

```

Execute o comando `mvn` a seguir para executar o aplicativo.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```