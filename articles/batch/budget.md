---
title: Análise de custos e orçamento - Lote Azure
description: Aprenda a obter uma análise de custos e defina um orçamento para os recursos de computação subjacentes e licenças de software usadas para executar suas cargas de trabalho em lote.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022708"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Análise de custos e orçamentos para o Lote Azure

Não há custo para o Azure Batch em si, apenas os recursos de computação subjacentes e licenças de software usadas para executar cargas de trabalho em lote. Em um alto nível, os custos são incorridos de máquinas virtuais (VMs) em um pool, transferência de dados da VM ou quaisquer dados de entrada ou saída armazenados na nuvem. Vamos dar uma olhada em alguns componentes-chave do Batch para entender de onde vêm os custos, como definir um orçamento para um pool ou conta, e algumas técnicas para tornar suas cargas de trabalho em lote mais econômicas.

## <a name="batch-resources"></a>Recursos do Lote

As máquinas virtuais são o recurso mais significativo usado para o processamento em lote. O custo de utilização de VMs para Batch é calculado com base no tipo, quantidade e duração do uso. As opções de faturamento da VM incluem [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento antecipado). Ambas as opções de pagamento têm benefícios diferentes dependendo da sua carga de trabalho computacional, e ambos os modelos de pagamento afetarão sua conta de forma diferente.

Quando os aplicativos são implantados em nós de lote (VMs) usando [pacotes de aplicativos,](batch-application-packages.md)você é cobrado pelos recursos de armazenamento do Azure que os pacotes de aplicativos consomem. Você também é cobrado pelo armazenamento de quaisquer arquivos de entrada ou saída, como arquivos de recursos e outros dados de log. Em geral, o custo dos dados de armazenamento associados ao Batch é muito menor do que o custo dos recursos computacionais. Cada VM em um pool criado com **virtualmachineConfiguration** tem um disco operacional associado que usa discos gerenciados pelo Azure. Os discos gerenciados pelo Azure têm um custo adicional, e outros níveis de desempenho de disco também têm custos diferentes.

Os pools de lotes usam recursos de rede. Em particular, para **virtualmachineConfiguration** pools de balanceadores de carga padrão são usados, que requerem endereços IP estáticos. Os balanceadores de carga usados pelo Batch são visíveis para contas **de Assinatura de Usuário,** mas não são visíveis para contas **de serviço em lote.** Os balanceadores de carga padrão incorrem em encargos para todos os dados passados para e de VMs de pool de lote; selecione APIs de lote que recuperam dados de nós de pool (como Obter arquivo de tarefa/nó), pacotes de aplicativos de tarefas, arquivos de recursos/saída e imagens de contêiner incorrerão em encargos.

### <a name="additional-services"></a>Serviços adicionais

Serviços que não incluem VMs e armazenamento podem considerar o custo de sua conta Batch.

Outros serviços comumente usados com batch podem incluir:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicações gráficas

Dependendo dos serviços que você usa com sua solução Batch, você pode incorrer em taxas adicionais. Consulte a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para determinar o custo de cada serviço adicional.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Análise de custos e orçamento para um pool

Através do portal Azure, você pode criar orçamentos e alertas de gastos para sua conta de pool(s) ou batch. Orçamentos e alertas são úteis para notificar as partes interessadas de quaisquer riscos de gastos excessivos. É possível que haja um atraso nos alertas de gastos e exceda ligeiramente um orçamento. Neste exemplo, veremos a análise de custos de um pool de lotes individual.

1. No portal Azure, selecione **Gerenciamento de Custos + Faturamento** na barra de navegação à esquerda.
1. Selecione sua assinatura na seção **Minhas assinaturas**
1. Vá para a **análise de custos** na seção De Gerenciamento de **Custos** da barra de navegação esquerda, que mostrará uma visão como esta:
1. Selecione **Adicionar filtro**. Na primeira lista de paradas, selecione **Recurso** ![Selecione o filtro de recursos](./media/batch-budget/resource-filter.png)
1. Na segunda queda, selecione o pool Batch. Quando o pool for selecionado, a análise de custos será semelhante à análise a seguir.
    ![Análise de custos de um pool](./media/batch-budget/pool-cost-analysis.png)

A análise de custos resultante mostra o custo do pool, bem como os recursos que contribuem para esse custo. Neste exemplo, as VMs usadas no pool são os recursos mais caros.

