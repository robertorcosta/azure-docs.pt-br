---
title: Como associar o Cache Redis do Azure ao aplicativo Azure Spring Cloud | Microsoft Docs
description: Como associar o Cache Redis do Azure ao aplicativo Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607565"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Tutorial: Associar serviços do Azure ao aplicativo Azure Spring Cloud: Cache Redis do Azure

O Azure Spring Cloud permite que você associe serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente seu aplicativo Spring Boot. Este artigo demonstra como associar o aplicativo ao Cache Redis do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Uma instância de serviço do Cache Redis do Azure
* Extensão do Azure Spring Cloud para o CLI do Azure

Caso não tenha uma instância do Azure Spring Cloud implantada, siga as etapas neste [início rápido](spring-cloud-quickstart-launch-app-portal.md) para implantar seu primeiro aplicativo do Spring Cloud.

## <a name="bind-azure-cache-for-redis"></a>Associar o Cache Redis do Azure

1. Adicione a dependência a seguir no `pom.xml` do projeto.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Remova as propriedades `spring.redis.*`, se houver, no arquivo `application.properties`.

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma implantação usando `az spring-cloud app deployment create`.

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure. Localize o **Painel do Aplicativo** e selecione o aplicativo a ser associado ao Cache Redis do Azure.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Em seguida, escolha `Service binding` e selecione o botão `Create service binding`. Preencha o formulário, não se esqueça de selecionar o **Tipo de associação** `Azure Cache for Redis`, seu servidor Redis e a opção de chave primária. 

1. Reinicie o aplicativo e essa associação agora deve funcionar.

1. Para que a associação de serviço esteja correta, selecione o nome da associação e verifique os detalhes dela. O campo `property` deve ter esta aparência:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a associar seu aplicativos do Azure Spring Cloud a um Cache Redis do Azure.  Para saber mais sobre como associar serviços ao aplicativo, continue para o tutorial para associar um aplicativo a um BD MySQL.

> [!div class="nextstepaction"]
> [Saiba como associar um serviço MySQL do Azure ao serviço Azure Spring Cloud](spring-cloud-tutorial-bind-mysql.md).