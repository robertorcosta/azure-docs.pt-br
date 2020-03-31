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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: 11123ee04dd02a60dff0b88e2e6e85fcd613a7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068005"
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

Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real aos Serviços de Mídia para aplicar o **Transform** a um determinado conteúdo de entrada de vídeo ou áudio. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Empregos](transforms-jobs-concept.md).

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

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
