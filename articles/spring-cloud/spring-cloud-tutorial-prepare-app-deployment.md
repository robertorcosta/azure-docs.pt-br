---
title: Preparar um aplicativo Spring para implantação no Azure Spring Cloud | Microsoft Docs
description: Neste início rápido, você prepara um aplicativo Spring Java para implantação.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 98d9f3f656cff84cec8d223ed535255157155bd2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038325"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Tutorial: Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud

Este início rápido mostra como preparar um aplicativo Spring Cloud Java existente para implantação no Azure Spring Cloud.  Configurado corretamente, o Azure Spring Cloud fornece serviços robustos para monitorar, dimensionar e atualizar seu aplicativo Spring Cloud. 

## <a name="java-runtime-version"></a>Versão do Java Runtime

Somente aplicativos Spring/Java podem ser executados no Azure Spring Cloud.

Java 8 e 11 são compatíveis. O ambiente de hospedagem contém o Azul Zulu OpenJDK mais recente para o Azure. Consulte [este artigo](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) para obter mais informações sobre o Azul Zulu OpenJDK para Azure. 

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring Boot e Spring Cloud

Somente os aplicativos Spring Boot são compatíveis com o Azure Spring Cloud. O Spring Boot 2.0 e o 2.1 são compatíveis. As combinações de Spring Boot e Spring Cloud compatíveis são listadas na tabela abaixo.

Versão do Spring Boot | Versão do Spring Cloud
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

Verifique se o arquivo `pom.xml` tem as dependências Spring Boot e Spring Cloud com base em sua versão.

### <a name="version-20"></a>Versão 2.0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Versão 2.1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dependência de cliente do Azure Spring Cloud

O Azure Spring Cloud hospeda e gerencia os componentes Spring Cloud para você, tais como o Registro de Serviço do Spring Cloud e o Servidor de Configuração do Spring Cloud. Inclua a biblioteca de clientes do Azure Spring Cloud em suas dependências para permitir a comunicação com sua instância do serviço do Azure Spring Cloud.

A tabela a seguir lista as versões corretas para seu aplicativo Spring Boot/Spring Cloud.

Versão do Spring Boot | Versão do Spring Cloud | Versão do Azure Spring Cloud
---|---|---
2.0.x | Finchley.RELEASE | 2.0.0-SNAPSHOT
2.1.x | Greenwich.RELEASE | 2.1.0-SNAPSHOT

Inclua este snippet em seu `pom.xml` com a versão correta do Azure Spring cloud na 'dependência':

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para habilitar os recursos internos do Azure Spring Cloud, seu aplicativo deve incluir as seguintes dependências. Isso garantirá que seu aplicativo seja configurado corretamente com cada componente.  

### <a name="service-registry"></a>Registro do Serviço

Para usar o serviço gerenciado do Registro de Serviço do Azure, inclua `spring-cloud-starter-netflix-eureka-client` em `POM.xml`, conforme mostrado abaixo.

O ponto de extremidade do servidor de Registro de Serviços será injetado automaticamente como variáveis de ambiente com o seu aplicativo. Em seguida, os aplicativos poderão se registrar com o servidor do Registro de Serviço e descobrir outros microsserviços dependentes.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Configuração Distribuída

Para habilitar a configuração distribuída, inclua `spring-cloud-config-client` na seção dependências do seu `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração de inicialização, pois ela impedirá que o aplicativo funcione com o Servidor de Configuração.

### <a name="metrics"></a>Métricas

Inclua `spring-boot-starter-actuator` na seção de dependências do pom.xml. As métricas serão retiradas periodicamente dos pontos de extremidade JMX e poderão ser visualizadas usando o portal do Azure.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Rastreamento distribuído

Inclua `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` na seção de dependências do pom.xml, conforme mostrado abaixo. Além disso, você precisa habilitar uma instância do Azure App Insights para trabalhar com sua instância de serviço do Azure Spring Cloud. Leia mais [aqui](spring-cloud-tutorial-distributed-tracing.md) sobre como habilitar o App Insights com o Azure Spring Cloud

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar o aplicativo Java Spring para implantação no Azure Spring Cloud.  Para saber como habilitar o servidor de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como configurar o Servidor de Configuração](spring-cloud-tutorial-config-server.md).

