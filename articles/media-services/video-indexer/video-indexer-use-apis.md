---
title: Usar a API do Video Indexer
titleSuffix: Azure Media Services
description: Este artigo descreve como começar a usar a API Video Indexer dos serviços de mídia do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 4da437fecf0529194d72b2d403302ff81055ec0f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892678"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Tutorial: Usar a API do Video Indexer

O Video Indexer consolida várias tecnologias de ia (inteligência artificial de áudio e vídeo) oferecidas pela Microsoft em um serviço integrado, tornando o desenvolvimento mais simples. As APIs são projetadas para permitir que os desenvolvedores se consumam em tecnologias de ia de mídia sem se preocupar com a escala, o alcance global, a disponibilidade e a confiabilidade das plataformas de nuvem. Você pode usar a API para carregar seus arquivos, obter informações detalhadas sobre o vídeo, obter URLs de ideias inseridas e widgets do jogador e muito mais.

Ao criar uma conta de Video Indexer, você pode escolher uma conta de avaliação gratuita (onde você obtém um determinado número de minutos de indexação gratuita) ou uma opção paga (onde você não está limitado pela cota). Com uma avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita para os usuários do site e até 2400 minutos de indexação gratuita para usuários de API. Com uma opção paga, você cria uma conta de Video Indexer que está [conectada à sua assinatura do Azure e uma conta dos serviços de mídia do Azure](connect-to-azure.md). Você paga pelos minutos indexados, bem como os encargos relacionados à conta dos Serviços de Mídia do Azure.

Este artigo mostra como os desenvolvedores podem aproveitar a [API do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Inscrever-se à API

1. Entre no [Portal do Desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/).
    
    ![Entrar no portal do desenvolvedor Video Indexer](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Você deve usar o mesmo provedor usado quando se inscreveu no Video Indexer.
   > * Contas pessoais do Google e Microsoft (Outlook/Live) só podem ser usadas para contas de avaliação. Contas conectadas ao Azure exigem o Azure AD.
   > * Pode haver apenas uma conta ativa por email. Se um usuário tentar entrar com user@gmail.com para o LinkedIn e posterior com user@gmail.com para o Google, o último exibirá uma página de erro, dizendo que o usuário já existe.

2. Inscrever-se.

    Selecione a guia [produtos](https://api-portal.videoindexer.ai/products) . Em seguida, selecione autorização e assinar.
    
    ![Guia produtos no portal do desenvolvedor Video Indexer](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Novos usuários são automaticamente inscritos na Autorização.
    
    Depois de assinar, você pode ver sua assinatura e suas chaves primárias e secundárias. As chaves devem ser protegidas. As chaves só devem ser usadas pelo seu código do servidor. Eles não devem estar disponíveis no lado do cliente (. js,. html e assim por diante).

    ![Assinatura e chaves no portal Video Indexer Developer](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> O usuário do Video Indexer pode usar uma chave de assinatura única para se conectar a várias contas do aplicativo. Depois você pode vincular essas contas do Video Indexer para diferentes contas de Serviços de Mídia.

## <a name="obtain-access-token-using-the-authorization-api"></a>Obter o token de acesso usando a API de autorização

Depois de assinar a API de autorização, você pode obter tokens de acesso. Esses tokens de acesso são usados para autenticar na API de operações.

Cada chamada para a API de operações deve estar associada a um token de acesso, correspondendo ao escopo de autorização da chamada.

- Nível de usuário: os tokens de acesso no nível do usuário permitem executar operações no nível do **usuário** . Por exemplo, obter contas associadas.
- Nível da conta: os tokens de acesso no nível da conta permitem executar operações no nível da **conta** ou do **vídeo** . Por exemplo, carregar vídeo, listar todos os vídeos, obter informações de vídeo e assim por diante.
- Nível de vídeo: os tokens de acesso no nível do vídeo permitem executar operações em um **vídeo**específico. Por exemplo, obtenha informações sobre o vídeo, baixe legendas, receba widgets e assim por diante.

Você pode controlar se esses tokens são somente leitura ou se eles permitem a edição especificando **allowEdit = true/false**.

Para a maioria dos cenários de servidor para servidor, você provavelmente usará o mesmo token de **conta** , pois ele abrange operações de **conta** e operações de **vídeo** . No entanto, se você estiver planejando fazer chamadas do lado do cliente para Video Indexer (por exemplo, do JavaScript), convém usar um token de acesso de **vídeo** para impedir que os clientes obtenham acesso a toda a conta. Isso também é o motivo pelo qual, ao inserir Video Indexer código de cliente em seu cliente (por exemplo, usando o **widget obter insights** ou o **widget Get Player**), você deve fornecer um token de acesso de **vídeo** .

Para facilitar, você pode usar a **API de autorização**> **GetAccounts** para obter suas contas sem obter primeiro um token de usuário. Você também pode pedir para obter as contas com tokens válidos, permitindo que você ignore uma chamada adicional para obter um token de conta.

Os tokens de acesso expiram após 1 hora. Certifique-se de que seu token de acesso seja válido antes de usar a API de operações. Se ele expirar, chame a API de autorização novamente para obter um novo token de acesso.

Você está pronto para começar a integração com a API. Encontre [a descrição detalhada de cada API REST do Indexador de Vídeo](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID da Conta

O parâmetro de ID da conta é obrigatório em todas as chamadas da API operacional. O ID da conta é um GUID que pode ser obtido de uma das seguintes maneiras:

* Use o **site do Video Indexer** para obter a ID da conta:

    1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
    2. Navegue até a **configurações** página.
    3. Copie a ID de conta.

        ![Configurações de Video Indexer e ID da conta](./media/video-indexer-use-apis/account-id.png)

* Use o **Portal do Desenvolvedor do Video Indexer** para obter a ID da conta de maneira programática.

    Use o [obter contas](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API.

    > [!TIP]
    > Você pode gerar tokens de acesso para as contas definindo `generateAccessTokens=true`.

* Obtenha o ID da conta a partir do URL de uma página do player em sua conta.

    Quando você assiste a um vídeo, o ID aparece após a `accounts`seção e antes da`videos` seção.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recomendações

Esta seção lista algumas recomendações ao usar a API do Video Indexer.

- Se você estiver planejando carregar um vídeo, é recomendável colocá-lo em algum local de rede pública (por exemplo, OneDrive). Obter o link para vídeo e fornecer a URL como o parâmetro de arquivo de upload.

    A URL fornecida ao Video Indexer deve apontar para um arquivo de mídia (áudio ou vídeo). Alguns dos links gerados pelo OneDrive são para uma página HTML que contém o arquivo. Uma verificação fácil para a URL é colá-la em um navegador — se o arquivo começar a ser baixado, provavelmente será uma boa URL. Se o navegador estiver renderizando alguma visualização, provavelmente não é um link para um arquivo, mas para uma página HTML.

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

## <a name="see-also"></a>Consulte

- [Visão geral do indexador vídeo](video-indexer-overview.md)
- [Regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Próximos passos

[Examinar os detalhes da saída JSON](video-indexer-output-json-v2.md).
