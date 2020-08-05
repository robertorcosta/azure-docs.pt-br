---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: a0eb507237044a1a28b75532ff1f782c1ad88dbb
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407704"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de Fala do Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta Site

Crie uma nova pasta vazia. Caso deseje hospedar o exemplo em um servidor Web, certifique-se de que ele possa acessar a pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Fala para JavaScript nessa pasta

Baixe o SDK de Fala como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o na pasta recém-criada. Isso resulta no desempacotamento de cinco arquivos, incluindo `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
O último arquivo é opcional e é útil para depuração no código do SDK.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo arquivo na pasta, chamada `index.html`, e abra este arquivo com um editor de texto.

1. Crie o esqueleto do HTML a seguir:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-translate-stt.html)]

## <a name="create-the-token-source-optional"></a>Criar a origem do token (opcional)

Caso deseje hospedar a página da Web em um servidor Web, é possível fornecer opcionalmente uma origem do token para seu aplicativo de demonstração.
Dessa forma, sua chave de assinatura nunca sairá do seu servidor, permitindo que seus usuários usem funcionalidades de fala sem inserir nenhum código de autorização por si próprios.

Crie um arquivo chamado `token.php`. Neste exemplo, pressupomos que o seu servidor Web dê suporte à linguagem de script PHP com o cURL habilitado. Insira o seguinte código:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Os tokens de autorização só têm um tempo de vida limitado.
> Este exemplo simplificado não mostra como atualizar os tokens de autorização automaticamente. Como um usuário, é possível recarregar manualmente a página ou pressionar F5 para atualizar.

## <a name="build-and-run-the-sample-locally"></a>Criar e executar o exemplo localmente

Para iniciar o aplicativo, clique duas vezes no arquivo index.html ou abra index.html com seu navegador da Web favorito. Ele apresentará uma GUI simples, permitindo que você insira sua chave de assinatura e sua [região](../../../../regions.md) e dispare a transcrição do texto da fala de entrada.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Criar e executar o exemplo por meio de um servidor Web

Para iniciar o aplicativo, abra seu navegador da Web favorito e aponte-o para a URL pública na qual a pasta está hospedada, insira sua [região](../../../../regions.md) e dispare a transcrição do texto da fala de entrada. Se configurado, ele adquirirá um token da origem do token.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]