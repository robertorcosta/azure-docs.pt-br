---
title: Perguntas frequentes sobre os Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309179"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Serviços de mídia v3 perguntas freqüentes

Este artigo dá respostas a perguntas frequentes sobre o Azure Media Services v3.

## <a name="general"></a>Geral

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem executar ações nos recursos do Azure Media Services? 

Consulte [o RBAC (Role-based Access Control, controle de acesso baseado em função) para contas de Serviços de Mídia](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Como faço o streaming para dispositivos Apple iOS?

Certifique-se de que você tem **(format=m3u8-aapl)** no final do seu caminho (após a parte **/manifesto** da URL) para dizer ao servidor de origem de streaming para retornar o conteúdo HTTP Live Streaming (HLS) para consumo em dispositivos nativos do Apple iOS. Para obter detalhes, consulte [Entregando conteúdo](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer para configurar Unidades Reservadas para Mídia?

Para os trabalhos de Análise de Áudio e Análise de Vídeo que são acionados pelo Media Services v3 ou Video Indexer, recomendamos que você provisione sua conta com 10 Unidades Reservadas de Mídia S3 (MRUs). Se você precisar de mais de 10 MRUs S3, abra um bilhete de suporte usando o [portal Azure](https://portal.azure.com/).

Para obter detalhes, consulte [Dimension media processing](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada Transformação descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real aos Serviços de Mídia para aplicar o Transform a um conteúdo de vídeo ou áudio de entrada. Depois que o Transform for criado, você pode enviar Jobs usando APIs de Serviços de Mídia ou qualquer um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>carreguei, codifiquei e publiquei um vídeo. Por que o vídeo não reproduzia quando tento transmitir?

Uma das razões mais comuns é que você não tem o ponto final do streaming do qual você está tentando reproduzir no estado de Running.

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Quando você estiver usando paginação, você deve sempre usar o próximo link para enumerar a coleção e não depender de um determinado tamanho de página. Para obter detalhes e exemplos, confira [Filtragem, ordenação, paginação](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais recursos ainda não estão disponíveis no Azure Media Services v3?

Para obter [detalhes, consulte As lacunas de recursos em relação às APIs v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de movimentação de uma conta de Serviços de Mídia entre assinaturas?  

Para obter detalhes, consulte [Mover uma conta de Serviços de Mídia entre assinaturas](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão ao vivo 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Como faço para parar a transmissão ao vivo depois que a transmissão é feita?

Você pode abordá-lo do lado do cliente ou do lado do servidor.

#### <a name="client-side"></a>Lado do cliente

Seu aplicativo web deve solicitar ao usuário se ele quiser terminar a transmissão, pois está fechando o navegador. Este é um evento de navegador que seu aplicativo web pode lidar.

#### <a name="server-side"></a>Lado do Servidor

Você pode monitorar eventos ao vivo assinando eventos do Azure Event Grid. Para obter mais informações, consulte o esquema de [eventos EventGrid](media-services-event-schemas.md#live-event-types).

Você pode:

* [Assine](reacting-to-media-services-events.md) os eventos [microsoft.media.media.liveeventEncoder desconectado](media-services-event-schemas.md#liveeventencoderdisconnected) e monitore que nenhuma reconexão entre por um tempo para parar e excluir seu evento ao vivo.
* [Inscreva-se](reacting-to-media-services-events.md) nos eventos de [batimentos cardíacos](media-services-event-schemas.md#liveeventingestheartbeat) de nível de pista. Se todas as faixas tiverem uma taxa de bits de entrada caindo para 0 ou o último carimbo de tempo não estiver mais aumentando, você pode encerrar o evento ao vivo com segurança. Os eventos de batimentos cardíacos chegam a cada 20 segundos para cada faixa, então pode ser um pouco verboso.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Como insiro quebras/vídeos e lousas de imagem durante uma transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo Telestream Wirecast, Switcher Studio (no iOS) e OBS Studio (aplicativo gratuito).

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar criptografia de chave clara AES-128 ou um sistema DRM?

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente através do TLS para que a chave seja criptografada em trânsito, mas sem qualquer criptografia adicional ("no claro"). Como resultado, a chave usada para descriptografar o conteúdo é acessível ao player cliente e pode ser visualizada em um rastreamento de rede no cliente em texto simples. A criptografia de chave AES-128 não criptografada é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

Sistemas DRM como PlayReady, Widevine e FairPlay fornecem um nível adicional de criptografia na chave usada para descriptografar o conteúdo, em comparação com uma chave clara AES-128. A chave de conteúdo é criptografada para uma chave protegida pelo tempo de execução do DRM, além de qualquer criptografia de nível de transporte fornecida pela TLS. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. Recomendamos drm para casos de uso em que o espectador pode não ser um partido confiável e você precisa do mais alto nível de segurança.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como mostrar um vídeo apenas para usuários que tenham uma permissão específica, sem usar o Azure AD?

Você não precisa usar nenhum provedor de token específico, como o Azure Active Directory (Azure AD). Você pode criar seu próprio provedor [JWT](https://jwt.io/) (o chamado Secure Token Service, ou STS) usando criptografia de chave assimétrica. Em seu STS personalizado, você pode adicionar reivindicações com base na sua lógica de negócios.

Certifique-se de que o emissor, a audiência e as reivindicações correspondam exatamente entre o que está no JWT e o `ContentKeyPolicyRestriction` valor usado em `ContentKeyPolicy`.

Para obter mais informações, consulte [Proteger seu conteúdo usando criptografia dinâmica do Media Services](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Como e onde consegui um token JWT antes de usá-lo para solicitar uma licença ou chave?

Para a produção, você precisa ter o Secure Token Service (ou seja, um serviço web), que emite um token JWT após uma solicitação HTTPS. Para o teste, você pode `GetTokenAsync` usar o código mostrado no método definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

O jogador faz uma solicitação, depois que um usuário é autenticado, ao STS para tal token e atribui-o como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

Para um exemplo de execução de STS com uma chave simétrica ou uma chave assimétrica, consulte a [ferramenta JWT](https://aka.ms/jwt). Para um exemplo de um player baseado no Azure Media Player usando um token JWT, consulte a ferramenta de teste de [mídia Azure](https://aka.ms/amtest). (Expanda o **link player_settings** para ver a entrada do token.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizar solicitações para transmitir vídeos com criptografia AES?

A abordagem correta é usar o Secure Token Service. No STS, dependendo do perfil do usuário, adicione diferentes reivindicações (como "Usuário Premium", "Usuário Básico", "Usuário de Avaliação Gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. Para diferentes conteúdos `ContentKeyPolicyRestriction` ou ativos, terá o valor correspondente. `RequiredClaims`

Use as APIs do Azure Media Services para configurar a entrega de licenças/chaves e criptografar seus ativos (conforme mostrado [nesta amostra](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, consulte:

- [Visão geral de proteção do conteúdo](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Devo usar HTTP ou HTTPS?
O aplicativo de player ASP.NET MVC deve ser compatível com o seguinte:

* Autenticação de usuário por meio do Azure AD que está em HTTPS.
* Troca de JWT entre o cliente e o Azure AD, que está em HTTPS.
* Aquisição de licença de DRM pelo cliente, que deve estar em HTTPS se a entrega de licença é fornecida pelos Serviços de Mídia. O pacote de produtos PlayReady não faz com que HTTPS seja obrigatório para a entrega de licença. Se o servidor de licença do PlayReady está fora dos Serviços de Mídia, você pode usar HTTP ou HTTPS.

O aplicativo de player ASP.NET usa HTTPS como uma melhor prática, portanto o Player de Mídia é uma página em HTTPS. No entanto, http é preferido para streaming, então você precisa considerar esses problemas com conteúdo misto:

* O navegador não permite conteúdo misto. Mas plug-ins como Silverlight e o plug-in OSMF para Smooth e DASH permitem isso. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript malicioso, o que pode colocar os dados do cliente em risco. Os navegadores bloqueiam essa funcionalidade por padrão. A única maneira de contornar isso é no lado do servidor (origem), por meio da permissão de todos os domínios (sejam HTTPS ou HTTP). Isso provavelmente também não será uma boa ideia.
* Evite conteúdo misto. O aplicativo de player e o Player de Mídia devem usar HTTP ou HTTPS. Quando você está jogando conteúdo misto, a tecnologia SilverlightSS requer a compensação de um aviso de conteúdo misto. A tecnologia FlashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto de extremidade de streaming tiver sido criado antes de agosto de 2014, ele não será compatível com HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?

Você pode usar exatamente o mesmo design e implementação para ajudar a proteger a transmissão ao vivo em Serviços de Mídia, tratando o ativo associado a um programa como um ativo VOD. Para fornecer uma proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de associar o ativo à saída ao vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Mídia?

Muitas vezes, os clientes têm investido em uma fazenda de servidores de licença, seja em seu próprio datacenter ou em um hospedado por provedores de serviços DRM. Com a Proteção de Conteúdo dos Serviços de Mídia, você pode operar em modo híbrido. O conteúdo pode ser hospedado e protegido dinamicamente nos Serviços de Mídia, enquanto as licenças DRM são fornecidas por servidores fora dos Serviços de Mídia. Nesse caso, considere as seguintes alterações:

* O STS precisa emitir tokens que sejam aceitáveis e possam ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um JWT específico que contém uma mensagem de autorização. Você precisa ter um STS para emitir tal JWT. 
* Você não precisa mais configurar o serviço de entrega de licença nos Serviços de Mídia. Você precisa fornecer urls de aquisição de licença (para PlayReady, Widevine e FairPlay) quando configurar `ContentKeyPolicy`.

> [!NOTE]
> Widevine é um serviço fornecido pelo Google e sujeito aos termos de serviço e política de privacidade do Google.

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

Atualmente, você pode usar o [portal Azure](https://portal.azure.com/) para:

* Gerenciar [eventos ao vivo](live-events-outputs-concept.md) em serviços de mídia v3. 
* Exibir (não gerenciar) [ativos](assets-concept.md)v3 . 
* [Obtenha informações sobre como acessar APIs](access-api-portal.md). 

Para todas as [outras tarefas](transforms-jobs-concept.md) de gerenciamento (por exemplo, Transformações e Empregos e [proteção de conteúdo),](content-protection-overview.md)use a [API REST,](https://docs.microsoft.com/rest/api/media/)a [CLI do Azure](https://aka.ms/ams-v3-cli-ref)ou uma das [SDKs](media-services-apis-overview.md#sdks)suportadas.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

O `AssetFile` conceito foi removido da API de Serviços de Mídia para separar os Serviços de Mídia da dependência do Storage SDK. Agora, o Azure Storage, não os Serviços de Mídia, mantém as informações que pertencem ao SDK de armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Recomendamos agora que você use criptografia de armazenamento do lado do servidor (que está em prática por padrão). Para obter mais informações, consulte [a criptografia do serviço de armazenamento Do Zure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming para iOS

As perguntas freqüentes a seguir fornecem assistência para a solução de problemas do streaming de FairPlay off-line para iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Por que apenas o áudio toca, mas nenhum vídeo aparece durante o modo offline?

Esse comportamento parece estar relacionado ao design do aplicativo de exemplo. Quando uma faixa de áudio alternativa está presente (que é o caso do HLS) durante o modo offline, tanto o iOS 10 quanto o iOS 11 são padrão para a faixa de áudio alternativa. Para compensar esse comportamento para o modo offline FPS, remova a faixa de áudio alternativa do fluxo. Para fazer isso no Media Services, adicione o filtro manifesto dinâmico **somente áudio=falso**. Em outras palavras, uma URL HLS termina com **.ism/manifest (format=m3u8-aapl,audio-only=false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Por que ela ainda toca áudio somente sem vídeo durante o modo offline após eu adicionar audio-only=false?

Dependendo do design da chave de cache para a rede de entrega de conteúdo, o conteúdo pode ser armazenado em cache. Limpe o cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>O modo offline FPS é suportado no iOS 11 além do iOS 10?

Sim. O modo offline de FPS tem suporte para iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Por que eu não consigo encontrar o documento “Reprodução Offline com o Streaming de FairPlay e HTTP Live Streaming no SDK do Servidor de FPS”?

Desde a versão 4 do SDK do Servidor de FPS, este documento foi mesclado no “Guia de Programação de Streaming de FairPlay”.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>O que é a estrutura de arquivos offline/baixados em dispositivos iOS?

A estrutura do arquivo baixado em um dispositivo iOS tem a aparência da captura de tela abaixo. A pasta `_keys` armazena licenças de FPS baixadas, com um arquivo de armazenamento para cada host de serviço de licença. A pasta `.movpkg` armazena conteúdo de áudio e vídeo. 

A primeira pasta com um nome que termina com um traço seguido de um número contém conteúdo de vídeo. O valor numérico é a largura de banda máxima das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira `Data` pasta nomeada contém a lista de reprodução principal do conteúdo FPS. Por fim, boot.xml fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura de arquivos offline para o aplicativo de exemplo FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Aqui está um arquivo sample boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Streaming widevine para Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Como posso fornecer licenças persistentes (ativadas offline) para alguns clientes/usuários e licenças não persistentes (off-off disabled) para outros? Tenho que duplicar o conteúdo e usar chaves de conteúdo separadas?

Como o Media Services v3 `StreamingLocator` permite que um ativo tenha várias instâncias, você pode ter:

* Um `ContentKeyPolicy` exemplo `license_type = "persistent"` `ContentKeyPolicyRestriction` com , `"persistent"`com `StreamingLocator`reivindicação em , e seu .
* Outro `ContentKeyPolicy` exemplo `license_type="nonpersistent"` `ContentKeyPolicyRestriction` com , `"nonpersistent`com `StreamingLocator`reivindicação em ", e sua .
* Duas `StreamingLocator` instâncias `ContentKey` que têm valores diferentes.

Dependendo da lógica de negócios do STS personalizado, diferentes declarações são emitidas no token JWT. Com o token, apenas a licença correspondente pode ser obtida e apenas a URL correspondente pode ser reproduzida.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual é o mapeamento entre os níveis de segurança do WIDEVine e do Media Services DRM?

A visão geral da arquitetura WIDEvine DRM do Google define três níveis de segurança. No entanto, a documentação do [Azure Media Services no modelo de licença Widevine](widevine-license-template-overview.md) descreve cinco níveis de segurança (requisitos de robustez do cliente para reprodução). Esta seção explica como os níveis de segurança mapeiam.

Ambos os conjuntos de níveis de segurança são definidos pelo Google Widevine. A diferença está no nível de uso: arquitetura ou API. Os cinco níveis de segurança são usados na API do Widevine. O `content_key_specs` objeto, `security_level`que contém, é desserializado e passado para o serviço de entrega global Widevine pelo serviço de licença Widevine da Azure Media Services. A tabela a seguir mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura Widevine** |**Níveis de segurança usados na API Widevine**|
|---|---| 
| **Nível de segurança 1**: Todo o processamento, criptografia e controle de conteúdo são realizados dentro do Ambiente de Execução Confiável (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.|**security_level=5**: A criptografia, a decodificação e todo o manuseio da mídia (compactado e descompactado) devem ser tratados dentro de um TEE apoiado por hardware.<br/><br/>**security_level=4**: A criptografia e a decodificação do conteúdo devem ser realizadas dentro de um TEE apoiado por hardware.|
**Segurança Nível 2**: A criptografia (mas não o processamento de vídeo) é realizada dentro do TEE. Os buffers descriptografados são retornados ao domínio do aplicativo e processados através de hardware ou software de vídeo separados. No Nível 2, no entanto, as informações criptográficas ainda são processadas apenas dentro do TEE.| **security_level=3**: As operações de material-chave e cripto devem ser realizadas dentro de um TEE apoiado por hardware. |
| **Nível de segurança 3:** Não há TEE no dispositivo. Medidas apropriadas podem ser tomadas para proteger as informações criptográficas e conteúdo descriptografado no sistema operacional host. Uma implementação de Nível 3 também pode incluir um mecanismo criptográfico de hardware, mas isso melhora apenas o desempenho, não a segurança. | **security_level=2**: É necessário cripto de software e um decodificador ofuscado.<br/><br/>**security_level=1**: É necessária a criptomoeda de caixa branca baseada em software.|

#### <a name="why-does-content-download-take-so-long"></a>Por que o download de conteúdo leva muito tempo?

Há duas maneiras para melhorar a velocidade do download:

* Habilite uma rede de entrega de conteúdo para que os usuários sejam mais propensos a atingir isso em vez do ponto final de origem/streaming para download de conteúdo. Se um usuário atingir um ponto final de streaming, cada segmento HLS ou fragmento DASH será dinamicamente empacotado e criptografado. Mesmo que essa latência esteja em escala de milissegundos para cada segmento ou fragmento, quando você tem um vídeo de uma hora de duração, a latência acumulada pode ser grande e causar um download mais longo.
* Dê aos usuários a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todos os conteúdos. Para o modo offline, não adianta baixar todas as camadas de qualidade. Há duas maneiras de fazer isso:

  * Controle do cliente: O aplicativo do jogador seleciona automaticamente, ou o usuário seleciona, a camada de qualidade de vídeo e as faixas de áudio a serem baixadas.
  * Controle de serviço: Você pode usar o recurso Manifesto Dinâmico no Azure Media Services para criar um filtro (global), que limita a lista de reprodução HLS ou DASH MPD a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários incluirá este filtro.

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
