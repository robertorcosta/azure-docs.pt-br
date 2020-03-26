---
title: Usar a configuração dinâmica em um aplicativo Spring Boot
titleSuffix: Azure App Configuration
description: Saiba como atualizar dados de configuração dinamicamente em aplicativos Spring Boot
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216753"
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
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Salve o arquivo e, em seguida, compile e execute o aplicativo como de costume.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou seu aplicativo Spring Boot para atualizar dinamicamente as definições de configuração na Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
