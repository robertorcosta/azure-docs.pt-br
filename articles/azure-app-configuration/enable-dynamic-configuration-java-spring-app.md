---
title: Usar a configuração dinâmica em um aplicativo Spring Boot
titleSuffix: Azure App Configuration
description: Saiba como atualizar dados de configuração dinamicamente em aplicativos Spring Boot
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121554"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Usar a configuração dinâmica em um aplicativo Java Spring

A biblioteca de clientes do Spring Boot da Configuração de Aplicativos dá suporte à atualização de um conjunto de definições de configuração sob demanda, sem fazer com que o aplicativo seja reiniciado. A biblioteca de clientes armazena em cache cada configuração para evitar um excesso de chamadas ao repositório de configurações. A operação de atualização não atualiza o valor até que o valor armazenado em cache expire, mesmo quando o valor foi alterado no repositório de configurações. O tempo de expiração padrão de cada solicitação é de 30 segundos. Ele pode ser substituído, se necessário.

Verifique se há configurações atualizadas sob demanda chamando o método `refreshConfigurations()` da `AppConfigurationRefresh`.

Como alternativa, você pode usar o pacote `spring-cloud-azure-appconfiguration-config-web`, que usa uma dependência de `spring-web` para lidar com a atualização automatizada.

## <a name="use-automated-refresh"></a>Usar a atualização automatizada

Para usar a atualização automatizada, comece com um aplicativo Spring Boot que usa a Configuração de Aplicativos, como o aplicativo criado com o [Início rápido do Spring Boot para a Configuração de Aplicativos](quickstart-java-spring-app.md).

Em seguida, abra o arquivo *pom.xml* em um editor de texto e adicione uma `<dependency>` a `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Executar e testar o aplicativo localmente

1. Compile o aplicativo Spring Boot com Maven e execute-o.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Abra uma nova janela do navegador e acesse a URL: `http://localhost:8080`.  Você verá a mensagem associada à sua chave. 

    Você também pode usar a *ondulação* para testar o aplicativo, por exemplo: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Para testar a configuração dinâmica, abra o portal de Configuração de Aplicativos do Azure associado ao aplicativo. Selecione **Gerenciador de Configurações** e atualize o valor da chave exibida, por exemplo:
    | Chave | Valor |
    |---|---|
    | application/config.message | Hello - Updated |

1. Atualize a página do navegador para ver a nova mensagem exibida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou seu aplicativo Spring Boot para atualizar dinamicamente as definições de configuração na Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
