---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: a9c8d604e5564526936f37edcc9eec5891443a47
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779921"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK (Java Development Kit)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso e uma cadeia de conexão dos Serviços de Comunicação implantados. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Abra o terminal ou a janela de comando e navegue até o diretório em que você deseja criar seu aplicativo Java. Execute o comando abaixo para gerar o projeto Java no modelo maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você observará que a tarefa 'gerar' criou um diretório com o mesmo nome que o `artifactId`. Nesse diretório, o diretório src/main/java contém o código-fonte do projeto, o `src/test/java directory` contém a fonte de teste e o arquivo `pom.xml` é o Modelo de Objeto do Projeto, ou POM.

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Navegue até o diretório */src/main/java/com/communication/quickstart*
1. Abra o arquivo *App.java* em seu editor
1. Substitua a instrução `System.out.println("Hello world!");`
1. Adicione diretivas `import`

Use o seguinte código para começar:

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância de um `CommunicationIdentityClient` com a chave de acesso e o ponto de extremidade do recurso. Saiba como [gerenciar a cadeia de conexão do recurso](../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `main`:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

Você pode inicializar o cliente com qualquer cliente HTTP personalizado que implemente a interface `com.azure.core.http.HttpClient`. O código acima demonstra o uso do [cliente HTTP do Azure Core Netty](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) fornecido por `azure-core`.

## <a name="create-a-user"></a>Criar um usuário

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Você deve manter um mapeamento entre os usuários do aplicativo e as entidades geradas dos Serviços de Comunicação (por exemplo, armazenando-os em seu banco de dados do servidor de aplicativos).

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Emitir tokens de acesso do usuário

Use o método `issueToken` para emitir um token de acesso para um usuário dos Serviços de Comunicação. Se você não fornecer o parâmetro `user` opcional, um usuário será criado e retornado com o token.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

Os tokens de acesso do usuário são credenciais de curta duração que precisam ser reemitidas para impedir que os usuários tenham interrupções de serviço. A propriedade de resposta `expiresAt` indica o tempo de vida do token.

## <a name="revoke-user-access-tokens"></a>Revogar tokens de acesso do usuário

Em alguns casos, talvez seja necessário revogar explicitamente os tokens de acesso do usuário, por exemplo, quando um usuário altera a senha que eles usam para se autenticar no serviço. Isso usa o método `revokeTokens` para invalidar todos os tokens de acesso de um usuário.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Excluir um usuário

A exclusão de um usuário revoga todos os tokens ativos e impede que você emita tokens subsequentes para as identidades. Ele também remove todo o conteúdo persistente associado ao usuário.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Executar o código

Navegue até o diretório que contém o arquivo *pom.xml* e compile o projeto usando o comando `mvn` a seguir.

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
