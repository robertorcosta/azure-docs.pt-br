---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9ce4b25d26c0294cf7618b5851c0956af7687ee7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837501"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com Gradle

Vamos começar criando um diretório de trabalho para esse projeto. Na linha de comando (ou terminal), execute esse comando:

```console
mkdir detect-sample
cd detect-sample
```

Em seguida, você vai inicializar um projeto do Gradle. Esse comando criará arquivos de compilação essenciais para o Gradle, principalmente o `build.gradle.kts`, que é usado para criar e configurar seu aplicativo no tempo de execução. Execute este comando no diretório de trabalho:

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

## <a name="configure-the-build-file"></a>Configure o arquivo de compilação

Localize `build.gradle.kts` e abra-o com seu editor de texto ou IDE favorito. Depois copie nessa configuração de build:

```
plugins {
    java
    application
}
application {
    mainClassName = "Detect"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Observe que esse exemplo tem dependências de OkHttp para solicitações HTTP e de Gson para manipular e analisar o JSON. Para saber mais sobre as configurações de compilação, confira [Criar novas builds de Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Criar um arquivo Java

Vamos criar uma pasta para seu aplicativo de exemplo. No diretório de trabalho, execute:

```console
mkdir -p src/main/java
```

Em seguida, nessa pasta, crie um arquivo chamado `Detect.java`.

## <a name="import-required-libraries"></a>Importe as bibliotecas necessárias

Abra `Detect.java` e adicione essas instruções de importação:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definir variáveis

Primeiro, você precisa criar uma classe pública para seu projeto:

```java
public class Detect {
  // All project code goes here...
}
```

Adicione essas linhas à classe `Detect`. Você observará que a chave de assinatura e o ponto de extremidade estão sendo lidos com base em variáveis de ambiente:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/detect?api-version=3.0";
```

Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre a autenticação com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Crie um cliente e compile uma solicitação

Adicione essa linha à classe `Detect` para instanciar o `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos compilar a solicitação POST. Fique à vontade para alterar o texto para detecção do idioma.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Criar uma função para analisar a resposta

Essa função simples analisa e melhora a aparência da resposta JSON no serviço de Tradução de Texto.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é fazer uma solicitação e receber uma resposta. Adicione essas linhas ao projeto:

```java
public static void main(String[] args) {
    try {
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

E, pronto, você já pode executar seu aplicativo de exemplo. Na linha de comando (ou sessão de terminal), navegue até a raiz do diretório de trabalho e execute:

```console
gradle build
```

Quando o build for concluído, execute:

```console
gradle run
```

## <a name="sample-response"></a>Resposta de exemplo

Depois de executar o exemplo, você deverá ver o seguinte impresso no terminal:

> [!NOTE]
> Localize a abreviação do país/região nesta [lista de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Confira a referência da API para saber tudo o que você pode fazer com a API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
