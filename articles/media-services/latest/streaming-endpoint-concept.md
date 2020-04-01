---
title: Pontos finais de streaming (Origem)
titleSuffix: Azure Media Services
description: Saiba mais sobre o Streaming Endpoints (Origin), um serviço dinâmico de embalagem e streaming que fornece conteúdo diretamente para um aplicativo de player cliente ou para uma Rede de Entrega de Conteúdo (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: a8674714451f3cc686acb74d6c66ac6800b75936
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478088"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming Endpoints (Origem) nos Serviços de Mídia do Azure

No Microsoft Azure Media Services, um [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) representa um serviço dinâmico (just-in-time) de embalagem e origem que pode fornecer seu conteúdo ao vivo e sob demanda diretamente para um aplicativo de player cliente usando um dos protocolos comuns de mídia de streaming (HLS ou DASH). Além disso, o **Streaming Endpoint** fornece criptografia dinâmica (just-in-time) para DRMs líderes do setor. 

Quando você cria uma conta de Serviços de Mídia, um Ponto de Extremidade de Streaming **padrão** é criado em um estado parado. Você não pode excluir o ponto final de streaming **padrão.** Mais pontos finais de streaming podem ser criados na conta (ver [Cotas e limitações](limits-quotas-constraints.md)).

> [!NOTE]
> Para começar a transmitir vídeos, é necessário iniciar o **Ponto de extremidade de streaming** do qual deseja transmitir o vídeo.
>
> Você só é cobrado quando o seu Streaming Endpoint está em estado de execução.

Certifique-se também de revisar o tópico [de embalagem Dinâmica.](dynamic-packaging-overview.md) 

## <a name="naming-convention"></a>Convenção de nomenclatura

O formato do nome do `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`host `servicename` da URL de streaming é: , onde = o nome do ponto final de streaming ou o nome do evento ao vivo.

Ao usar o ponto `servicename` final de streaming padrão, `{accountname}-{regionname}.streaming.azure.net`é omitido para que a URL seja: .

### <a name="limitations"></a>Limitações

* O nome de ponto final de streaming tem um valor máximo de 24 caracteres.
* O nome deve seguir este `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`padrão [regex:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

## <a name="types"></a>Tipos

Existem dois tipos **de streaming endpoint:** **Standard** (preview) e **Premium**. O tipo é definido pelo número de unidades de escala (`scaleUnits`) alocadas para o ponto de extremidade de streaming.

A tabela descreve os tipos:

|Type|Unidades de escala|Descrição|
|--------|--------|--------|  
|**Standard**|0|O ponto final de streaming padrão é um tipo **Padrão** — `scaleUnits`ele pode ser alterado para o tipo Premium ajustando .|
|**Premium**|>0|**Premium** Os pontos finais de streaming são adequados para cargas de trabalho avançadas e fornecem capacidade de largura de banda dedicada e escalável. Você passa **Premium** para um tipo `scaleUnits` Premium ajustando (unidades de streaming). `scaleUnits` fornece capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Ao usar o tipo **Premium,** cada unidade habilitada fornece capacidade de largura de banda adicional ao aplicativo. |

> [!NOTE]
> Para clientes que procuram fornecer conteúdo para grandes públicos da Internet, recomendamos que você habilite o CDN no Streaming Endpoint.

Para obter informações sobre SLA, consulte [Preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

Recurso|Standard|Premium
---|---|---
Produtividade |Até 600 Mbps e pode fornecer um throughput muito maior e eficaz quando um CDN é usado.|200 Mbps por UA (unidade de streaming). Pode fornecer um throughput muito maior e eficaz quando um CDN é usado.
CDN|Azure CDN, CDN de terceiros, ou sem CDN.|Azure CDN, CDN de terceiros, ou sem CDN.
A cobrança é rateada| Diário|Diário
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|SUS adicional
Filtragem ip/G20/Host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> Usado diretamente no ponto final de streaming quando o CDN não está habilitado no ponto final.<br/>

## <a name="streaming-endpoint-properties"></a>Propriedades do Ponto Final de Streaming

Esta seção fornece detalhes sobre algumas das propriedades do Streaming Endpoint. Para exemplos de como criar um novo ponto de extremidade de streaming e descrições de todas as propriedades, consulte [Ponto de Extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: Usado para configurar as seguintes configurações de segurança para este ponto final de streaming: chaves de autenticação de cabeçalho de assinatura Akamai e endereços IP que podem se conectar a este ponto final. Esta propriedade só pode `cdnEnabled` ser definida quando for definida como falsa.

- `cdnEnabled`: Indica se a integração CDN do Azure para este ponto final de streaming está habilitada (desativada por padrão). Se você definir `cdnEnabled` como verdadeiro, as seguintes configurações serão desabilitadas: `customHostNames` e `accessControl`.

    Nem todos os data centers dão suporte para integração da CDN do Azure. Para verificar se o data center tem a integração CDN do Azure disponível, faça as seguintes etapas:

  - Tente definir a `cdnEnabled` como true.
  - Verifique o resultado retornado para um `HTTP Error Code 412` (PreconditionFailed) com uma mensagem de "Streaming endpoint CdnEnabled property não pode ser definido como verdadeiro, pois o recurso de CDN não está disponível na região atual."

    Se você tiver esse erro, o data center não o suporta. Tente outro data center.

- `cdnProfile`: `cdnEnabled` Quando é definido como verdadeiro, você também pode passar `cdnProfile` valores. `cdnProfile` é o nome do perfil CDN no qual o ponto de extremidade CDN será criado. Você pode fornecer um cdnProfile existente ou usar um novo. Se o valor for NULL e `cdnEnabled` for verdadeiro, o valor padrão "AzureMediaStreamingPlatformCdnProfile" será usado. Se o perfil `cdnProfile` fornecido já existir, um ponto de extremidade será criado sob ele. Se o perfil não existir, um novo perfil será criado automaticamente.
- `cdnProvider`: Quando o CDN está ativado, você também pode passar `cdnProvider` valores. `cdnProvider` controla qual provedor será usado. Atualmente, há suporte para três valores: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se nenhum valor for `cdnEnabled` fornecido e for verdadeiro, "StandardVerizon" será usado (esse é o valor padrão).
- `crossSiteAccessPolicies`: Usado para especificar políticas de acesso entre sites para vários clientes. Para obter mais informações, consulte [Especificação de arquivo de política entre domínios](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e [Disponibilizando um serviço entre limites de domínios](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). As configurações só se aplicam ao Smooth Streaming.
- `customHostNames`: Usado para configurar um ponto final de streaming para aceitar tráfego direcionado a um nome de host personalizado. Esta propriedade é válida para pontos finais de `cdnEnabled`streaming padrão e premium e pode ser definida quando : falso.

    A propriedade do nome de domínio deve ser confirmada pelos Serviços de Mídia. O Media Services verifica a propriedade `CName` do nome de domínio exigindo um registro contendo o ID da conta do Serviço de Mídia como um componente a ser adicionado ao domínio em uso. Por exemplo, para "sports.contoso.com" ser usado como um nome do host personalizado para o ponto de extremidade de streaming, um registro para `<accountId>.contoso.com` deve ser configurado para apontar para um dos nomes do host de verificação dos Serviços de Mídia. O nome do host de verificação é composto por verifydns.\<mediaservices-dns-zone>.

    A seguir, as zonas DNS esperadas devem ser usadas no registro de verificação para diferentes regiões do Azure.
  
  - América do Norte, Europa, Cingapura, Hong Kong SAR, Japão:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Por exemplo, `CName` um registro que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.media.azure.net" prova que o ID de Serviços de Mídia 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem a propriedade do domínio contoso.com, permitindo assim que qualquer nome sob contoso.com seja usado como um nome de host personalizado para um ponto final de streaming sob essa conta. Para localizar o valor da ID do Serviço de Mídia, acesse o [portal do Azure](https://portal.azure.com/) e selecione a conta de Serviço de Mídia. O **ID** da conta aparece no canto superior direito da página.

    Se houver uma tentativa de definir um nome de `CName` host personalizado sem uma verificação adequada do registro, a resposta do DNS falhará e será armazenada em cache por algum tempo. Depois que um registro adequado estiver em vigor, poderá demorar um pouco até que a resposta armazenada em cache seja revalidada. Dependendo do provedor de DNS para o domínio personalizado, leva de alguns minutos a uma hora para revalidar o registro.

    `CName` Além dos mapas `<accountId>.<parent domain>` para `verifydns.<mediaservices-dns-zone>`, você `CName` deve criar outro que mapeie o nome do host personalizado (por exemplo, `sports.contoso.com`) para o nome de host do seu Media Services Streaming Endpoint (por exemplo, `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Os pontos finais de streaming localizados no mesmo data center não podem compartilhar o mesmo nome de host personalizado.

    Atualmente, o Media Services não suporta TLS com domínios personalizados.

- `maxCacheAge`- Substitui o cabeçalho de controle de cache HTTP padrão da idade máxima definido pelo ponto final de streaming em fragmentos de mídia e manifestos sob demanda. O valor é definido em segundos.
- `resourceState` -

    - Parado: o estado inicial de um Endpoint de streaming após a criação
    - Início: está em transição para o estado de execução
    - Em execução: é capaz de transmitir conteúdo para os clientes
    - Dimensionamento: as unidades de escala estão sendo aumentadas ou diminuídas
    - Parando: está em transição para o estado parado
    - Excluindo: está sendo excluído

- `scaleUnits`: Fornecer-lhe uma capacidade de saída dedicada que pode ser adquirida em incrementos de 200 Mbps. Se você precisar mudar para um tipo **Premium**, ajuste `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Por que usar vários pontos finais de streaming?

Um único ponto final de streaming pode transmitir vídeos ao vivo e sob demanda e a maioria dos clientes usa apenas um ponto final de streaming. Esta seção dá alguns exemplos de por que você pode precisar usar vários pontos finais de streaming.

* Cada unidade reservada permite 200 Mbps de largura de banda. Se você precisar de mais de 2.000 Mbps (2 Gbps) de largura de banda, você pode usar o segundo ponto final de streaming e o saldo de carga para lhe dar largura de banda adicional.

    No entanto, o CDN é a melhor maneira de obter escala para streaming de conteúdo, mas se você estiver fornecendo tanto conteúdo que o CDN está puxando mais de 2 Gbps, então você pode adicionar pontos finais adicionais de streaming (origens). Neste caso, você precisaria distribuir URLs de conteúdo que são equilibrados entre os dois pontos finais de streaming. Essa abordagem dá melhor cache do que tentar enviar solicitações para cada origem aleatoriamente (por exemplo, através de um gerenciador de tráfego). 
    
    > [!TIP]
    > Normalmente, se o CDN está puxando mais de 2 Gbps, então algo pode estar mal configurado (por exemplo, sem blindagem de origem).
    
* Balanceamento de carga diferentes provedores de CDN. Por exemplo, você pode configurar o ponto final de streaming padrão para usar o CDN da Verizon e criar um segundo para usar o Akamai. Em seguida, adicione um pouco de balanceamento de carga entre os dois para obter o balanceamento multi-CDN. 

    No entanto, muitas vezes o cliente faz balanceamento de carga entre vários provedores de CDN usando uma única origem.
* Streaming de conteúdo misto: Ao vivo e vídeo sob demanda. 

    Os padrões de acesso para conteúdo ao vivo e sob demanda são muito diferentes. O conteúdo ao vivo tende a receber muita demanda pelo mesmo conteúdo de uma só vez. O conteúdo de vídeo sob demanda (conteúdo de arquivamento de cauda longa, por exemplo) tem baixo uso no mesmo conteúdo. Assim, o cache funciona muito bem no conteúdo ao vivo, mas não tão bem no conteúdo da cauda longa.

    Considere um cenário em que seus clientes estão assistindo principalmente conteúdo ao vivo, mas só ocasionalmente estão assistindo conteúdo sob demanda e ele é servido a partir do mesmo Streaming Endpoint. O baixo uso de conteúdo sob demanda ocuparia espaço de cache que seria melhor salvo para o conteúdo ao vivo. Neste cenário, recomendamos servir o conteúdo ao vivo de um Streaming Endpoint e o conteúdo de cauda longa de outro Streaming Endpoint. Isso melhorará o desempenho do conteúdo do evento ao vivo.
    
## <a name="scaling-streaming-with-cdn"></a>Dimensionamento de streaming com CDN

Veja os artigos a seguir:

- [Visão geral do CDN](../../cdn/cdn-overview.md)
- [Dimensionamento de streaming com CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Faça perguntas e receba atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

[Empacotamento dinâmico](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar pontos de extremidade de streaming](manage-streaming-endpoints-howto.md)