Para criar um orçamento para o pool selecione **Orçamento: nenhum**e, em seguida, selecione **Criar novo >de orçamento **. Agora use a janela para configurar um orçamento especificamente para o seu pool.

Para obter mais informações sobre a configuração de um orçamento, consulte [Criar e gerenciar os orçamentos do Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> O Lote do Azure baseia-se na tecnologia de Serviços de Nuvem do Azure e Máquinas Virtuais do Azure. Ao escolher a **Configuração de Serviços de Nuvem**, você é cobrado com base na estrutura de Preços dos Serviços de Nuvem. Ao escolher a **Configuração da Máquina Virtual**, você é cobrado com base na estrutura de Preços das Máquinas Virtuais. O exemplo nesta página usa a **Configuração da Máquina Virtual**.

## <a name="minimize-cost"></a>Minimizar o custo

Usar várias VMs e serviços do Azure por longos períodos de tempo pode ser caro. Felizmente, existem serviços disponíveis para ajudar a reduzir seus gastos, bem como estratégias para maximizar a eficiência de sua carga de trabalho.

### <a name="low-priority-virtual-machines"></a>Máquinas virtuais de baixa prioridade

As VMs de baixa prioridade reduzem o custo das cargas de trabalho em lote, aproveitando a capacidade máxima de computação no Azure. Quando você especifica VMs de baixa prioridade em seus pools, Batch usa esse excedente para executar sua carga de trabalho. Há uma economia substancial de custos usando VMs de baixa prioridade no lugar de VMs dedicadas.

Saiba mais sobre como configurar VMs de baixa prioridade para sua carga de trabalho em [Usar VMs de baixa prioridade com Batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo de disco do sistema operacional da máquina virtual

Existem vários [tipos de disco do Sistema Operacional VM](../virtual-machines/windows/disks-types.md). A maioria das séries VM tem tamanhos que suportam armazenamento premium e padrão. Quando um tamanho 's' de VM é selecionado para um pool, Batch configura discos ssd premium do sistema operacional. Quando o tamanho 'não-s' da VM é selecionado, então o tipo de disco HDD padrão mais barato é usado. Por exemplo, discos ssd ssd premium são usados `Standard_D2s_v3` para `Standard_D2_v3`e discos hdd padrão do sistema operacional são usados para .

Os discos premium do SSD OS são mais caros, mas têm desempenho mais alto e as VMs com discos premium podem começar um pouco mais rápido que as VMs com discos de sistema operacional HDD padrão. Com batch, o disco do SISTEMA OPERACIONAL muitas vezes não é usado muito, pois os aplicativos e arquivos de tarefas estão localizados no disco SSD temporário das VMs. Portanto, em muitos casos, não há necessidade de pagar o custo aumentado para o SSD premium que é provisionado quando um tamanho de VM 's' é especificado.

### <a name="reserved-virtual-machine-instances"></a>Instâncias de máquinas virtuais reservadas

Se você pretende usar batch por um longo período de tempo, você pode economizar no custo de VMs usando [Reservas Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para suas cargas de trabalho. Uma taxa de reserva é consideravelmente menor do que uma taxa de pagamento à medida que você vai. As instâncias das máquinas virtuais usadas sem reserva são cobradas à taxa de pagamento. Se você comprar uma reserva, o desconto de reserva é aplicado e você não é mais cobrado nas taxas de pagamento.

### <a name="automatic-scaling"></a>Dimensionamento automático

[O dimensionamento automático](batch-automatic-scaling.md) dimensiona dinamicamente o número de VMs em seu pool de lotes com base nas demandas do trabalho atual. Ao dimensionar o pool com base na vida útil de um trabalho, o dimensionamento automático garante que as VMs são dimensionadas e usadas apenas quando há um trabalho a ser realizado. Quando o trabalho está concluído, ou não há empregos, as VMs são automaticamente dimensionadas para economizar recursos computacionais. O dimensionamento permite reduzir o custo total da sua solução Batch usando apenas os recursos necessários.

Para obter mais informações sobre o dimensionamento automático, consulte [Dimensionar automaticamente nós de computação em um pool de lotes Azure](batch-automatic-scaling.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [APIs em lote e ferramentas](batch-apis-tools.md) disponíveis para construção e monitoramento de soluções em lote.  

- Saiba mais sobre [VMs de baixa prioridade com Batch](batch-low-pri-vms.md).
