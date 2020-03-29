---
title: Personalizar avaliações para avaliação do servidor do Azure Migrate | Microsoft Docs
description: Descreve como personalizar avaliações criadas com a avaliação do servidor do Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234282"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar avaliações criadas pelo Azure Migrate Server Assessment.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de aplicativos e cargas de trabalho locais, bem como VMs da nuvem pública/privada para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

Você pode usar a ferramenta Azure Migrate Server Assessment para criar avaliações para VMs VMware e VMs Hyper-V no local, em preparação para migração para o Azure.

## <a name="about-assessments"></a>Sobre avaliações

Há dois tipos de avaliações que podem ser executadas usando a Avaliação de Servidor das Migrações para Azure.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: Com base em dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado padrão ou premium)**: Com base no IOPS e no throughput dos discos no local.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: Com base no tamanho da VM no local<br/><br> **Tipo de disco recomendado**: Com base na configuração do tipo de armazenamento selecionado para a avaliação.


## <a name="how-is-an-assessment-done"></a>Como é feita uma avaliação?

Uma avaliação feita no Azure Migrate Server Assessment tem três etapas. A avaliação começa com uma análise de adequação, seguida de dimensionamento, e por fim, uma estimativa de custo mensal. Uma máquina só passa para um estágio posterior se passar pelo anterior. Por exemplo, se um computador falhar na verificação de adequação do Azure, será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão calculados. [Saiba mais.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

**Propriedade** | **Detalhes**
--- | ---
**Localização do alvo** | O local do Azure para o qual você deseja migrar.<br/> A avaliação do servidor atualmente suporta essas regiões-alvo: Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Central dos EUA, China Leste, China Norte, Leste da Ásia, Leste dos EUA, Leste dos EUA2, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Oeste, Coréia Central, Coréia do Sul, Centro-Norte dos EUA, Europa Norte, Centro-Sul dos EUA, Sudeste Asiático, Índia do Sul, Reino Unido Sul, Reino Unido Oeste, US Gov Arizona, US Gov Texas, US Gov Virginia, West Central US, Europa Ocidental, Índia Ocidental, OESTE DOS EUA e US2 Oeste.
**Tipo de armazenamento** | Você pode usar esta propriedade para especificar o tipo de discos para os quais deseja mover-se no Azure.<br/><br/> Para o dimensionamento de premissas as-on, você pode especificar o tipo de armazenamento de destino como discos gerenciados premium, discos gerenciados por SSD padrão ou discos gerenciados por HDD padrão. Para dimensionamento baseado em desempenho, você pode especificar o tipo de disco de destino como discos automáticos, gerenciados por Premium, discos gerenciados por HDD padrão ou discos padrão gerenciados por SSD.<br/><br/> Quando você especifica o tipo de armazenamento como automático, a recomendação de disco é feita com base nos dados de desempenho dos discos (IOPS e taxa de transferência). Se você especificar o tipo de armazenamento como premium/padrão, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se você quiser alcançar um SLA VM de uma única instância de 99,9%, você pode querer especificar o tipo de armazenamento como discos gerenciados premium. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium. Azure
**Instâncias reservadas (RI)** | Esta propriedade ajuda você a especificar se você tem [Instâncias Reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, as estimativas de custos na avaliação são então feitas levando em descontos de RI. As instâncias reservadas atualmente só têm suporte para a oferta de Pagamento Conforme o Uso em Migrações para Azure.
**Critério de dimensionamento** | O critério a ser usado para VMs de tamanho direito para o Azure. Você pode fazer *dimensionamento baseado em desempenho* ou dimensionar as VMs *como no local,* sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a ser considerada para avaliação dos dados de desempenho das máquinas. Esta propriedade só é aplicável quando o critério de dimensionamento é *baseado em desempenho*.
**Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Esta propriedade só é aplicável quando o dimensionamento é *baseado em desempenho*.
**Série VM** |     Você pode especificar a série de VM que deseja considerar para o dimensionamento correto. Por exemplo, se você tiver um ambiente de produção que não planeja migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série e o dimensionamento correto será feito apenas na série selecionada.
**Fator de conforto** | A avaliação do servidor Azure Migrate considera um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos.
**Oferecer** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você se inscreveu. As Migrações para Azure calculam o custo de acordo com isso.
**Moeda** | Moeda de cobrança.
**Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure.<br/> A configuração padrão é 0%.
**Tempo de atividade da VM** | Se as VMs não estiverem em execução 24x7 no Azure, será possível especificar a duração da execução (número de dias por mês e número de horas por dia) e as estimativas de custo serão feitas adequadamente.<br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício híbrido do Azure** | Especifique se você tem garantia de software e se é elegível para [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. O padrão é Sim.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar propriedades de avaliação após a criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate, clique **em Servidores**.
2. No **Azure Migrate: Server Assessment**, clique na contagem de avaliações.
3. Em **Avaliação,** clique na avaliação relevante > **editar propriedades**.
5. Personalize as propriedades de avaliação de acordo com a tabela acima.
6. Clique em **Salvar** para atualizar a avaliação.


Você também pode editar propriedades de avaliação quando estiver criando uma avaliação.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
