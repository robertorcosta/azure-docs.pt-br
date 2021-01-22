---
title: Análise de custo e orçamentos
description: Saiba como obter uma análise de custo, definir um orçamento e reduzir os custos para os recursos de computação subjacentes e as licenças de software usadas para executar suas cargas de trabalho do lote.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679299"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Análise de custo e orçamentos para o Lote do Azure

Não há nenhum custo para usar o lote do Azure em si, embora possa haver cobranças para os recursos de computação subjacentes e as licenças de software usadas para executar cargas de trabalho do lote. Os custos podem ser incorridos de máquinas virtuais (VMs) em um pool, transferência de dados da VM ou quaisquer dados de entrada ou saída armazenados na nuvem. Este tópico o ajudará a entender de onde vêm os custos, como definir um orçamento para um pool ou conta do lote e maneiras de reduzir os custos das cargas de trabalho do lote.

## <a name="batch-resources"></a>Recursos do Lote

As máquinas virtuais são o recurso mais significativo usado para processamento do Lote. O custo do uso de VMs para o Lote é calculado com base no tipo, na quantidade e na duração do uso. As opções de cobrança de VM incluem [Pagamento Conforme o Uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento com antecedência). Ambas as opções de pagamento têm benefícios diferentes dependendo da carga de trabalho de computação e afetarão sua fatura de forma diferente.

Quando os aplicativos são implantados em nós do Lote (VMs) usando [pacotes de aplicativos](batch-application-packages.md), você é cobrado pelos recursos de Armazenamento do Azure que seus pacotes de aplicativos consomem. Também haverá cobrança pelo armazenamento de qualquer arquivo de entrada ou de saída, como arquivos de recurso e outros dados de log. Em geral, o custo dos dados de armazenamento associados ao Lote é muito menor do que o custo dos recursos de computação. Cada VM em um pool criado com a [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) tem um disco de sistema operacional associado que usa discos gerenciados pelo Azure. Os discos gerenciados pelo Azure têm um custo adicional, e outros níveis de desempenho de disco também têm custos diferentes.

Os pools do Lote usam recursos de rede. Em particular, para pools **VirtualMachineConfiguration** , são usados balanceadores de carga padrão, que exigem endereços IP estáticos. Os balanceadores de carga usados pelo Lote são visíveis para contas de **Assinatura de Usuário**, mas não são visíveis para contas de **Serviço do Lote**. Os balanceadores de carga padrão incorrem em encargos para todos os dados passados para VMs do pool do Lote e recebidos delas; algumas APIs do Lote que recuperam dados de nós de pool (como Obter Tarefa/Arquivo de Nó), pacotes de aplicativos de tarefa, arquivos de saída/recursos e imagens de contêiner incorrem em encargos.

### <a name="additional-services"></a>Serviços adicionais

Serviços que não incluem VMs e armazenamento podem considerar o custo da sua conta do Lote.

Outros serviços comumente usados com o Lote podem incluir:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicativos gráficos

Dependendo de quais serviços você usa com sua solução do Lote, podem incorrer valores adicionais. Confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para determinar o custo de cada serviço adicional.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Análise de custo e orçamento para um pool

No portal do Azure, você pode criar orçamentos e gastar alertas para seus pools do lote ou contas do lote. Orçamentos e alertas são úteis para notificar os participantes de qualquer risco de excesso de gastos, embora seja possível que haja um atraso em alertas de gastos e um pouco excedente a um orçamento.

Neste exemplo, vamos exibir a análise de custo de um pool individual do Lote.

1. Na portal do Azure, digite ou selecione gerenciamento de **custos + cobrança** .
1. Selecione sua assinatura na seção **escopos de cobrança** .
1. Em **Gerenciamento de Custos**, selecione **Análise de custo**.
1. Selecione **Adicionar Filtro**. Na primeira lista suspensa, selecione **recurso**
1. Na segunda lista suspensa, selecione o pool do Lote. Quando o pool for selecionado, você verá a análise de custo para o pool, semelhante ao exemplo mostrado aqui.
    ![Captura de tela mostrando a análise de custo de um pool no portal do Azure.](./media/batch-budget/pool-cost-analysis.png)

