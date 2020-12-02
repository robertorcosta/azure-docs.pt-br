---
title: Criar uma avaliação de VM do Azure com a avaliação de servidor de migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação de VM do Azure com a ferramenta de avaliação de servidor de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 534619ace09b4e11934062a591adf8d9ab6f77ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500794"
---
# <a name="create-an-azure-vm-assessment"></a>Criar uma avaliação de VM do Azure

Este artigo descreve como criar uma avaliação de VM do Azure para VMs VMware locais ou VMs do Hyper-V com migrações para Azure: avaliação de servidor.

As [migrações para Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](./create-manage-projects.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment.
- Para criar uma avaliação, você precisa configurar um dispositivo de migração do Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor. [Saiba mais](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Visão geral da avaliação de VM do Azure
Há dois tipos de critérios de dimensionamento que você pode usar para criar uma avaliação de VM do Azure usando migrações para Azure: avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização da CPU e de memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento selecionada para a avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **Servidores**, no bloco **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliar**.

    ![Captura de tela mostra os servidores de migração do Azure com a avaliação selecionada em ferramentas de avaliação.](./media/how-to-create-assessment/assess.png)

3. Em **Avaliar servidores**, selecione o tipo de avaliação como "VM do Azure", selecione a origem da descoberta e especifique o nome da avaliação.

    ![Noções básicas sobre a avaliação](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Clique em **Exibir tudo** para examinar as propriedades da avaliação.

    ![Propriedades de avaliação](./media/how-to-create-assessment//view-all.png)

5. Clique em **avançar** para **Selecionar computadores para avaliação**. Em **Selecionar ou criar um grupo**, selecione **Criar** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
6. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.
7. Clique em **avançar** para **Examinar + criar avaliação** para examinar os detalhes da avaliação.
8. Clique em **Criar Avaliação** para criar o grupo e execute a avaliação.

    ![Criar uma avaliação](./media/how-to-create-assessment//assessment-create.png)

9. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.
10. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.



## <a name="review-an-azure-vm-assessment"></a>Examinar uma avaliação de VM do Azure

Uma avaliação de VM do Azure descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

### <a name="view-an-azure-vm-assessment"></a>Exibir uma avaliação de VM do Azure

1. Em **Metas de migração** >  **Servidores**, clique em **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.
2. Em **Avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se as VMs estão prontas para a migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: as Migrações para Azure recomendam um tamanho de VM e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: usado quando as Migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

3. Clique em um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



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

- Saiba como usar o [mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.