---
title: Usar a API do Video Indexer
titleSuffix: Azure Media Services
description: Este artigo descreve como começar com a API do Indexador de Vídeo do Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 8b6d160f71bfe8b2e5c447296d511b54ce6542c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245844"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Tutorial: Usar a API do Video Indexer

O Video Indexer consolida várias tecnologias de inteligência artificial (IA) de áudio e vídeo oferecidas pela Microsoft em um serviço integrado, tornando o desenvolvimento mais simples. As APIs foram projetadas para permitir que os desenvolvedores se concentrem no consumo de tecnologias de IA de mídia sem se preocupar com escala, alcance global, disponibilidade e confiabilidade de plataformas em nuvem. Você pode usar a API para carregar seus arquivos, obter insights de vídeo detalhados, obter URLs de insights incorporáveis e widgets do jogador e muito mais.

Ao criar uma conta do Indexador de Vídeo, você pode escolher uma conta de avaliação gratuita (onde você recebe um certo número de minutos de indexação grátis) ou uma opção paga (onde você não está limitado pela cota). Com uma avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários de sites e até 2400 minutos de indexação gratuita para usuários de API. Com uma opção paga, você cria uma conta do Video Indexer que está [conectada à sua assinatura do Azure e a uma conta do Azure Media Services](connect-to-azure.md). Você paga pelos minutos indexados, bem como os encargos relacionados à conta dos Serviços de Mídia do Azure.

