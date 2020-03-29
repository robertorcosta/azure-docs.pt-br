---
title: Crie uma avaliação com a avaliação do servidor azure migrate | Microsoft Docs
description: Descreve como criar uma avaliação com a ferramenta Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229096"
---
# <a name="create-an-assessment"></a>Criar uma avaliação

Este artigo descreve como criar uma avaliação para VMS VMs ou VMs Hyper-V com Azure Migrate: Server Assessment.

[O Azure Migrate](migrate-services-overview.md) ajuda você a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto azure Migrate.
- Se você já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta Azure Migrate: Server Assessment.
- Para criar uma avaliação, você precisa configurar um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia dados de metadados e desempenho para o Azure Migrate: Server Assessment. [Saiba mais](migrate-appliance.md).


## <a name="assessment-overview"></a>Visão geral da avaliação
Existem dois tipos de avaliações que você pode criar usando o Azure Migrate: Avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: Com base em dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado padrão ou premium)**: Com base no IOPS e no throughput dos discos no local.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: Com base no tamanho da VM no local<br/><br> **Tipo de disco recomendado**: Com base na configuração do tipo de armazenamento selecionado para a avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **Servidores,** no **Azure Migrate: Server Assessment** tile, clique **em Avaliar**.

    ![Avaliar](./media/how-to-create-assessment/assess.png)

2. Em **Avaliar servidores,** especifique um nome para a avaliação.
3. Clique em **Exibir tudo** para examinar as propriedades da avaliação.

    ![Propriedades de avaliação](./media/how-to-create-assessment//view-all.png)

3. Em **Selecionar ou criar um grupo,** **selecione Criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.
5. Clique em **Criar Avaliação** para criar o grupo e execute a avaliação.

    ![Criar uma avaliação](./media/how-to-create-assessment//assessment-create.png)

6. Depois que a avaliação for criada, visualize-a em **Servers** > **Azure Migrate: Server Assessment** > **Assessmentassessments**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.



## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Prontidão**azul : Se as VMs são adequadas para migração para o Azure.
- **Estimativa mensal de custos**: Os custos mensais estimados de computação e armazenamento para a execução das VMs no Azure.
- **Estimativa mensal de custos de armazenamento**: Custos estimados para armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. **Servers**Em Servidores de metas >   **de migração,** clique em **Avaliações** no **Azure Migrate: Avaliação do servidor**.
2. Em **Avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se as VMs estão prontas para a migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: O Azure Migrate recomenda um tamanho de VM e estimativas de custos para VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e remediação sugerida.
    - **Não está pronto para o Azure**: Mostra problemas e remediação sugerida.
    - **Prontidão desconhecida**: Usada quando o Azure Migrate não pode avaliar a prontidão, devido a problemas de disponibilidade de dados.

2. Clique em um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Examinar os detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento da execução das VMs no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e em seus discos e propriedades.
    - Os custos mensais estimados de computação e armazenamento são mostrados.
    - A estimativa de custo refere-se à execução das VMs locais como VMs de IaaS. A Avaliação de Servidor das Migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo mensal de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Revisar classificação de confiança

Quando você executa avaliações com base no desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é fornecida.
- A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são indicadas a seguir.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas




## <a name="next-steps"></a>Próximas etapas

- Aprenda a usar [o mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
