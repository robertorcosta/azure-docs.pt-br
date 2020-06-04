---
title: Análise de custo e orçamento
description: Saiba como obter uma análise de custo e definir um orçamento para os recursos de computação subjacentes e as licenças de software usadas para executar suas cargas de trabalho do Lote.
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 13c8cc508a4940b5e21570104527c40988879919
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725764"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Análise de custo e orçamentos para o Lote do Azure

Não há encargos para o Lote do Azure em si, somente os recursos de computação subjacentes e as licenças de software usadas para executar cargas de trabalho do Lote. Em um alto nível, os custos são incorridos de VMs (máquinas virtuais) em um pool, uma transferência de dados da VM ou em dados de entrada ou saída armazenados na nuvem. Vamos dar uma olhada em alguns dos principais componentes do Lote para entender de onde vêm os custos, como definir um orçamento para um pool ou uma conta e algumas técnicas para tornar suas cargas de trabalho do Lote mais econômicas.

## <a name="batch-resources"></a>Recursos do Lote

As máquinas virtuais são o recurso mais significativo usado para processamento do Lote. O custo do uso de VMs para o Lote é calculado com base no tipo, na quantidade e na duração do uso. As opções de cobrança de VM incluem [Pagamento Conforme o Uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento com antecedência). Ambas as opções de pagamento têm benefícios diferentes, dependendo de sua carga de trabalho de computação, e ambos os modelos de pagamento afetarão sua fatura de maneira diferente.

Quando os aplicativos são implantados em nós do Lote (VMs) usando [pacotes de aplicativos](batch-application-packages.md), você é cobrado pelos recursos de Armazenamento do Azure que seus pacotes de aplicativos consomem. Também haverá cobrança pelo armazenamento de qualquer arquivo de entrada ou de saída, como arquivos de recurso e outros dados de log. Em geral, o custo dos dados de armazenamento associados ao Lote é muito menor do que o custo dos recursos de computação. Cada VM em um pool criado com **VirtualMachineConfiguration** tem um disco de SO associado que usa discos gerenciados pelo Azure. Os discos gerenciados pelo Azure têm um custo adicional, e outros níveis de desempenho de disco também têm custos diferentes.

Os pools do Lote usam recursos de rede. Em particular, para pools **VirtualMachineConfiguration**, os balanceadores de carga padrão são usados, o que exige endereços IP estáticos. Os balanceadores de carga usados pelo Lote são visíveis para contas de **Assinatura de Usuário**, mas não são visíveis para contas de **Serviço do Lote**. Os balanceadores de carga padrão incorrem em encargos para todos os dados passados para VMs do pool do Lote e recebidos delas; algumas APIs do Lote que recuperam dados de nós de pool (como Obter Tarefa/Arquivo de Nó), pacotes de aplicativos de tarefa, arquivos de saída/recursos e imagens de contêiner incorrem em encargos.

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

Por meio do portal do Azure, é possível criar orçamentos e gastar alertas para os pools do Lote ou a conta do Lote. Orçamentos e alertas são úteis para notificar os stakeholders de qualquer risco de excesso de gastos. É possível que haja um atraso em alertas de gastos e que ele exceda ligeiramente um orçamento. Neste exemplo, vamos exibir a análise de custo de um pool individual do Lote.

1. No portal do Azure, selecione **Gerenciamento de Custos + Cobrança** na barra de navegação à esquerda.
1. Selecione sua assinatura na seção **Minhas assinaturas**
1. Vá para **Análise de custo** na seção **Gerenciamento de Custos** da barra de navegação à esquerda, que mostrará uma exibição como esta:
1. Selecione **Adicionar Filtro**. Na primeira lista suspensa, selecione **Recurso** ![Selecione o filtro de recursos](./media/batch-budget/resource-filter.png)
1. Na segunda lista suspensa, selecione o pool do Lote. Quando o pool for selecionado, a análise de custo será semelhante à análise a seguir.
    ![Análise de custo de um pool](./media/batch-budget/pool-cost-analysis.png)

A análise de custo resultante mostra o custo do pool, bem como os recursos que contribuem com esse custo. Neste exemplo, as VMs usadas no pool são o recurso mais dispendioso.

