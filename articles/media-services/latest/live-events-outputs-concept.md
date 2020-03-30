---
title: Conceitos de Eventos ao Vivo e Saídas Ao Vivo no Azure Media Services v3
titleSuffix: Azure Media Services
description: Este tópico fornece uma visão geral de eventos ao vivo e saídas ao vivo no Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e6f2ad2c5c30e3c75e8d3588e386ea14e8e3350b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065953"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventos ao vivo e saídas ao vivo em serviços de mídia

O Azure Media Services permite que você entregue eventos ao vivo para seus clientes na nuvem do Azure. Para configurar seus eventos de transmissão ao vivo em Media Services v3, você precisa entender os conceitos discutidos neste artigo.

> [!TIP]
> Para clientes que migram de APIs v2 do Media Services v2, a entidade **Evento Ao Vivo** substitui o **Canal** em v2 e a saída **ao vivo** substitui o **Programa**.

## <a name="live-events"></a>Eventos ao Vivo

[Eventos ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Quando você cria um Evento Ao Vivo, um ponto final de entrada principal e secundário é criado que você pode usar para enviar um sinal ao vivo de um codificador remoto. O codificador de live remoto envia o feed de contribuição para esse ponto final de entrada usando o protocolo de entrada [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmentado-MP4). Para o protocolo de ingestão de RTMP,`rtmp://`o conteúdo pode ser enviado`rtmps://`no clear ( ) ou criptografado com segurança no fio( ). Para o protocolo de ingestão Smooth Streaming, os esquemas de URL compatíveis são `http://` ou `https://`.  

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um [Evento Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser definido como um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). Os tipos são definidos durante a criação usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None**: Um codificador ao vivo no local envia um fluxo de taxa de bits múltiplo. O fluxo ingerido passa pelo Evento Ao Vivo sem qualquer processamento adicional. Também chamado de modo de passagem.
* **LiveEventEncodingType.Standard**: Um codificador ao vivo no local envia um único fluxo de bitrate para o Live Event and Media Services cria vários fluxos de bitrate. Se a alimentação de contribuição for de 720p ou maior resolução, a predefinição **Default720p** codificará um conjunto de 6 pares de resolução/bitrates.
* **LiveEventEncodingType.Premium1080p**: Um codificador ao vivo no local envia um único fluxo de bitrate para o Live Event and Media Services cria vários fluxos de bitrate. A predefinição Default1080p especifica o conjunto de saída de pares de resolução/bitrates.

### <a name="pass-through"></a>Passagem

![pass-through Evento ao vivo com o diagrama de exemplo do Media Services](./media/live-streaming/pass-through.svg)

Ao usar o **Evento ao vivo** de passagem, você depende de seu codificador dinâmico local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o Evento ao vivo (usando o protocolo RTMP ou MP4 fragmentado). A seguir, o Evento ao vivo executa os fluxos de vídeo de entrada sem qualquer processamento adicional. Esse evento ao vivo é otimizado para eventos ao vivo de longa duração ou transmissão ao vivo linear 24x365. Ao criar esse tipo de Evento ao vivo, especifique Nenhum (LiveEventEncodingType.None).

Você pode enviar a contribuição em resoluções de até 4K e em uma taxa de quadros de 60 quadros / segundo, com codecs de vídeo H.264/AVC ou H.265/HEVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio. Para obter mais informações, consulte [comparação de tipos de eventos ao vivo](live-event-types-comparison.md).

> [!NOTE]
> Usar um método de passagem é a maneira mais econômica de fazer transmissão ao vivo quando você está fazendo vários eventos durante um longo período de tempo, e você já investiu em codificadores no local. Veja os detalhes de [preços](https://azure.microsoft.com/pricing/details/media-services/) .
>

Veja um exemplo de código do .NET no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Codificação ativa  

![codificação ao vivo com diagrama de exemplo do Media Services](./media/live-streaming/live-encoding.svg)

Ao usar a codificação ao vivo com serviços de mídia, você configura seu codificador ao vivo no local para enviar um único vídeo bitrate como o feed de contribuição para o Evento Ao Vivo (usando o protocolo RTMP ou Fragmented-Mp4). Em seguida, você configura um Evento ao Vivo para que ele codifica esse fluxo de bits de entrada para um [fluxo de vídeo de taxa de bits múltiplo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), e torna a saída disponível para entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming.

Quando você usa codificação ao vivo, você pode enviar o feed de contribuição apenas em resoluções até 1080p resolução a uma taxa de quadros de 30 quadros/segundo, com codec de vídeo H.264/AVC e codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec. Observe que o Pass-through Live Events pode suportar resoluções de até 4K a 60 quadros/segundo. Para obter mais informações, consulte [comparação de tipos de eventos ao vivo](live-event-types-comparison.md).

As resoluções e bits contidos na saída do codificador vivo são determinados pela preconfiguração. Se usar um codificador de vida **padrão** (LiveEventEncodingType.Standard), então a predefinição *Default720p* especifica um conjunto de seis pares de resolução/taxa de bits, indo de 720p a 3,5 Mbps para 192p a 200 kbps. Caso contrário, se usar um codificador de live **Premium1080p** (LiveEventEncodingType.Premium1080p), então a predefinição *Default1080p* especifica um conjunto de seis pares de resolução/taxa de bits, indo de 1080p a 3,5 Mbps para 180p a 200 kbps. Para obter informações, confira [Predefinições do sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Se você precisar personalizar a preconfiguração de codificação ao vivo, abra um bilhete de suporte através do portal Azure. Especifique a tabela desejada de resolução e bitrates. Verifique se há apenas uma camada em 720p (se solicitar uma preconfiguração para um codificador de vida padrão) ou em 1080p (se solicitar uma preconfiguração para um codificador ao vivo Premium1080p) e 6 camadas no máximo.

## <a name="creating-live-events"></a>Criando eventos ao vivo

### <a name="options"></a>Opções

Ao criar um Evento ao vivo, você pode especificar as seguintes opções:

* O protocolo de streaming para o Evento ao vivo (atualmente, os protocolos RTMP e Smooth Streaming são compatíveis).<br/>Não é possível alterar a opção de protocolo enquanto o Evento ao Vivo ou suas Saídas ao Vivo associadas estiverem em execução. Se você precisar de protocolos diferentes, crie um Evento ao Vivo separado para cada protocolo de streaming.  
* Ao criar o evento, é possível especificar sua inicialização automática. <br/>Quando a inicialização automática é definida como verdadeira, o evento em tempo real será iniciado após a criação. A cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional. Alternativamente, você pode iniciar o evento quando estiver pronto para iniciar o streaming.

    Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).

* Restrições de IP sobre a ingestão e versão prévia. É possível definir os endereços IP que têm permissão para ingerir um vídeo neste Evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IP usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').<br/>Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos seguintes formatos: endereço IpV4 com quatro números ou faixa de endereço CIDR.

    Se você quiser habilitar certos IPs em seus próprios firewalls ou quiser restringir entradas para seus eventos ao vivo para endereços IP do Azure, baixe um arquivo JSON das faixas de endereços IP do [Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Para obter detalhes sobre este arquivo, selecione a seção **Detalhes** na página.
    
* Ao criar o evento, você pode optar por ativar transcrições ao vivo. <br/> Por padrão, a transcrição ao vivo é desativada. Você não pode alterar essa propriedade enquanto o Evento ao Vivo ou suas saídas ao vivo associadas estiverem em execução. 

### <a name="naming-rules"></a>Regras de nomenclatura

* Max ao vivo nome do evento é 32 caracteres.
* O nome deve seguir este `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`padrão [regex:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

Veja também [as convenções de nomeação do Streaming Endpoints](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Para garantir a exclusividade do seu nome de evento ao vivo, você pode gerar um GUID e remover todos os hífens e suportes encaracolados (se houver). A seqüência será única em todos os eventos ao vivo e seu comprimento é garantido para ser 32.

## <a name="live-event-ingest-urls"></a>URLs de ingestão de Evento ao vivo

Uma vez que o Evento Ao Vivo é criado, você pode obter URLs de ingestão que você fornecerá ao codificador on-premises ao vivo. O codificador dinâmico usa essas URLs para gerar a entrada de um fluxo ao vivo. Para obter mais informações, consulte [codificadores ao vivo recomendados no local](recommended-on-premises-live-encoders.md).

Você pode usar URLs intuitivas ou não intuitivas.

> [!NOTE] 
> Para que uma URL de ingestão seja preditiva, defina o modo de "personalização".

* URL não intuitiva

    URL sem vaidade é o modo padrão no Media Services v3. Você possivelmente obterá o Evento ao vivo rapidamente, mas a URL de ingestão é conhecida apenas ao iniciar o evento ao vivo. A URL será alterada se você parar/iniciar o Evento ao vivo. <br/>Non-Vanity é útil em cenários quando um usuário final quer transmitir usando um aplicativo onde o aplicativo quer obter um evento ao vivo o mais rápido possível e ter uma URL de ingestão dinâmica não é um problema.

    Se um aplicativo cliente não precisar pré-gerar uma URL de ingestão antes da criação do Evento Ao Vivo, deixe que os Serviços de Mídia gerem automaticamente o Token de Acesso para o evento ao vivo.

* URL intuitiva

    O modo vanity é preferido por grandes emissoras de mídia que usam codificadores de transmissão de hardware e não querem reconfigurar seus codificadores quando iniciam o Evento Ao Vivo. Eles querem uma URL preditiva, que não muda com o tempo.

    Para especificar este `vanityUrl` modo, você define `true` `false`como no momento da criação (padrão é ). Você também precisa passar seu próprio`LiveEventInput.accessToken`token de acesso () no momento da criação. Você especifica o valor do token para evitar um token aleatório na URL. O token de acesso tem que ser uma seqüência GUID válida (com ou sem os hífens). Uma vez que o modo está definido, ele não pode ser atualizado.

    O token de acesso precisa ser único em seu data center. Se o seu aplicativo precisar usar uma URL de vaidade, é recomendável criar sempre uma nova instância GUID para o seu token de acesso (em vez de reutilizar qualquer GUID existente).

    Use as SEGUINTES APIs para ativar a URL de vaidade `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`e definir o token de acesso a um GUID válido (por exemplo, ).  

    |Idioma|Habilitar url de vaidade|Definir token de acesso|
    |---|---|---|
    |REST|[propriedades.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vaidade-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--token de acesso](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.vanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura de URL de ingestão dinâmica

* A cadeia de caracteres *aleatória* abaixo é um número hexadecimal de 128 bits composto de 32 caracteres de “0” a “9” e “a” a “f”.
* *seu token de acesso*: A seqüência GUID válida que você definiu ao usar o modo de vaidade. Por exemplo, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome do fluxo*: Indica o nome do fluxo para uma conexão específica. O valor do nome de fluxo geralmente é adicionado pelo codificador ao vivo que você usa. Você pode configurar o codificador de vida para usar qualquer nome para descrever a conexão, por exemplo: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>URL não intuitiva

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL intuitiva

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de visualização do Evento ao vivo

Uma vez que o Evento Ao Vivo comece a receber o feed de contribuição, você pode usar seu ponto final de visualização para visualizar e validar que você está recebendo a transmissão ao vivo antes de publicar mais. Depois de verificar se a transmissão de visualização é boa, você pode usar o Evento ao Vivo para disponibilizar a transmissão ao vivo para entrega através de um ou mais pontos finais (pré-criados). Para isso, crie uma nova [saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) no Evento Ao Vivo.

> [!IMPORTANT]
> Certifique-se de que o vídeo está fluindo para a URL de visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa duração do Live Event

Para obter detalhes, consulte [operações de longo prazo.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Saídas ao Vivo

Uma vez que você tenha o fluxo fluindo para o Evento Ao Vivo, você pode iniciar o evento de streaming criando um [Ativo](https://docs.microsoft.com/rest/api/media/assets), [Saída ao Vivo](https://docs.microsoft.com/rest/api/media/liveoutputs)e [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). A Saída ao vivo arquiva o fluxo e o disponibiliza para usuários por meio do [Ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Para obter informações detalhadas sobre saídas ao vivo, consulte [Usando um DVR em nuvem](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Veja o artigo [perguntas frequentes.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Faça perguntas e receba atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Tutorial de live streaming](stream-live-tutorial-with-api.md)
