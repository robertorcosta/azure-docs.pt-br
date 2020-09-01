---
title: Visão geral dos Serviços de Mídia do Azure | Microsoft Docs
description: Os Serviços de Mídia do Microsoft Azure tratam-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Este artigo fornece uma visão geral dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 0d896b0ce23116f1b87718f492d407ac094b9e6a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89261148"
---
# <a name="azure-media-services-overview"></a>Visão geral dos Serviços de Mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/media-services-overview.md)
> * [Versão 2](media-services-overview.md)

> [!NOTE]
> Não estão sendo adicionados novos recursos aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

O AMS (Serviços de Mídia do Microsoft Azure) trata-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Os serviços de mídia se baseiam em APIs REST que permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Você pode compilar fluxos de trabalho de ponta a ponta usando totalmente os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifique usando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue usando os serviços de mídia. Você pode optar por transmitir seu conteúdo ao vivo ou fornecer conteúdo sob demanda. 


## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como lembrete importante, você deve obedecer a todas as leis aplicáveis em seu uso dos serviços de mídia do Azure e não pode usar os serviços de mídia ou qualquer serviço do Azure de uma maneira que viole os direitos de outras pessoas ou que possam ser prejudiciais a outros.

Antes de carregar qualquer vídeo/imagem nos serviços de mídia, você deve ter todos os direitos adequados para usar o vídeo/imagem, incluindo, quando exigido por lei, todos os condados necessários de indivíduos (se houver) no vídeo/imagem, para uso, processamento e armazenamento de seus dados nos serviços de mídia e no Azure. Algumas jurisdições podem impor requisitos legais especiais para a coleta, o processamento online e o armazenamento de determinadas categorias de dados, como dados biométricos. Antes de usar os serviços de mídia e o Azure para o processamento e o armazenamento de quaisquer dados sujeitos a requisitos legais especiais, você deve garantir a conformidade com quaisquer requisitos legais que possam se aplicar a você.

Para saber mais sobre conformidade, privacidade e segurança nos serviços de mídia, visite a [central de confiabilidade](https://www.microsoft.com/trust-center/?rtc=1)da Microsoft. Para as obrigações de privacidade da Microsoft, a manipulação de dados e as práticas de retenção, incluindo como excluir seus dados, examine a [política de privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os [termos de serviços online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e o [adendo de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Usando os serviços de mídia, você concorda em estar associado ao OST, ao DPA e à declaração de privacidade.
 
## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Media Services, você deve possuir o seguinte:

* Uma conta do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Serviços de Mídia do Azure. Para obter mais informações, veja [Criar conta](media-services-portal-create-account.md).
* (Opcional) Configure o ambiente de desenvolvimento. Escolha .NET ou API REST para seu ambiente de desenvolvimento. Para obter mais informações, veja [Configurar ambiente](media-services-dotnet-how-to-use.md).

    Além disso, saiba como [conectar-se programaticamente à API do AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Um ponto de extremidade de streaming padrão ou premium em estado iniciado.  Para obter mais informações, consulte [Gerenciando pontos de extremidade de streaming](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDKs e ferramentas

Para compilar soluções de serviços de mídia, você pode usar:

* [API REST dos Serviços de Mídia](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Um dos SDKs de cliente disponíveis:
    * SDK dos serviços de mídia do Azure para .NET
    
        * [Pacote NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Código-fonte do GitHub](https://github.com/Azure/azure-sdk-for-media-services)
    * [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java),
    * [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php),
    * [Serviços de Mídia do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (esta é uma versão de um SDK do Node.js que não foi criada pela Microsoft. Ele é mantido por uma comunidade e atualmente não tem cobertura de 100% das APIs do AMS).
* Ferramentas existentes:
    * [Azure portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) ([AMSE] Gerenciador de Serviços de Mídia do Azure é um aplicativo Winforms/C# para Windows)

> [!NOTE]
> Para obter a versão mais recente do SDK do Java e começar a desenvolver com Java, confira [Introdução ao SDK de cliente Java para Serviços de Mídia](./media-services-java-how-to-use.md). <br/>
> Para baixar o SDK mais recente do PHP para os Serviços de Mídia, procure a versão 0.5.7 do pacote do Microsoft/WindowAzure no [Repositório do Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Exemplos de código

Localize vários exemplos de código na galeria **Exemplos de Código do Azure**: [exemplos de código dos Serviços de Mídia do Azure](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Conceitos

Para conferir os conceitos dos Serviços de Mídia do Azure, confira [Conceitos](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Cenários com suporte e disponibilidade dos Serviços de Mídia em data centers

Para obter informações detalhadas, veja [Cenários do AMS e disponibilidade de recursos e serviços nos data centers](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

Para obter mais informações, veja [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Para obter informações sobre a disponibilidade nos datacenters, consulte a seção [Disponibilidade](scenarios-and-availability.md#availability).

## <a name="support"></a>Suporte

[Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte do Azure, incluindo os Serviços de Mídia.

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