Para criar um orçamento para o pool, selecione **Orçamento: nenhum** e, em seguida, selecione **Criar orçamento >** . Agora, use a janela para configurar um orçamento especificamente para o pool.

Para obter mais informações sobre como configurar um orçamento, confira [Criar e gerenciar orçamentos do Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> O Lote do Azure baseia-se na tecnologia de Serviços de Nuvem do Azure e Máquinas Virtuais do Azure. Ao escolher a **Configuração de Serviços de Nuvem**, você é cobrado com base na estrutura de preços dos Serviços de Nuvem. Ao escolher a **Configuração da Máquina Virtual**, você é cobrado com base na estrutura de preços das Máquinas Virtuais. O exemplo nesta página usa a **Configuração de Máquina Virtual**.

## <a name="minimize-cost"></a>Minimizar o custo

O uso de várias VMs e serviços do Azure por longos períodos de tempo pode ser dispendioso. Felizmente, há serviços disponíveis para ajudar a reduzir seus gastos, bem como estratégias para maximizar a eficiência de sua carga de trabalho.

### <a name="low-priority-virtual-machines"></a>Máquinas virtuais de baixa prioridade

As VMs de baixa prioridade reduzem o custo das cargas de trabalho do Lote aproveitando a capacidade de computação excedente no Azure. Quando você especifica VMs de baixa prioridade em seus pools, o Lote usa esse excedente para executar sua carga de trabalho. Há uma economia significativa com o uso de VMs de baixa prioridade em vez de VMs dedicadas.

Saiba mais sobre como configurar VMs de baixa prioridade para sua carga de trabalho em [Usar VMs de baixa prioridade com o Lote](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo de disco de SO da máquina virtual

Há vários [tipos de disco de SO de VM](../virtual-machines/windows/disks-types.md). A maioria das séries de VM tem tamanhos que dão suporte ao armazenamento Premium e Standard. Quando um tamanho de VM 's' é selecionado para um pool, o Lote configura discos do SO de SSD Premium. Quando o tamanho de VM 'não s' é selecionado, o tipo de disco HDD padrão mais barato é usado. Por exemplo, os discos do SO de SSD Premium são usados para `Standard_D2s_v3` e os discos do SO de HDD padrão são usados para `Standard_D2_v3`.

Os discos do SO de SSD Premium são mais caros, mas têm desempenho mais alto e VMs com discos Premium podem iniciar um pouco mais rapidamente do que as VMs com discos do SO de HDD padrão. Com o Lote, o disco do SO geralmente não é tão usado, pois os aplicativos e os arquivos de tarefa estão localizados no disco SSD temporário de VMs. Portanto, em muitos casos, não há necessidade de pagar o aumento do custo para o SSD Premium que é provisionado quando um tamanho de VM 's' é especificado.

### <a name="reserved-virtual-machine-instances"></a>Instâncias de máquina virtual reservada

Se você pretende usar o Lote por um longo período de tempo, pode economizar no custo das VMs usando [Reservas do Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para suas cargas de trabalho. Uma taxa de reserva é consideravelmente menor do que uma taxa paga conforme o uso. As instâncias de máquina virtual usadas sem uma reserva são cobradas à taxa paga conforme o uso. Se você comprar uma reserva, o desconto de reserva será aplicado e você não será mais cobrado com as taxas pagas conforme o uso.

### <a name="automatic-scaling"></a>Dimensionamento automático

O [dimensionamento automático](batch-automatic-scaling.md) dimensiona dinamicamente o número de VMs em seu pool do Lote com base nas demandas do trabalho atual. Ao dimensionar o pool com base no tempo de vida de um trabalho, o dimensionamento automático garante que as VMs sejam escaladas verticalmente e usadas somente quando houver um trabalho a ser executado. Quando o trabalho for concluído ou não houver trabalhos, as VMs passarão por redução vertical automaticamente para salvar os recursos de computação. O dimensionamento permite reduzir o custo geral da solução do Lote usando apenas os recursos de que você precisa.

Para saber mais sobre o dimensionamento automático, confira [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação e o monitoramento de soluções do Lote.  

- Saiba m ais sobre [VMs de baixa prioridade com o Lote](batch-low-pri-vms.md).
