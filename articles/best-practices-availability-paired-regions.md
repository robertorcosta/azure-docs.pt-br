---
title: Garantir a continuidade de negócios & recuperação de desastres usando regiões emparelhadas do Azure
description: Garanta a resiliência do aplicativo usando o emparelhamento regional do Azure
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248249"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

Uma região do Azure consiste em um conjunto de data centers implantados dentro de um perímetro definido por latência e conectados por meio de uma rede dedicada de baixa latência.  Isso garante que os serviços do Azure dentro de uma região do Azure ofereçam o melhor desempenho e segurança possíveis.  

Uma geografia do Azure define uma área do mundo que contém pelo menos uma região azure. As geografias definem um mercado discreto, tipicamente contendo duas ou mais regiões, que preservam os limites de residência e conformidade de dados.  Saiba mais informações sobre a infra-estrutura global do Azure [aqui](https://azure.microsoft.com/global-infrastructure/regions/)

Um par regional consiste em duas regiões dentro da mesma geografia. O Azure serializa as atualizações da plataforma (manutenção planejada) entre pares regionais, garantindo que apenas uma região em cada par atualize de cada vez. Se uma paralisação afetar várias regiões, pelo menos uma região em cada par será priorizada para a recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Alguns serviços do Azure aproveitam ainda mais as regiões emparelhadas para garantir a continuidade dos negócios e proteger contra a perda de dados.  O Azure fornece várias [soluções](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) de armazenamento que aproveitam as regiões emparelhadas para garantir a disponibilidade de dados. Por exemplo, [o GRS (Azure Geo-redundantStorage, armazenamento geo-redundante)](./storage/common/storage-redundancy.md#geo-redundant-storage) replica dados para uma região secundária automaticamente, garantindo que os dados sejam duráveis mesmo no caso de a região primária não ser recuperável. 

Observe que nem todos os serviços do Azure replicam automaticamente dados, nem todos os serviços do Azure recuam automaticamente de uma região com falha para seu par.  Nesses casos, a recuperação e a replicação devem ser configuradas pelo cliente.

## <a name="can-i-select-my-regional-pairs"></a>Posso selecionar meus pares regionais?

Não. Alguns serviços do Azure dependem de pares regionais, como o [armazenamento redundante](./storage/common/storage-redundancy.md)do Azure. Esses serviços não permitem que você crie novos pares regionais.  Da mesma forma, como o Azure controla a priorização planejada de manutenção e recuperação para pares regionais, você não pode definir seus próprios pares regionais para aproveitar esses serviços. No entanto, você pode criar sua própria solução de recuperação de desastres construindo serviços em qualquer número de regiões e aproveitando os serviços do Azure para emparelhá-los. 

Por exemplo, você pode usar serviços do Azure, como [o AzCopy,](./storage/common/storage-use-azcopy-v10.md) para agendar backups de dados para uma conta de armazenamento em uma região diferente.  Usando [o Azure DNS e o Azure Traffic Manager,](./networking/disaster-recovery-dns-traffic-manager.md)os clientes podem projetar uma arquitetura resiliente para seus aplicativos que sobreviverá à perda da região primária.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Estou limitado a usar serviços dentro dos meus pares regionais?

Não. Embora um determinado serviço Azure possa contar com um par regional, você pode hospedar seus outros serviços em qualquer região que satisfaça suas necessidades de negócios.  Uma solução de armazenamento Azure GRS pode emparelhar dados no Canada Central com um peer no Canadá Leste enquanto usa recursos de computação localizados no leste dos EUA.  

## <a name="must-i-use-azure-regional-pairs"></a>Devo usar pares regionais do Azure?

Não. Os clientes podem aproveitar os serviços do Azure para projetar um serviço resiliente sem depender dos pares regionais do Azure.  No entanto, recomendamos que você configure a recuperação de desastres de continuidade de negócios (BCDR) entre pares regionais para se beneficiar do [isolamento](./security/fundamentals/isolation-choices.md) e melhorar a [disponibilidade](./availability-zones/az-overview.md). Para aplicativos compatíveis com várias regiões ativas, é recomendável usar ambas as regiões em um par de regiões, sempre que possível. Isso garante a disponibilidade ideal para aplicativos e minimiza o tempo de recuperação em caso de desastre. Sempre que possível, projete sua aplicação para [máxima resiliência](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) e facilidade de recuperação de [desastres.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Pares Regionais Azure

| painel Geografia do app&#39;s selecionado | Par Regional A | Par Regional B  |
|:--- |:--- |:--- |
| Ásia-Pacífico |Ásia Oriental (Hong Kong) | Sudeste asiático (Cingapura) |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |Centro-Sul dos Estados Unidos |
| Canada |Canadá Central |Leste do Canadá |
| China |Norte da China |Leste da China|
| China |Norte da China 2 |Leste da China 2|
| Europa |Europa do Norte (Irlanda) |Europa Ocidental (Países Baixos) |
| França |França Central|Sul da França|
| Alemanha |Alemanha Central |Nordeste da Alemanha |
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia do Sul |Coreia Central |Sul da Coreia |
| América do Norte |Leste dos EUA |Oeste dos EUA |
| América do Norte |Leste dos EUA 2 |Centro dos EUA |
| América do Norte |Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| América do Norte |Oeste dos EUA 2 |Centro-Oeste dos EUA |
| Noruega | Leste da Noruega | Noruega Ocidental |
| África do Sul | Norte da África do Sul |África do Sul Ocidental |
| Suíça | Suíça Norte |Suíça Ocidental |
| Reino Unido |Oeste do Reino Unido |Sul do Reino Unido |
| Emirados Árabes Unidos | Norte dos EAU | EAU Central
| Departamento de Defesa dos EUA |DoD do Leste dos EUA |DoD Central dos EUA |
| Governo dos EUA |Governo dos EUA do Arizona |Governo dos EUA do Texas |
| Governo dos EUA |US Gov Iowa |Gov. dos EUA – Virgínia |
| Governo dos EUA |Gov. dos EUA – Virgínia |Governo dos EUA do Texas |

> [!Important]
> - A Índia Ocidental está emparelhada em uma única direção. A região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia é a Índia Central.
> - O Brasil Sul é único porque está emparelhado com uma região fora de sua geografia. Brasil A região secundária do Sul é o Centro-Sul dos EUA. A região secundária do Centro-Sul dos EUA não é o Sul do Brasil.


## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A imagem abaixo ilustra um aplicativo hipotético que usa o par regional para recuperação de desastres. Os números verdes destacam as atividades inter-regionais de três serviços do Azure (computação, armazenamento e banco de dados do Azure) e como eles estão configurados para se replicar em todas as regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.

![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

## <a name="cross-region-activities"></a>Atividades entre regiões
Como mencionado na Figura 2.

1. **Azure Compute (IaaS)** – Você deve prover recursos adicionais de computação com antecedência para garantir que os recursos estejam disponíveis em outra região durante um desastre. Para saber mais, confira as [Orientação técnica de resiliência do Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Armazenamento azure** - Se você estiver usando discos gerenciados, aprenda sobre [backups entre regiões](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) com backup sinuoso e [replique VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) de uma região para outra com o Azure Site Recovery. Se você estiver usando contas de armazenamento, então o GRS (Geo-Redundante Storage, armazenamento geo-redundante) será configurado por padrão quando uma conta do Azure Storage for criada. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage/common/storage-redundancy.md).

3. **Banco de dados Azure SQL** – Com a Geo-Replicação do Banco de Dados Azure SQL, você pode configurar a replicação assíncrona das transações para qualquer região do mundo; no entanto, recomendamos que você implante esses recursos em uma região emparelhada para a maioria dos cenários de recuperação de desastres. Para saber mais, confira [Replicação geográfica no Banco de Dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** – O Resource Manager fornece de maneira inerente isolamento lógico dos componentes entre as regiões. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## <a name="benefits-of-paired-regions"></a>Benefícios de uma região emparelhada

5. **Isolamento físico** – Quando possível, o Azure prefere pelo menos 300 milhas de separação entre datacenters em um par regional, embora isso não seja prático ou possível em todas as geografias. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).  

6. **Replicação fornecida pela plataforma** - Alguns serviços, como o Geo-Redundant Storage, fornecem replicação automática para a região emparelhada.

7. **Ordem de recuperação da região** – Em caso de uma paralisação ampla, a recuperação de uma região é priorizada para fora de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

8. **Atualizações seqüenciais** – As atualizações planejadas do sistema Azure são implementadas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de bugs e falhas lógicas no raro caso de uma atualização ruim.

9. **Residência de dados** – Uma região reside dentro da mesma geografia que seu par (com exceção do Brasil Sul) para atender aos requisitos de residência de dados para fins de jurisdição tributária e de aplicação da lei.
