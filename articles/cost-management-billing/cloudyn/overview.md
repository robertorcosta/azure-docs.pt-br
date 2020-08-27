---
title: Visão geral do Cloudyn no Azure
description: O Cloudyn é uma solução de gerenciamento de custos de várias nuvens que ajuda você a usar o Azure e outros recursos de nuvem.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 3acc13ca535808f14cb01d50e38f6bd4d12902fc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684432"
---
# <a name="what-is-the-cloudyn-service"></a>O que é o serviço Cloudyn?

O Cloudyn, uma subsidiária da Microsoft, permite acompanhar o uso da nuvem e as despesas de seus recursos do Azure e de outros provedores de nuvem, incluindo AWS e Google. Os relatórios do painel fáceis de entender ajudam com a alocação de custos e showbacks/estornos também. O Cloudyn ajuda a otimizar seus gastos na nuvem identificando os recursos subutilizados que, posteriormente, você pode gerenciar e ajustar.

Para assistir a um vídeo de Introdução, consulte [Introdução ao Cloudyn do Azure](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/).
 
O Gerenciamento de Custos do Azure oferece uma funcionalidade semelhante ao Cloudyn. O Gerenciamento de Custos do Azure é uma solução de gerenciamento de custos nativa do Azure. Ele ajuda a analisar custos, criar e gerenciar orçamentos, exportar dados e examinar e agir de acordo com as recomendações de otimização que visam economizar dinheiro. Para obter mais informações, consulte [gerenciamento de custos do Azure](../cost-management-billing-overview.md).
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Assista ao [vídeo do Gerenciamento de Custos do Azure e Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) para ver recomendações sobre quando você deve usar o Gerenciamento de Custos do Azure ou o Cloudyn com base em suas necessidades de negócios.
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

## <a name="monitor-usage-and-spending"></a>Monitorar o uso e os gastos

O monitoramento do uso e dos gastos é extremamente importante para infraestruturas de nuvem, porque as organizações pagam pelos recursos consumidos ao longo do tempo. Quando o uso excede os limites do contrato, podem ocorrer excedentes de custo inesperados rapidamente. Alguns fatores importantes podem dificultar o monitoramento ad hoc. Primeiro, a projeção dos custos de acordo com o uso médio presume que o consumo permaneça consistente durante determinado período de cobrança. Em segundo lugar, quando os custos estão prestes a exceder ou excedem o orçamento, é importante obter notificações de forma proativa para ajustar os gastos. Além disso, os provedores de serviços de nuvem podem não oferecer a projeção dos custos vs. limites ou relatórios de comparação de período a período.

Os relatórios ajudam a monitorar os gastos para analisar e acompanhar o uso na nuvem, os custos e as tendências. Usando relatórios ao Longo do Tempo, você pode detectar anomalias que diferem das tendências normais. As ineficiências na implantação de nuvem são visíveis nos relatórios de otimização. Você também pode observar as ineficiências em relatórios de análise de custo.

## <a name="manage-costs"></a>Gerenciar os custos

Os dados históricos podem ajudar a gerenciar os custos ao analisar o uso e os custos ao longo do tempo para identificar tendências. As tendências são então usadas para prever gastos futuros. O Cloudyn também inclui relatórios de custos previstos úteis.

A alocação de custos gerencia os custos analisando os custos de acordo com a política de marcação. Use marcações em suas contas personalizadas, recursos e entidades para refinar a alocação de custos. O Gerenciador de Categorias organiza as marcações para ajudar a fornecer mais governança. Além disso, use a alocação de custos para showback/estorno para mostrar a utilização de recursos e os custos associados para influenciar os comportamentos de consumo ou cobrar os clientes do locatário.

O controle de acesso ajuda a gerenciar os custos garantindo que os usuários e as equipes acessem somente os dados de gerenciamento de custo necessários. Use a estrutura de entidades, o gerenciamento de usuários e os relatórios agendados com listas de destinatários para atribuir o acesso.

Os alertas ajudam a gerenciar os custos notificando-o automaticamente quando ocorrem gastos incomuns ou excesso de gastos. Os alertas podem notificar outros stakeholders automaticamente sobre anomalias de gastos e riscos de excesso de gastos. Vários relatórios dão suporte a alertas com base no orçamento e nos limites de custo. No entanto, atualmente não há suporte para alertas em contas de parceiro ou assinaturas do CSP.

## <a name="improve-efficiency"></a>Melhorar a eficiência

Determine o uso ideal de VM e identifique as VMs ociosas ou remova VMs ociosas e os discos desanexados com o Cloudyn. Usando as informações contidas nos relatórios de Ineficiência e Otimização de Dimensionamento, você pode criar um plano para reduzir ou remover as VMs ociosas. No entanto, atualmente não há suporte para relatórios de otimização em contas de parceiro ou assinaturas do CSP.

Se você provisionou instâncias Reservadas do AWS, melhore a utilização das instâncias reservadas com os relatórios de Otimização, em que é possível exibir as recomendações de compras, modificar reservas não utilizadas e planejar o provisionamento.


## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o Cloudyn, a próxima etapa é registrar seu ambiente de nuvem e começar a explorar seus dados.

- [Registrar-se mo programa de Parceiro CSP e exibir dados de custo](quick-register-csp.md)
