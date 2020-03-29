---
title: Conecte-se aos Azure Media Services v3 API - Java
description: Este artigo descreve como se conectar à Azure Media Services v3 API com Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888488"
---
# <a name="connect-to-media-services-v3-api---java"></a>Conecte-se aos Serviços de Mídia v3 API - Java

Este artigo mostra como se conectar ao Azure Media Services v3 Java SDK usando o método de login principal do serviço.

Neste artigo, o Visual Studio Code é usado para desenvolver o aplicativo de amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Siga [o Writing Java com o Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) para instalar:

   - JDK
   - Apache Maven
   - Pacote de extensão Java
- Certifique-se `JAVA_HOME` de `PATH` definir e ambiente de variáveis.
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Mídia.
- Siga as etapas no tópico APIs de [acesso.](access-api-cli-how-to.md) Registre o ID de assinatura, o ID do aplicativo (ID do cliente), a chave de autenticação (secreta) e o ID do inquilino que você precisa em uma etapa posterior.

Leia também:

- [Java em Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gerenciamento de projetos Java em código VS](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Revisar [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Abra uma ferramenta de `cd` linha de comando e para um diretório onde você deseja criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando você executa `pom.xml`o `App.java`comando, os arquivos são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. No Visual Studio Code, abra a pasta onde seu projeto está
1. Encontrar e abrir o`pom.xml`
1. Adicione as dependências necessárias

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Conecte-se ao cliente Java

1. Abra `App.java` o `src\main\java\com\azure\ams` arquivo em baixo e certifique-se de que seu pacote está incluído na parte superior:

    ```java
    package com.azure.ams;
    ```
1. Abaixo da declaração de pacote, adicione estas instruções de importação:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Para criar as credenciais do Active Directory que você precisa fazer solicitações, adicione o código seguinte ao método principal da classe App e defina os valores que você obteve das [APIs](access-api-cli-how-to.md)de acesso :
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Execute o aplicativo.

## <a name="see-also"></a>Confira também

- [Conceitos dos Serviços de Mídia](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Referência java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Próximas etapas

Agora você `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` pode incluir e começar a manipular entidades.

Para obter mais exemplos de código, consulte o repo [de amostras Java SDK.](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)
