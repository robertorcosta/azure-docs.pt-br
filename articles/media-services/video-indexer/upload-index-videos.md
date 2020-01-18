---
title: Carregar e indexar vídeos com o Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar APIs para carregar e indexar seus vídeos com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: e457fbe5b8dd23c93110fb8ccc7d8857128de82c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169367"
---
# <a name="upload-and-index-your-videos"></a>Carregar e indexar seus vídeos  

Ao carregar vídeos com a API do Video Indexer, você tem as seguintes opções de upload: 

* Carregue o vídeo de uma URL (preferido),
* envie o arquivo de vídeo como uma matriz de bytes no corpo da solicitação,
* Use o ativo dos serviços de mídia do Azure existente fornecendo a [ID do ativo](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (com suporte somente em contas pagas).

Depois que o vídeo for carregado, Video Indexer (opcionalmente) codificará o vídeo (discutido no artigo). Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. Com a opção paga, você cria uma conta do Video Indexer que está [conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure](connect-to-azure.md). Você paga por minutos indexados, bem como os encargos relacionados à conta de mídia. 

O artigo mostra como carregar e indexar vídeos com estas opções:

* [O site Video Indexer](#website) 
* [As APIs de Video Indexer](#apis)

## <a name="uploading-considerations-and-limitations"></a>Considerações sobre o carregamento e limitações
 
- Um nome do vídeo não deve ter mais de 80 caracteres.
- Ao carregar o vídeo com base na URL (preferencial), o ponto de extremidade deve ser protegido com o TLS 1,2 (ou superior).
- O tamanho do carregamento com a opção de URL é limitado a 30 GB.
- O comprimento da URL de solicitação é limitado a 6144 caracteres em que o comprimento da URL da cadeia de caracteres de consulta é limitado a 4096 caracteres.
- O tamanho do carregamento com a opção de matriz de bytes é limitado a 2GB.
- A opção de matriz de bytes atinge o tempo limite após 30 min.
- A URL fornecida no `videoURL` param precisa ser codificada.
- A indexação de ativos de serviços de mídia tem a mesma limitação de indexação da URL.
- Video Indexer tem um limite de duração máximo de 4 horas para um único arquivo.
- A URL precisa estar acessível (por exemplo, uma URL pública). 

    Se for uma URL privada, o token de acesso precisará ser fornecido na solicitação.
- A URL deve apontar para um arquivo de mídia válido e não para uma página da Web, como um link para a página de `www.youtube.com`.
- Você pode carregar até 60 filmes por minuto.

> [!Tip]
> É recomendável usar o .NET Framework versão 4.6.2. ou superior porque versões mais antigas do .NET Framework não usam TLS 1.2 por padrão.
>
> Se você precisar usar versões mais antigas do .NET Framework, adicione uma linha em seu código antes de fazer a chamada à API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Formatos de arquivo com suporte para Video Indexer

Consulte o artigo [formatos de contêiner/arquivo de entrada](../latest/media-encoder-standard-formats.md#input-containerfile-formats) para obter uma lista de formatos de arquivo que você pode usar com Video indexer.

## <a name="a-idwebsiteupload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>carregar e indexar um vídeo usando o site do Video Indexer

> [!NOTE]
> Um nome do vídeo não deve ter mais de 80 caracteres.

1. Conecte-se ao site do [Video Indexer](https://www.videoindexer.ai/).
2. Para carregar um vídeo, pressione o botão ou o link **Carregar**.

    ![Carregar](./media/video-indexer-get-started/video-indexer-upload.png)

    Depois que o seu vídeo tiver sido carregado, o Video Indexer iniciará a indexação e a análise do vídeo.

    ![Carregado](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Depois que o Video Indexer terminar de analisar, você receberá uma notificação com um link para seu vídeo e uma breve descrição do que foi encontrado nele. Por exemplo: people (pessoas), topics (tópicos), OCRs.

## <a name="a-idapisupload-and-index-with-api"></a><a id="apis"/>carregar e indexar com a API

Use o [carregar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API de vídeo para carregar e indexar vídeos com base em uma URL. O exemplo de código a seguir inclui o código comentado que mostra como carregar a matriz de bytes. 

### <a name="configurations-and-params"></a>Configurações e parâmetros

Esta seção descreve alguns parâmetros opcionais e quando você deseja defini-los.

#### <a name="externalid"></a>externalID 

Este parâmetro permite que você especifique uma ID que será associada ao vídeo. A ID pode ser aplicada à integração de sistema externa VCM (Gerenciamento de Conteúdo de Vídeo). Os vídeos localizados no portal do Video Indexer podem ser pesquisados usando a ID externa especificada.

#### <a name="callbackurl"></a>callbackUrl

Uma URL usada para notificar o cliente (usando uma solicitação POST) sobre os eventos a seguir:

- Alteração de estado de indexação: 
    - Propriedades:    
    
        |Nome|Description|
        |---|---|
        |id|A ID do vídeo|
        |state|O estado do vídeo|  
    - Exemplo: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Pessoa identificada no vídeo:
  - Propriedades
    
      |Nome|Description|
      |---|---|
      |id| A ID do vídeo|
      |faceId|A identificação de face que aparece no índice de vídeo|
      |knownPersonId|A ID da pessoa que é exclusiva dentro de um modelo de detecção facial|
      |personName|O nome da pessoa|
        
    - Exemplo: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="notes"></a>Observações

- O Video Indexer retorna quaisquer parâmetros existentes fornecidos na URL original.
- A URL fornecida deve ser codificada.

#### <a name="indexingpreset"></a>indexingPreset

Use esse parâmetro se gravações brutas ou externas contiverem ruídos de fundo. Esse parâmetro é usado para configurar o processo de indexação. É possível especificar os seguintes valores:

- `AudioOnly` – Indexar e extrair insights usando apenas áudio (ignorando vídeo)
- `VideoOnly`-indexar e extrair informações usando somente vídeo (ignorando áudio)
- `Default` – Indexar e extrair insights usando áudio e vídeo
- `DefaultWithNoiseReduction` – Indexar e extrair insights de áudio e vídeo durante a aplicação de algoritmos de redução de ruído no fluxo de áudio

O preço depende da opção de indexação selecionada.  

#### <a name="priority"></a>priority

Os vídeos são indexados pelo Video Indexer de acordo com a prioridade. Use o parâmetro **priority** para especificar a prioridade de índice. Os seguintes valores são válidos: **Baixo**, **Normal** (padrão) e **Alto**.

O parâmetro **priority** tem suporte apenas para contas pagas.

#### <a name="streamingpreset"></a>streamingPreset

Depois que o vídeo tiver sido carregado, o Video Indexer codificará opcionalmente o vídeo. Em seguida, passe para a indexação e análise do vídeo. Quando o Video Indexer terminar a análise, você receberá uma notificação com a ID do vídeo.  

Ao usar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [Reindexar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), um dos parâmetros opcionais é `streamingPreset`. Se você definir `streamingPreset` como `Default`, `SingleBitrate` ou `AdaptiveBitrate`, o processo de codificação será disparado. Depois que os trabalhos de indexação e codificação forem concluídos, o vídeo será publicado para que você também possa transmiti-lo. O Ponto de extremidade de streaming do qual você deseja transmitir o vídeo deve estar no estado **Executando**.

Para executar trabalhos de indexação e de codificação, a [conta dos Serviços de Mídia do Azure conectada à sua conta do Video Indexer](connect-to-azure.md), requer unidades reservadas. Para obter mais informações, consulte [Scaling Media Processing](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) (Colocação em escala do processamento de mídia). Como esses são trabalhos com uso intensivo de computação, o tipo de unidade S3 é altamente recomendável. O número de RUs define o número máximo de trabalhos que podem ser executados em paralelo. A recomendação de linha de base é de 10 RUs de S3. 

Se você apenas deseja indexar seu vídeo, mas não o codificar, defina `streamingPreset` como `NoStreaming`.

#### <a name="videourl"></a>VideoUrl

Uma URL do arquivo de áudio/vídeo a serem indexados. O URL deve apontar para um arquivo de mídia (as páginas HTML não são suportadas). O arquivo pode ser protegido por um token de acesso fornecido como parte do URI e o terminal que atende ao arquivo deve ser protegido com o TLS 1.2 ou superior. A URL deve ser codificado. 

Se o `videoUrl` não for especificado, o Indexador de Vídeo espera que você passe o arquivo como um conteúdo de corpo de várias partes / formulário.

### <a name="code-sample"></a>Exemplo de código

O seguinte snippet de código em C# demonstra o uso de todas as APIs do indexador de vídeo juntos.

#### <a name="instructions-for-running-this-code-sample"></a>Instruções para executar este exemplo de código

Depois de copiar esse código em sua plataforma de desenvolvimento, você precisará fornecer dois parâmetros: chave de autenticação do gerenciamento de API e URL de vídeo.

* Chave de API – chave de API é sua chave de assinatura de gerenciamento de API pessoal, que permitirá que você obtenha um token de acesso para executar operações em sua conta de Video Indexer. 

    Para obter sua chave de API, percorra este fluxo:

    * Navegar para https://api-portal.videoindexer.ai/
    * Logon
    * Ir para **produtos** -> **autorização** -> **assinatura de autorização**
    * Copiar a **chave primária**
* URL de vídeo – uma URL do arquivo de vídeo/áudio a ser indexada. O URL deve apontar para um arquivo de mídia (as páginas HTML não são suportadas). O arquivo pode ser protegido por um token de acesso fornecido como parte do URI e o terminal que atende ao arquivo deve ser protegido com o TLS 1.2 ou superior. A URL deve ser codificado.

O resultado da execução bem-sucedida do exemplo de código incluirá uma URL do widget Insight e uma URL do widget do Player que permitirá que você examine as informações e o vídeo carregados, respectivamente. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Erros comuns

Os códigos de status listados na tabela a seguir podem ser retornados pela operação de Upload.

|Código de status|ErrorType (no corpo da resposta)|Description|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|O mesmo vídeo já está em curso de ser processado na conta especificada.|
|400|VIDEO_ALREADY_FAILED|O mesmo vídeo falhou ao processar na conta informada há menos de duas horas. Os clientes da API devem aguardar pelo menos duas horas antes de carregar novamente um vídeo.|

## <a name="next-steps"></a>Próximos passos

[Examinar a saída do Video Indexer do Azure produzida pela API](video-indexer-output-json-v2.md)
