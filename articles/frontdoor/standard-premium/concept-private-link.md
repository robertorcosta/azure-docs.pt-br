---
title: Proteja sua origem com o link privado no Azure front door Standard/Premium (visualização)
description: Esta página fornece informações sobre como proteger a conectividade com sua origem usando o link privado.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: dead60b9d8e0872f3d46b1f223ccf5e6697cbd90
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098512"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Proteja sua origem com o link privado no Azure front door Standard/Premium (visualização)

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Exibir os [documentos de porta frontal do Azure](../front-door-overview.md).

## <a name="overview"></a>Visão geral

O [link privado do Azure](../../private-link/private-link-overview.md) permite acessar os serviços de PaaS do Azure e os serviços hospedados do Azure por meio de um ponto de extremidade privado em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O SKU Premium da porta do Azure pode se conectar à sua origem usando o serviço de link privado. Seus aplicativos podem ser hospedados em sua rede virtual privada ou por trás de um serviço PaaS, não acessível pela Internet pública.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Arquitetura de pontos de extremidade privados da porta frontal":::

Quando você habilita o link privado para sua origem na configuração Premium da porta frontal do Azure, a porta frontal cria um ponto de extremidade privado em seu nome na rede privada regional da porta da frente. Esse ponto de extremidade é gerenciado pela porta frontal do Azure. Você receberá uma solicitação de ponto de extremidade particular da porta do Azure para a mensagem de aprovação em sua origem. Depois de aprovar a solicitação, um endereço IP privado é atribuído da rede virtual da porta frontal, o tráfego entre a porta frontal do Azure e sua origem atravessa o link privado estabelecido com o backbone de rede do Azure. O tráfego de entrada para sua origem agora é protegido quando vindo da sua porta de frente do Azure.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Habilitar ponto de extremidade privado":::

O Azure front door Premium dá suporte a vários tipos de origem. Se sua origem estiver hospedada em um conjunto de máquinas virtuais em sua rede privada, primeiro você precisará criar um balanceador de carga padrão interno, habilitar o serviço de link privado para o balanceador de carga padrão e, em seguida, selecionar tipo de origem personalizada. Para configuração de link privado, selecione "Microsoft. Network/PrivateLinkServices como tipo de recurso. Para serviços de PaaS, como o aplicativo Web do Azure e a conta de armazenamento, você pode habilitar o serviço de vínculo privado a partir dos serviços correspondentes primeiro e selecionar Microsoft. Web/sites para o aplicativo Web e Microsoft. Storage/StorageAccounts para tipos de serviço de link privado da conta de armazenamento.

## <a name="limitations"></a>Limitações

Os pontos de extremidade particulares da porta de recepção do Azure estão disponíveis nas seguintes regiões durante a visualização pública: leste dos EUA, oeste dos EUA e EUA Central do Sul.

Para obter a melhor latência, você deve sempre escolher uma região do Azure mais próxima da sua origem ao escolher habilitar o ponto de extremidade do link privado da porta frontal.

Os pontos de extremidade privados da porta de recepção do Azure são gerenciados pela plataforma e sob a assinatura da porta frontal do Azure. A porta frontal do Azure permite conexões de link privado com a mesma assinatura de cliente que é usada para criar o perfil de porta frontal.

## <a name="next-steps"></a>Próximas etapas

* Para conectar o Azure front door Premium a máquinas virtuais usando o serviço de vínculo privado, consulte [criar um ponto de extremidade privado](../../private-link/create-private-endpoint-portal.md).
* Para conectar o Azure front door Premium ao seu aplicativo Web por meio do serviço de vínculo privado, consulte [conectar-se a um aplicativo Web usando um ponto de extremidade privado](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Para conectar o Azure front door Premium à sua conta de armazenamento por meio do serviço de vínculo privado, consulte [conectar-se a uma conta de armazenamento usando um ponto de extremidade privado](../../private-link/tutorial-private-endpoint-storage-portal.md).
