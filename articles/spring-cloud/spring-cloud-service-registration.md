---
title: Automatize o registro e a descoberta de serviços
description: Saiba como automatizar a descoberta e o registro de serviços usando o Registro de Serviços de Nuvem da Primavera
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278852"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Descubra e registre seus serviços de Nuvem de Primavera

O Service Discovery é um requisito fundamental para uma arquitetura baseada em microserviços.  Configurar cada cliente manualmente leva tempo e introduz a possibilidade de erro humano.  O Registro de Serviços de Nuvem da Primavera do Azure resolve esse problema.  Uma vez configurado, um servidor do Registro de Serviços controlará o registro e a descoberta do serviço para os microserviços do seu aplicativo. O servidor do Registro de Serviços mantém um registro dos microserviços implantados, permite o balanceamento de carga do lado do cliente e desacopla provedores de serviços de clientes sem depender de DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registre seu aplicativo usando o Registro de Serviços de Nuvem da Primavera

Antes que seu aplicativo possa gerenciar o registro e a descoberta do serviço usando o Registro de Serviços de Nuvem da Primavera, várias dependências devem ser incluídas no arquivo *pom.xml* do aplicativo.
Inclua dependências para *spring-cloud-starter-netflix-eureka-client* e *spring-cloud-starter-azure-spring-cloud-client* para o seu *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Atualize a classe de nível superior

Finalmente, adicionamos uma anotação à classe de nível superior da sua aplicação

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

O ponto final do servidor do Spring Cloud Service Registry será injetado como uma variável de ambiente em seu aplicativo.  Os microserviços agora poderão se cadastrar no servidor do Registro de Serviços e descobrir outros microserviços dependentes.

Observe que pode levar alguns minutos para que as alterações se propagarem do servidor para todos os microsserviços.
