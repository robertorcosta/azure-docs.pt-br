---
title: Perguntas frequentes sobre os Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure v3.
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
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886816"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Serviços de mídia v3 perguntas freqüentes

Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure (AMS) v3.

## <a name="general"></a>Geral

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem executar ações nos recursos do Azure Media Services? 

Consulte [o RBAC (Role-based Access Control, controle de acesso baseado em função) para contas de Serviços de Mídia](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>como fazer streaming para dispositivos iOS da Apple?

Certifique-se de ter "(format=m3u8-aapl)" no final do seu caminho (após a parte "/manifesto" da URL) para dizer ao servidor de origem de streaming para retornar o conteúdo HLS para consumo em dispositivos nativos do Apple iOS (para detalhes, consulte [a entrega de conteúdo](dynamic-packaging-overview.md)).

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer para configurar Unidades Reservadas para Mídia?

Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real aos Serviços de Mídia para aplicar o **Transform** a um determinado conteúdo de entrada de vídeo ou áudio. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>carreguei, codifiquei e publiquei um vídeo. Qual seria o motivo pelo qual o vídeo não é reproduzido quando tento transmiti-lo?

um dos motivos mais comuns é não ter o ponto de extremidade de streaming do qual você está tentando reproduzir no estado de Execução.

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Ao usar a paginação, você sempre deve usar o próximo link para enumerar a coleção e não depender de um tamanho de página em particular. Para obter detalhes e exemplos, confira [Filtragem, ordenação, paginação](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais recursos ainda não estão disponíveis no Azure Media Services v3?

Para obter [detalhes, consulte as lacunas de recursos em relação às APIs v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de movimentação de uma conta de Serviços de Mídia entre assinaturas?  

Para obter detalhes, consulte [Mover uma conta de Serviços de Mídia entre assinaturas](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão ao vivo 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Como parar a transmissão ao vivo depois que a transmissão é feita?

Você pode abordá-lo do lado do cliente ou do lado do servidor.

#### <a name="client-side"></a>Lado do cliente

Seu aplicativo web deve solicitar ao usuário se ele quiser encerrar a transmissão se estiver fechando o navegador. Este é um evento de navegador que seu aplicativo web pode lidar.

#### <a name="server-side"></a>Lado do Servidor

Você pode monitorar eventos ao vivo assinando eventos da Event Grid. Para obter mais informações, consulte o esquema de [eventos eventgrid](media-services-event-schemas.md#live-event-types).

* Você pode [assinar](reacting-to-media-services-events.md) o nível de transmissão [Microsoft.Media.LiveEventEncoderDesconectado](media-services-event-schemas.md#liveeventencoderdisconnected) e monitorar que nenhuma reconexão entra por um tempo para parar e excluir seu evento ao vivo.
* Ou, você pode [se inscrever](reacting-to-media-services-events.md) para os eventos [de batimentos cardíacos](media-services-event-schemas.md#liveeventingestheartbeat) de nível de pista. Se todas as faixas tiverem taxa de bits de entrada caindo para 0; ou o último carimbo de tempo não está mais aumentando, então você também pode encerrar o evento ao vivo com segurança. Os eventos de batimentos cardíacos chegam a cada 20 segundos para cada faixa, então pode ser um pouco verboso.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e imagens fixas durante a transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo o Telestream Wirecast, o Switcher Studio (no iOS), o OBS Studio (aplicativo gratuito) e muito mais.

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar uma criptografia de chave clara AES-128 ou um sistema DRM?

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente através do TLS para que a chave seja criptografada em trânsito, mas sem qualquer criptografia adicional ("no claro"). Como resultado, a chave usada para descriptografar o conteúdo é acessível ao player cliente e pode ser visualizada em um rastreamento de rede no cliente em texto simples. Uma criptografia de chave clara AES-128 é adequada para casos de uso em casos em que o espectador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos dentro de uma empresa para serem visualizados pelos funcionários).

Sistemas DRM como PlayReady, Widevine e FairPlay fornecem um nível adicional de criptografia na chave usada para descriptografar o conteúdo em comparação com uma chave clara AES-128. A chave de conteúdo é criptografada para uma chave protegida pelo tempo de execução do DRM em adicional a qualquer criptografia de nível de transporte fornecida pelo TLS. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. O DRM é recomendado para casos de uso em que o visualizador pode não ser uma parte confiável e você exige o mais alto nível de segurança.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como mostrar um vídeo apenas para usuários que têm uma permissão específica, sem usar o Azure AD?

Você não precisa usar nenhum provedor de token específico (como o Azure AD). Você pode criar seu próprio provedor [JWT](https://jwt.io/) (o chamado STS, Secure Token Service), usando criptografia de chave assimétrica. Em seu STS personalizado, você pode adicionar reivindicações com base na sua lógica de negócios.

Certifique-se de que o emissor, a audiência e as reivindicações correspondam exatamente entre o que está no JWT e o ContentKeyPolicyRestriction usado no ContentKeyPolicy.

Para obter mais informações, consulte [Proteger seu conteúdo usando criptografia dinâmica do Media Services](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter o token do JWT antes de usá-lo para a licença de solicitação ou a chave?

1. Para a produção, você precisa ter um Secure Token Services (STS) (serviço web) que emite o token JWT após uma solicitação HTTPS. Para teste, você pode usar o código mostrado no método **GetTokenAsync** definido no [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player será necessário fazer uma solicitação, depois que um usuário é autenticado para o STS para esse token e atribuí-lo como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Para um exemplo de execução de STS, com chave simétrica e assimétrica, consulte [https://aka.ms/jwt](https://aka.ms/jwt). 
* Para um exemplo de um player baseado no Azure Media [https://aka.ms/amtest](https://aka.ms/amtest) Player usando esse token JWT, consulte (expanda o link "player_settings" para ver a entrada do token).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como você autorizar solicitações para transmitir vídeos com a criptografia AES?

A abordagem correta é aproveitar o STS (serviço de Token seguro):

No STS, dependendo do perfil do usuário, adicione declarações diferentes (como "Usuário Premium", "Usuário Básico", "Usuário de Avaliação Gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. É claro que, para conteúdo diferente/ativo, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Use as APIs do Azure Media Services para configurar a entrega de licenças/chaves e criptografar seus ativos (conforme mostrado [nesta amostra](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, consulte:

- [Visão geral de proteção do conteúdo](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP ou HTTPS?
O aplicativo de player ASP.NET MVC deve ser compatível com o seguinte:

* Autenticação de usuário por meio do Azure AD que está em HTTPS.
* Troca de JWT entre o cliente e o Azure AD, que está em HTTPS.
* Aquisição de licença de DRM pelo cliente, que deve estar em HTTPS se a entrega de licença é fornecida pelos Serviços de Mídia. O pacote de produtos PlayReady não faz com que HTTPS seja obrigatório para a entrega de licença. Se o servidor de licença do PlayReady está fora dos Serviços de Mídia, você pode usar HTTP ou HTTPS.

O aplicativo de player ASP.NET usa HTTPS como uma melhor prática, portanto o Player de Mídia é uma página em HTTPS. No entanto, o HTTP é preferencial para streaming, então é necessário considerar o problema do conteúdo misto.

* O navegador não permite conteúdo misto. Mas plug-ins como os do Silverlight e OSMF, para smooth e DASH, permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça potencial de injeção de JavaScript mal-intencionado, o que pode fazer com que os dados do cliente fiquem em risco. Os navegadores bloqueiam essa funcionalidade por padrão. A única maneira de contornar isso é no lado do servidor (origem), por meio da permissão de todos os domínios (sejam HTTPS ou HTTP). Isso provavelmente também não será uma boa ideia.
* Evite conteúdo misto. O aplicativo de player e o Player de Mídia devem usar HTTP ou HTTPS. Durante a reprodução de conteúdo misto, a tecnologia silverlightSS exige a desmarcação de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto de extremidade de streaming tiver sido criado antes de agosto de 2014, ele não será compatível com HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?

Você pode usar exatamente o mesmo design e a mesma implementação para proteger a transmissão ao vivo nos Serviços de Mídia, tratando o ativo associado a um programa como um ativo de VoD. Para fornecer uma proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao Ativo como se fosse um ativo VOD antes de associar o Ativo à Saída Ao Vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Mídia?

Muitas vezes os clientes investiram em um farm de servidores de licença em seu próprio data center ou hospedado por provedores de serviço de DRM. Com a Proteção de Conteúdo dos Serviços de Mídia, você pode operar em modo híbrido. O conteúdo pode ser hospedado e protegido dinamicamente nos Serviços de Mídia, enquanto as licenças de DRM são fornecidas pelos servidores fora dos Serviços de Mídia. Nesse caso, considere as seguintes alterações:

* O STS precisa emitir tokens que sejam aceitáveis e possam ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um JWT específico que contém uma mensagem de autorização. Portanto, você precisa ter um STS para emitir esse JWT. 
* Você não precisa mais configurar o serviço de entrega de licença nos Serviços de Mídia. Você precisa fornecer URLs de aquisição de licença (para o PlayReady, o Widevine e o FairPlay) ao configurar o ContentKeyPolicies.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 comparado com v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

Atualmente, você pode usar o [portal Azure](https://portal.azure.com/) para:

* gerenciar Serviços de Mídia v3 [Eventos ao Vivo,](live-events-outputs-concept.md) 
* exibir (não gerenciar) [ativos](assets-concept.md)v3, 
* [obter informações sobre como acessar APIs](access-api-portal.md). 

Para todas as [outras tarefas](transforms-jobs-concept.md) de gerenciamento (por exemplo, Transformações e Empregos e [Proteção de Conteúdo),](content-protection-overview.md)use a [API REST,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)ou uma das [SDKs](media-services-apis-overview.md#sdks)suportadas.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

Os AssetFiles foram removidos da API do AMS para separar os Serviços de Mídia das dependências do SDK do Armazenamento. Agora o Armazenamento, não os Serviços de Mídia, mantém as informações que pertencem ao Armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Agora é recomendável usar a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming para iOS

As seguintes perguntas freqüentes fornecem assistência para a solução de problemas do streaming de FairPlay off-line para iOS:

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Por que apenas o áudio toca, mas nenhum vídeo aparece durante o modo offline?

Esse comportamento parece estar relacionado ao design do aplicativo de exemplo. Quando uma faixa de áudio alternativa está presente (que é o caso do HLS) durante o modo offline, tanto o iOS 10 quanto o iOS 11 são padrão para a faixa de áudio alternativa. Para compensar esse comportamento para o modo offline FPS, remova a faixa de áudio alternativa do fluxo. Para fazer isso nos Serviços de Mídia, adicione o filtro de manifestação dinâmica “audio-only=false”. Em outras palavras, uma URL de HLS termina com .ism/manifest(format=m3u8-aapl,audio-only=false). 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Por que ela ainda toca áudio somente sem vídeo durante o modo offline após eu adicionar audio-only=false?

Dependendo do design da chave do cache de rede de distribuição de conteúdo (CDN), o conteúdo pode ser armazenado em cache. Limpe o cache.

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>O modo de FPS offline também tem suporte em iOS 11 além do iOS 10?

Sim. O modo offline de FPS tem suporte para iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Por que eu não consigo encontrar o documento “Reprodução Offline com o Streaming de FairPlay e HTTP Live Streaming no SDK do Servidor de FPS”?

Desde a versão 4 do SDK do Servidor de FPS, este documento foi mesclado no “Guia de Programação de Streaming de FairPlay”.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>O que é a estrutura de arquivos offline/baixados em dispositivos iOS?

A estrutura do arquivo baixado em um dispositivo iOS tem a aparência da captura de tela abaixo. A pasta `_keys` armazena licenças de FPS baixadas, com um arquivo de armazenamento para cada host de serviço de licença. A pasta `.movpkg` armazena conteúdo de áudio e vídeo. A primeira pasta com o nome que termina com um traço seguido por um numero contém o conteúdo de vídeo. O valor numérico é o PeakBandwidth das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira pasta denominada "Dados" contém a lista de reprodução mestre do conteúdo de FPS. Por fim, boot.xml fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura do arquivo de aplicativo iOS de Exemplo do FairPlay Offline](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Um arquivo de exemplo boot.xml:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>Como fornecer licenças persistentes (habilitadas offline) para alguns clientes/usuários e licenças não persistentes (desabilitadas offline) para outros? É necessário duplicar o conteúdo e usar uma chave de conteúdo separada?

Uma vez que os Serviços de Mídia v3 permitem que um Ativo tenha vários StreamingLocators. Você pode ter

* Uma ContentKeyPolicy com o license_type = "persistent", ContentKeyPolicyRestriction com a declaração em "persistente", e seu StreamingLocator;
* Outra ContentKeyPolicy com o license_type="nonpersistent", ContentKeyPolicyRestriction com a declaração em "nonpersistent", e seu StreamingLocator.
* Os dois StreamingLocators têm uma ContentKey diferente.

Dependendo da lógica de negócios do STS personalizado, diferentes declarações são emitidas no token JWT. Com o token, apenas a licença correspondente pode ser obtida e apenas a URL correspondente pode ser reproduzida.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual é o mapeamento entre os níveis de segurança do WIDEVine e do Media Services DRM?

A visão geral da arquitetura WIDEvine DRM do Google define três níveis de segurança diferentes. No entanto, na [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](widevine-license-template-overview.md), são descritos cinco níveis de segurança diferentes. Esta seção explica como os níveis de segurança mapeiam.

O doc "Widevine DRM Architecture Review" do Google define os três níveis de segurança a seguir:

* Nível de Segurança 1: todo o processamento, criptografia e controle de conteúdo é realizado no TEE (Ambiente de Execução Confiável). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.
* Nível de Segurança 2: faz a criptografia (mas não o processamento de vídeo) no TEE: os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.
* Nível de Segurança 3: não tem um TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança.

Ao mesmo tempo, na [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](widevine-license-template-overview.md), a propriedade security_level de content_key_specs pode ter os cinco seguintes valores diferentes (requisitos de robustez do cliente para reprodução):

* A criptomoeda baseada em software é necessária.
* A criptografia de software e um decodificador oculto são obrigatórios.
* As operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware.
* A criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.
* A criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.

Ambos os níveis de segurança são definidos pelo Google Widevine. A diferença está em seu nível de uso: nível da arquitetura ou nível da API. Os cinco níveis de segurança são usados na API do Widevine. O objeto content_key_specs, que contém security_level, é desserializado e passado para o serviço de entrega global do Widevine pelo serviço de licença do Widevine nos Serviços de Mídia do Azure. A tabela abaixo mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura do Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de segurança 1**: Todo o processamento, criptografia e controle de conteúdo são realizados dentro do Ambiente de Execução Confiável (TEE). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.|**security_level=5**: a criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.<br/><br/>**security_level=4**: a criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.|
**Nível de Segurança 2**: faz a criptografia (mas não o processamento de vídeo) no TEE: os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.| **security_level=3**: as operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware. |
| **Nível de Segurança 3**: não tem um TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança. | **security_level=2**: É necessário cripto de software e um decodificador ofuscado.<br/><br/>**security_level=1**: É necessária a criptomoeda de caixa branca baseada em software.|

#### <a name="why-does-content-download-take-so-long"></a>Por que o download de conteúdo leva muito tempo?

Há duas maneiras para melhorar a velocidade do download:

* Habilitar a CDN, de modo que seja mais provável que os usuários finais acessem a CDN, em vez da origem e/ou do ponto de extremidade de streaming para o download de conteúdo. Se o usuário acessar o ponto de extremidade de streaming, cada segmento de HLS ou fragmento de DASH será empacotado e criptografado dinamicamente. Embora essa latência esteja na escala de milissegundos para cada segmento/fragmento, quando você tem um vídeo com duração de uma hora, a latência acumulada pode ser grande, levando a um download mais longo.
* Fornecer aos usuários finais a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio, em vez de todo o conteúdo. Para o modo offline, não faz sentido baixar todas as camadas de qualidade. Há duas maneiras de fazer isso:

   * Controlado pelo cliente: o aplicativo de player seleciona automaticamente ou o usuário seleciona a camada de qualidade de vídeo e as faixas de áudio a serem baixadas;
   * Controlado pelo serviço: é possível usar o recurso de Manifesto Dinâmico nos Serviços de Mídia do Azure para criar um filtro (global), que limita a playlist de HLS ou o DASH MPD a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários finais incluirá esse filtro.

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