A análise de custo resultante mostra o custo do pool, bem como os recursos que contribuem com esse custo. Neste exemplo, as VMs usadas no pool são o recurso mais dispendioso.

Para criar um orçamento para o pool, selecione **orçamento: nenhum** e, em seguida, selecione **criar novo orçamento >**. Agora, use a janela para [configurar um orçamento](../cost-management-billing/costs/tutorial-acm-create-budgets.md) especificamente para o pool.

> [!NOTE]
> O Lote do Azure baseia-se na tecnologia de Serviços de Nuvem do Azure e Máquinas Virtuais do Azure. Ao escolher a **Configuração de Serviços de Nuvem**, você é cobrado com base na estrutura de preços dos Serviços de Nuvem. Ao escolher a **Configuração da Máquina Virtual**, você é cobrado com base na estrutura de preços das Máquinas Virtuais. O exemplo nesta página usa a **configuração de máquina virtual**, que é recomendada para a maioria dos pools do lote.

## <a name="minimize-cost"></a>Minimizar o custo

O uso de várias VMs e serviços do Azure por longos períodos de tempo pode ser dispendioso. Considere o uso dessas estratégias para maximizar a eficiência de suas cargas de trabalho e reduzir os custos.

### <a name="low-priority-virtual-machines"></a>Máquinas virtuais de baixa prioridade

As [VMs de baixa prioridade](batch-low-pri-vms.md) reduzem o custo das cargas de trabalho do lote aproveitando a capacidade de computação excedente no Azure. Quando você especifica VMs de baixa prioridade em seus pools, o Lote usa esse excedente para executar sua carga de trabalho. Pode haver economias de custo substanciais quando você usa VMs de baixa prioridade em vez de VMs dedicadas.

### <a name="virtual-machine-os-disk-type"></a>Tipo de disco de SO da máquina virtual

O Azure oferece vários [tipos de disco de sistema operacional de VM](../virtual-machines/disks-types.md). A maioria das séries de VM tem tamanhos que dão suporte ao armazenamento Premium e Standard. Quando um ' tamanho da VM ' é selecionado para um pool, o lote configura OS discos do sistema operacional SSD Premium. Quando o tamanho da VM ' não s' é selecionado, o tipo de disco HDD padrão mais barato é usado. Por exemplo, os discos do SO de SSD Premium são usados para `Standard_D2s_v3` e os discos do SO de HDD padrão são usados para `Standard_D2_v3`.

SSD Premium discos do sistema operacional são mais caros, mas têm melhor desempenho. As VMs com discos Premium podem começar um pouco mais rapidamente do que as VMs com discos do sistema operacional HDD padrão. Com o lote, o disco do sistema operacional geralmente não é muito usado, já que os aplicativos e os arquivos de tarefas estão localizados no disco SSD temporário da VM. Por isso, você geralmente pode selecionar o tamanho da VM "não-s" para evitar pagar o aumento do custo do SSD Premium que é provisionado quando um "tamanho da VM" é especificado.

### <a name="reserved-virtual-machine-instances"></a>Instâncias de máquina virtual reservada

Se você pretende usar o lote por um longo período de tempo, pode reduzir o custo das VMs usando as [reservas do Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para suas cargas de trabalho. Uma taxa de reserva é consideravelmente menor do que uma taxa paga conforme o uso. As instâncias de máquina virtual usadas sem uma reserva são cobradas com a taxa paga conforme o uso. Quando você adquire uma reserva, o desconto de reserva é aplicado.

### <a name="automatic-scaling"></a>Dimensionamento automático

O [dimensionamento automático](batch-automatic-scaling.md) dimensiona dinamicamente o número de VMs em seu pool do Lote com base nas demandas do trabalho atual. Ao dimensionar o pool com base no tempo de vida de um trabalho, o dimensionamento automático garante que as VMs sejam escaladas verticalmente e usadas somente quando houver um trabalho a ser executado. Quando o trabalho for concluído ou quando não houver nenhum trabalho, as VMs serão dimensionadas automaticamente para salvar os recursos de computação. O dimensionamento permite reduzir o custo geral da solução do Lote usando apenas os recursos de que você precisa.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação e o monitoramento de soluções do Lote.  
- Saiba mais sobre como [usar VMs de baixa prioridade com o lote](batch-low-pri-vms.md).
