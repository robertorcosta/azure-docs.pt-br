---
title: Tutorial – Preparar um aplicativo Spring para implantação no Azure Spring Cloud
description: Neste tutorial, você prepara um aplicativo Spring Java para implantação.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 9918c7866b21cd2a9e021a355fb43977c91a89cf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277441"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud

Este início rápido mostra como preparar um aplicativo Java Spring Cloud existente para implantação no Azure Spring Cloud. Se configurado corretamente, o Azure Spring Cloud fornecerá serviços robustos para monitorar, dimensionar e atualizar seu aplicativo Java Spring Cloud.

## <a name="java-runtime-version"></a>Versão do Java Runtime

Somente aplicativos Spring/Java podem ser executados no Azure Spring Cloud.

O Azure Spring Cloud dá suporte ao Java 8 e ao Java 11. O ambiente de hospedagem contém a última versão do Azul Zulu OpenJDK para Azure. Para obter mais informações sobre o Azul Zulu OpenJDK para Azure, confira [Instalar o JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring Boot e Spring Cloud

O Azure Spring Cloud só dá suporte a aplicativos Spring Boot. Ele dá suporte ao Spring Boot versões 2.0 e 2.1. A seguinte tabela lista as combinações do Spring Boot e do Spring Cloud compatíveis:

Versão do Spring Boot | Versão do Spring Cloud
---|---
2,0 | Finchley.RELEASE
2.1 | Greenwich.RELEASE

Verifique se o arquivo pom.xml tem as dependências corretas do Spring Boot e do Spring Cloud de acordo com a versão do Spring Boot.

### <a name="dependencies-for-spring-boot-version-20"></a>Dependências do Spring Boot versão 2.0

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

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências do Spring Boot versão 2.1

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

O Azure Spring Cloud hospeda e gerencia os componentes do Spring Cloud para você. Esses componentes incluem o Registro de Serviço do Spring Cloud e o Servidor de Configuração do Spring Cloud. Inclua a biblioteca de clientes do Azure Spring Cloud em suas dependências para permitir a comunicação com sua instância de serviço do Azure Spring Cloud.

A tabela a seguir lista as versões corretas do Azure Spring Cloud para o aplicativo que usa o Spring Boot e o Spring Cloud.

Versão do Spring Boot | Versão do Spring Cloud | Versão do Azure Spring Cloud
---|---|---
2,0 | Finchley.RELEASE | 2,0
2.1 | Greenwich.RELEASE | 2.1

Inclua uma das dependências a seguir no arquivo pom.xml. Selecione a dependência cuja versão do Azure Spring Cloud corresponde à sua.

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Dependência do Azure Spring Cloud versão 2.0

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dependência do Azure Spring Cloud versão 2.1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para habilitar os recursos internos do Azure Spring Cloud, seu aplicativo deve incluir as seguintes dependências. Essa inclusão garante que o aplicativo seja configurado corretamente com cada componente.  

### <a name="service-registry-dependency"></a>Dependência do Registro de Serviço

Para usar o serviço gerenciado Registro de Serviço do Azure, inclua a dependência `spring-cloud-starter-netflix-eureka-client` no arquivo pom.xml, conforme mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto de extremidade do servidor do Registro de Serviço é injetado automaticamente como variáveis de ambiente com o aplicativo. Os aplicativos podem se registrar por conta própria no servidor do Registro de Serviço e descobrir outros microsserviços dependentes.

### <a name="distributed-configuration-dependency"></a>Dependência da Configuração Distribuída

Para habilitar a Configuração Distribuída, inclua a seguinte dependência `spring-cloud-config-client` na seção de dependências do arquivo pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração de inicialização. Caso contrário, o aplicativo deixará de funcionar com o Servidor de Configuração.

### <a name="metrics-dependency"></a>Dependência de Métricas

Inclua a dependência `spring-boot-starter-actuator` na seção de dependências do arquivo pom.xml, conforme mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são extraídas periodicamente dos pontos de extremidade JMX. Visualize as métricas usando o portal do Azure.

### <a name="distributed-tracing-dependency"></a>Dependência de Rastreamento Distribuído

Inclua as seguintes dependências `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` na seção de dependências do arquivo pom.xml:

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

 Você também precisa habilitar uma instância do Azure Application Insights para trabalhar com sua instância de serviço do Azure Spring Cloud. Leia o [tutorial sobre rastreamento distribuído](spring-cloud-tutorial-distributed-tracing.md) para saber como usar o Application Insights com o Azure Spring Cloud.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar o aplicativo Java Spring para implantação no Azure Spring Cloud. Para saber como configurar uma instância do Servidor de Configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como configurar uma instância do Servidor de Configuração](spring-cloud-tutorial-config-server.md)

Mais exemplos estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