Este artigo mostra como os desenvolvedores podem aproveitar a [API do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Inscrever-se à API

1. Entre no [Portal do Desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/).
    
    ![Faça login no Portal do Desenvolvedor do Indexador de Vídeo](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Você deve usar o mesmo provedor usado quando se inscreveu no Video Indexer.
   > * Contas pessoais do Google e da Microsoft (Outlook/Live) só podem ser usadas para contas de teste. Contas conectadas ao Azure exigem o Azure AD.
   > * Só pode haver uma conta ativa por e-mail. Se um usuário tentar user@gmail.com fazer login no user@gmail.com LinkedIn e depois com o Google, este último exibirá uma página de erro, dizendo que o usuário já existe.

2. Inscrever-se.

    Selecione a guia [Produtos.](https://api-portal.videoindexer.ai/products) Em seguida, selecione Autorização e inscreva-se.
    
    ![Guia de produtos no Portal do Desenvolvedor do Indexador de Vídeo](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Novos usuários são automaticamente inscritos na Autorização.
    
    Depois de se inscrever, você pode ver sua assinatura e suas chaves primárias e secundárias. As chaves devem ser protegidas. As chaves só devem ser usadas pelo seu código do servidor. Eles não devem estar disponíveis no lado do cliente (.js, .html, e assim por diante).

    ![Assinatura e chaves no Portal de Desenvolvedores de Indexador de Vídeo](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> O usuário do Video Indexer pode usar uma chave de assinatura única para se conectar a várias contas do aplicativo. Depois você pode vincular essas contas do Video Indexer para diferentes contas de Serviços de Mídia.

## <a name="obtain-access-token-using-the-authorization-api"></a>Obter o token de acesso usando a API de autorização

Depois de assinar a API de autorização, você pode obter tokens de acesso. Esses tokens de acesso são usados para autenticar na API de operações.

Cada chamada para a API de operações deve estar associada a um token de acesso, correspondendo ao escopo de autorização da chamada.

- Nível do usuário: Os tokens de acesso ao nível do usuário permitem que você execute operações no nível do **usuário.** Por exemplo, obter contas associadas.
- Nível da conta: Os tokens de acesso ao nível da conta permitem que você execute operações no nível da **conta** ou no nível de **vídeo.** Por exemplo, envie vídeo, liste todos os vídeos, obtenha insights de vídeo, e assim por diante.
- Nível de vídeo: Os tokens de acesso ao nível de vídeo permitem que você execute operações em um **vídeo**específico . Por exemplo, obtenha insights de vídeo, legendas de download, obter widgets, e assim por diante.

Você pode controlar se esses tokens são somente leitura ou se eles permitem a edição especificando **allowEdit=true/false**.

Para a maioria dos cenários de servidor para servidor, você provavelmente usará o mesmo token de **conta,** já que abrange tanto operações de **conta** quanto operações **de vídeo.** No entanto, se você está planejando fazer chamadas do lado do cliente para o Video Indexer (por exemplo, do JavaScript), você gostaria de usar um token de acesso de **vídeo** para impedir que os clientes tenham acesso a toda a conta. Essa também é a razão pela qual ao incorporar o código cliente do Video Indexer em seu cliente (por exemplo, usando **Get Insights Widget** ou **Get Player Widget),** você deve fornecer um token de acesso de **vídeo.**

Para facilitar, você pode usar a **API de autorização**> **GetAccounts** para obter suas contas sem obter primeiro um token de usuário. Você também pode pedir para obter as contas com tokens válidos, permitindo que você ignore uma chamada adicional para obter um token de conta.

Os tokens de acesso expiram após 1 hora. Certifique-se de que seu token de acesso seja válido antes de usar a API de operações. Se expirar, ligue novamente para a API autorização para obter um novo token de acesso.

Você está pronto para começar a se integrar com a API. Encontre [a descrição detalhada de cada API REST do Indexador de Vídeo](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID da Conta

O parâmetro de ID da conta é obrigatório em todas as chamadas da API operacional. O ID da conta é um GUID que pode ser obtido de uma das seguintes maneiras:

* Use o **site do Video Indexer** para obter a ID da conta:

    1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
    2. Navegue até a **configurações** página.
    3. Copie a ID de conta.

        ![Configurações do indexador de vídeo e id da conta](./media/video-indexer-use-apis/account-id.png)

* Use o **Portal do Desenvolvedor do Video Indexer** para obter a ID da conta de maneira programática.

    Use a API [da conta Get.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)

    > [!TIP]
    > Você pode gerar tokens de acesso para as contas definindo `generateAccessTokens=true`.

* Obtenha o ID da conta a partir do URL de uma página do player em sua conta.

    Quando você assiste a um vídeo, o ID aparece após a `accounts`seção e antes da`videos` seção.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recomendações

Esta seção lista algumas recomendações ao usar a API do Video Indexer.

- Se você está planejando fazer upload de um vídeo, é recomendável colocar o arquivo em algum local de rede pública (por exemplo, OneDrive). Obter o link para vídeo e fornecer a URL como o parâmetro de arquivo de upload.

    A URL fornecida ao Video Indexer deve apontar para um arquivo de mídia (áudio ou vídeo). Alguns dos links gerados pelo OneDrive são para uma página HTML que contém o arquivo. Uma verificação fácil para a URL é colhê-la em um navegador — se o arquivo começar a ser baixado, provavelmente é uma boa URL. Se o navegador está visualizando alguma visualização, provavelmente não é um link para um arquivo, mas para uma página HTML.

- Quando você chama a API que recebe insights de vídeo para o vídeo especificado, você obtém uma saída JSON detalhada como o conteúdo da resposta. [Ver detalhes sobre o JSON retornado neste tópico](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Exemplo de código

O seguinte snippet de código em C# demonstra o uso de todas as APIs do indexador de vídeo juntos.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Confira também

- [Visão geral do indexador vídeo](video-indexer-overview.md)
- [Regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Próximas etapas

- [Examine detalhes da saída JSON](video-indexer-output-json-v2.md)
- Confira o [código de amostra](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/API) que demonstra aspecto importante do upload e indexação de um vídeo. Seguir o código lhe dará uma boa idéia de como usar nossa API para funcionalidades básicas. Certifique-se de ler os comentários inline e notar nossos conselhos de boas práticas.

