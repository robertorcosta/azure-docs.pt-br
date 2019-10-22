---
title: Analisar o relatório de Planejador de Implantações do Azure Site Recovery para recuperação de desastre do VMware para o Azure | Microsoft Docs
description: Este artigo descreve como analisar o relatório gerado pelo Planejador de Implantações do Azure Site Recovery para a recuperação de desastres do VMware no Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: 4240e17320cc62dc1a0e74db2f40a452a63f2982
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690722"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analisar o relatório de Planejador de Implantações do Azure Site Recovery para a recuperação de desastres do VMware para o Azure

O relatório gerado do Microsoft Excel contém as seguintes planilhas:
## <a name="on-premises-summary"></a>Resumo local
A planilha de resumo local fornece uma visão geral do ambiente VMware com perfil.

![Resumo local do ambiente VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Data de início** e **data de término**: as datas de início e de término dos dados de criação de perfil considerados para geração de relatórios. Por padrão, a data de início é a data de início da criação de perfil e a data de término é a data em que a criação de perfil é interrompida. Poderão ser os valores 'StartDate' e 'EndDate' se o relatório for gerado com esses parâmetros.

**Número total de dias de criação de perfil**: o número total de dias de criação de perfil entre as datas de início e término para as quais o relatório é gerado.

**Número de máquinas virtuais compatíveis**: o número total de VMs compatíveis para as quais a largura de banda de rede necessária, o número necessário de contas de armazenamento, núcleos de Microsoft Azure, servidores de configuração e servidores de processo adicionais são calculados.

**Número total de discos em todas as máquinas virtuais compatíveis**: o número usado como uma das entradas para decidir o número de servidores de configuração e servidores de processo adicionais a serem usados na implantação.

**Número médio de discos por máquina virtual compatível**: o número médio de discos calculados em todas as VMs compatíveis.

**Tamanho médio do disco (GB)** : o tamanho médio do disco calculado em todas as VMs compatíveis.

**RPO desejado (minutos)** : o objetivo do ponto de recuperação padrão ou o valor passado para o parâmetro ' DesiredRPO ' no momento da geração de relatórios para estimar a largura de banda necessária.

**Largura de banda desejada (Mbps)** : o valor que você passou para o parâmetro ' Bandwidth ' no momento da geração de relatórios para estimar o RPO atingível.

**Rotatividade de dados típica observada por dia (GB)** : a variação média de dados observada em todos os dias de criação de perfil. Esse número é usado como uma das entradas para decidir o número de servidores de configuração e servidores de processo adicionais a serem usados na implantação.

## <a name="recommendations"></a>Recomendações

A folha de recomendações do relatório do VMware para o Azure tem os seguintes detalhes de acordo com o RPO selecionado:

![Recomendações para o relatório do VMware para o Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Dados com perfil
![A exibição de dados com perfil no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Período de dados**analisado: o período durante o qual a criação de perfil foi executada. Por padrão, a ferramenta inclui todos os dados de criação de perfil no cálculo, a menos que ele gere o relatório para um período específico usando as opções StartDate e EndDate durante a geração do relatório.

**Nome do servidor**: o nome ou endereço IP do host VMware vCenter ou ESXi cujo relatório de VMs é gerado.

**RPO desejado**: o objetivo de ponto de recuperação para sua implantação. Por padrão, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na planilha. Se você tiver usado o parâmetro *DesiredRPOinMin* ao gerar o relatório, esse valor será mostrado no resultado de RPO desejado.

### <a name="profiling-overview"></a>Visão geral da criação de perfil

![Resultados da criação de perfil no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total de máquinas virtuais com perfil**criado: o número total de VMs cujos dados de perfil estão disponíveis. Se o VMListFile tiver nomes de quaisquer VMs que não foram criadas com perfil, essas VMs não serão consideradas na geração de relatórios e serão excluídas da contagem total de VMs com criação de perfil.

**Máquinas virtuais compatíveis**: o número de VMs que podem ser protegidas no Azure usando site Recovery. É o número total de VMs compatíveis para as quais a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos do Azure e o número de servidores de configuração e servidores de processo adicionais são calculados. Os detalhes de cada VM compatível estão disponíveis na seção "VMs compatíveis".

**Máquinas virtuais incompatíveis**: o número de VMs com perfil que são incompatíveis para proteção com site Recovery. Os motivos para incompatibilidade são indicados na seção "VMs incompatíveis". Se o VMListFile tiver nomes de quaisquer VMs que não foram criadas com perfil, essas VMs serão excluídas da contagem de VMs incompatíveis. Essas VMs são listadas como "dados não encontrados" no final da seção "VMs incompatíveis".

**RPO desejado**: seu objetivo de ponto de recuperação desejado, em minutos. O relatório é gerado para três valores de RPO: 15 (padrão), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base na sua seleção na lista suspensa RPO desejado no canto superior direito da planilha. Se você tiver gerado o relatório usando o parâmetro *-DesiredRPO* com um valor personalizado, esse valor personalizado será exibido como o padrão na lista suspensa RPO desejado.

### <a name="required-network-bandwidth-mbps"></a>Largura de banda de rede necessária (Mbps)

![Largura de banda de rede necessária no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Para atender ao RPO 100 por cento do tempo:** A largura de banda recomendada em Mbps a ser alocada para atender ao RPO desejado de 100% do tempo. Essa quantidade de largura de banda deve ser dedicada para a replicação delta de estado estável de todas as suas VMs compatíveis para evitar violações de RPO.

**Para atender ao rpo 90 por cento do tempo**: devido a preços de banda larga ou por qualquer outro motivo, se você não puder definir a largura de banda necessária para atender ao rpo desejado 100% do tempo, poderá optar por usar uma configuração de largura de banda menor que possa atender ao rpo desejado 90 p orcentagem do tempo. Para entender as implicações de definir essa largura de banda menor, o relatório fornece uma análise hipotética sobre o número e a duração das violações de RPO esperadas.

**Taxa de transferência obtida:** A taxa de transferência do servidor no qual você executou o comando getthroughput para a região Microsoft Azure em que a conta de armazenamento está localizada. Esse número de taxa de transferência indica o nível estimado que você pode obter ao proteger as VMs compatíveis usando Site Recovery, desde que o servidor de configuração ou as características de rede e o armazenamento do servidor de processo permaneçam iguais aos do servidor a partir da qual você executou a ferramenta.

Para replicação, você deve definir a largura de banda recomendada para atender ao RPO 100% do tempo. Depois de definir a largura de banda, se você não vir nenhum aumento na taxa de transferência obtida, conforme relatado pela ferramenta, faça o seguinte:

1. Verifique se há qualquer QoS (qualidade de serviço) de rede que esteja limitando Site Recovery taxa de transferência.

2. Verifique se seu cofre de Site Recovery está na região de Microsoft Azure mais próxima com suporte físico para minimizar a latência de rede.

3. Verifique suas características de armazenamento local para determinar se você pode melhorar o hardware (por exemplo, HDD para SSD).

4. Altere as configurações de Site Recovery no servidor de processo para [aumentar a quantidade de largura de banda de rede usada para replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se você estiver executando a ferramenta em um servidor de configuração ou em um servidor de processo que já tenha VMs protegidas, execute a ferramenta algumas vezes. O número da taxa de transferência obtida é alterado dependendo da quantidade de rotatividade sendo processada nesse ponto no tempo.

Para todas as implantações de Site Recovery empresarial, recomendamos que você use o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Contas de armazenamento necessárias
O gráfico a seguir mostra o número total de contas de armazenamento (Standard e Premium) que são necessárias para proteger todas as VMs compatíveis. Para saber qual conta de armazenamento usar para cada VM, consulte a seção "posicionamento de armazenamento de VM". Se você estiver usando a v 2.5 de Planejador de Implantações, essa recomendação mostrará apenas o número de contas de armazenamento de cache padrão que são necessárias para a replicação, uma vez que os dados estão sendo gravados diretamente no Managed Disks.

![Contas de armazenamento necessárias no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Número necessário de núcleos do Azure
Esse resultado é o número total de núcleos a serem configurados antes do failover ou failover de teste de todas as VMs compatíveis. Se houver poucos núcleos disponíveis na assinatura, Site Recovery falha ao criar VMs no momento do failover de teste ou do failover.

![Número necessário de núcleos do Azure no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Infraestrutura local necessária
Esta figura é o número total de servidores de configuração e servidores de processo adicionais a serem configurados que seriam suficientes para proteger todas as VMs compatíveis. Dependendo das recomendações de tamanho com suporte [para o servidor de configuração](https://aka.ms/asr-v2a-on-prem-components), a ferramenta pode recomendar servidores adicionais. A recomendação baseia-se no maior da rotatividade por dia ou no número máximo de VMs protegidas (supondo uma média de três discos por VM), o que for atingido primeiro no servidor de configuração ou no servidor de processo adicional. Você encontrará os detalhes da rotatividade total por dia e o número total de discos protegidos na seção "resumo local".

![Infraestrutura local necessária no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Análise de hipóteses
Essa análise descreve quantas violações podem ocorrer durante o período de criação de perfil quando você define uma largura de banda menor para que o RPO desejado seja atendido somente 90% do tempo. Uma ou mais violações de RPO podem ocorrer em um determinado dia. O grafo mostra o RPO de pico do dia.
Com base nessa análise, você pode decidir se o número de violações de RPO em todos os dias e o pico de impacto de RPO por dia é aceitável com a menor largura de banda especificada. Se for aceitável, você poderá alocar a menor largura de banda para replicação. caso contrário, aloque a maior largura de banda sugerida para atender ao RPO desejado de 100% do tempo.

![Análise de hipóteses no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Tamanho de lote de VM recomendado para a replicação inicial
Nesta seção, recomendamos o número de VMs que podem ser protegidas em paralelo para concluir a replicação inicial dentro de 72 horas com a largura de banda sugerida para atender ao RPO desejado 100% do tempo que está sendo definido. Esse valor é um valor configurável. Para alterá-lo na hora da geração do relatório, use o parâmetro *GoalToCompleteIR* .

O gráfico aqui mostra um intervalo de valores de largura de banda e uma contagem de tamanho de lote de VM calculada para concluir a replicação inicial em 72 horas, com base no tamanho médio de VM detectado em todas as VMs compatíveis.

Na visualização pública, o relatório não especifica quais VMs devem ser incluídas em um lote. Você pode usar o tamanho do disco mostrado na seção "VMs compatíveis" para localizar o tamanho de cada VM e selecioná-las para um lote, ou você pode selecionar as VMs com base em características de carga de trabalho conhecidas. O tempo de conclusão das alterações de replicação inicial proporcionalmente, com base no tamanho real do disco da VM, no espaço em disco usado e na taxa de transferência de rede disponível.

![Tamanho de lote de VM recomendado](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Estimativa de custo
O grafo mostra a exibição resumida do custo total de recuperação de desastre (DR) estimado para o Azure de sua região de destino escolhida e a moeda que você especificou para a geração de relatórios.

![Resumo de estimativa de custo](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda a entender o custo de que você precisa para pagar pelo armazenamento, computação, rede e licença ao proteger todas as suas VMs compatíveis com o Azure usando o Azure Site Recovery. O custo é calculado em VMs compatíveis e não em todas as VMs com perfil.  

Você pode exibir o custo mensal ou anual. Saiba mais sobre as [regiões de destino com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e as [moedas com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes** O custo total de recuperação de desastres é dividido em quatro componentes: custo da licença de computação, armazenamento, rede e Azure Site Recovery. O custo é calculado com base no consumo que será incorrido durante a replicação e no tempo de análise da recuperação de desastre para computação, armazenamento (Premium e Standard), ExpressRoute/VPN configurado entre o site local e o Azure e Azure Site Recovery licença.

**Custo por Estados** O custo total de recuperação de desastre (DR) é categorias baseadas em dois Estados diferentes – replicação e análise de DR.

**Custo de replicação**: o custo que será incorrido durante a replicação. Ele abrange o custo de armazenamento, rede e licença de Azure Site Recovery.

**Custo de análise de recuperação de desastre**: o custo que será incorrido durante os failovers de teste. Azure Site Recovery gira as VMs durante o failover de teste. O custo de análise de DR cobre o custo de computação e armazenamento das VMs em execução.

**Custo de armazenamento do Azure por mês/ano** Ele mostra o custo total de armazenamento que será incorrido para o armazenamento Premium e Standard para replicação e análise de DR.
Você pode exibir a análise de custo detalhada por VM na folha de [estimativa de custo](site-recovery-vmware-deployment-planner-cost-estimation.md) .

### <a name="growth-factor-and-percentile-values-used"></a>Fator de crescimento e valores de percentil usados
Esta seção na parte inferior da planilha mostra o valor de percentil usado para todos os contadores de desempenho das VMs com perfil criado (o padrão é 95 º percentil) e o fator de crescimento (o padrão é 30%) usado em todos os cálculos.

![Fator de crescimento e valores de percentil usados](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações com largura de banda disponível como entrada

![Recomendações com largura de banda disponível como entrada](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Você pode ter uma situação em que você sabe que não é possível definir uma largura de banda de mais de x Mbps para replicação de Site Recovery. A ferramenta permite que você insira a largura de banda disponível (usando o parâmetro-Bandwidth durante a geração de relatórios) e obtenha o RPO atingível em minutos. Com esse valor de RPO atingível, você pode decidir se precisa configurar largura de banda adicional ou se está certo de ter uma solução de recuperação de desastre com esse RPO.

![RPO atingível para largura de banda de 500 Mbps](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Posicionamento de armazenamento de VM

>[!Note]
>O Planejador de Implantações v 2.5 em diante recomenda o posicionamento de armazenamento para computadores que serão replicados diretamente em discos gerenciados.

![Posicionamento de armazenamento de VM](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Tipo de armazenamento de replicação**: um disco gerenciado Standard ou Premium, que é usado para replicar todas as VMs correspondentes mencionadas na coluna **VMs para o local** .

**Tipo de conta de armazenamento de log**: todos os logs de replicação são armazenados em uma conta de armazenamento padrão.

**Prefixo sugerido para a conta de armazenamento**: o prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento em cache. Você pode usar seu próprio prefixo, mas a sugestão da ferramenta segue a [Convenção de nomenclatura de partição para contas de armazenamento](https://aka.ms/storage-performance-checklist).

**Nome da conta de log sugerido**: o nome da conta de armazenamento depois que você inclui o prefixo sugerido. Substitua o nome entre os colchetes angulares (< e >) pela sua entrada personalizada.

**Resumo de posicionamento**: um resumo dos discos necessários para VMs protegidas por tipo de armazenamento. Ele inclui o número total de VMs, o tamanho total provisionado em todos os discos e o número total de discos.

**Máquinas virtuais para colocar**: uma lista de todas as VMs que devem ser colocadas na conta de armazenamento determinada para otimizar o desempenho e o uso.

## <a name="compatible-vms"></a>VMs compatíveis
![Planilha do Excel de VMs compatíveis](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Nome da VM**: o nome da VM ou o endereço IP que é usado no VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VMDKs) que são anexados às VMs. Para distinguir VMs do vCenter com nomes duplicados ou endereços IP, os nomes incluem o nome do host ESXi. O host ESXi listado é aquele em que a VM foi colocada quando a ferramenta foi descoberta durante o período de criação de perfil.

**Compatibilidade de VM**: os valores são **sim** e **Sim** \*. **Sim** \* é para instâncias nas quais a VM é uma opção para [SSDs Premium](../virtual-machines/windows/disks-types.md). Aqui, o disco de alta rotatividade ou IOPS com perfil se ajusta na categoria P20 ou p30, mas o tamanho do disco faz com que ele seja mapeado para um P10 ou P20. A conta de armazenamento decide em qual tipo de disco de armazenamento Premium o qual mapear um disco, com base em seu tamanho. Por exemplo:
* < 128 GB é um P10.
* 128 GB a 256 GB é um P15
* 256 GB a 512 GB é um P20.
* 512 GB a 1024 GB é um p30.
* 1025 GB a 2048 GB é um P40.
* 2049 GB a 4095 GB é um P50.

Por exemplo, se as características de carga de trabalho de um disco o colocarem na categoria P20 ou p30, mas o tamanho o mapear para um tipo de disco de armazenamento Premium inferior, a ferramenta marcará essa VM como **sim** \*. A ferramenta também recomenda que você altere o tamanho do disco de origem para se ajustar ao tipo de disco de armazenamento Premium recomendado ou altere o tipo de disco de destino após o failover.

**Tipo de armazenamento**: Standard ou Premium.

**Asrseeddisk (disco gerenciado) criado para replicação**: o nome do disco que é criado quando você habilita a replicação. Ele armazena os dados e seus instantâneos no Azure.

**IOPS de l/g de pico (com fator de crescimento)** : IOPS de leitura/gravação de carga de trabalho de pico no disco (o padrão é o 95 º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que o IOPS de leitura/gravação total de uma VM nem sempre é a soma do IOPS de leitura/gravação dos discos individuais da VM, pois o IOPS de leitura/gravação de pico da VM é o pico da soma dos IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação de dados de pico em Mbps (com fator de crescimento)** : a taxa de variação de pico no disco (o padrão é o 95 º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que a rotatividade total de dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM, pois a variação de dados de pico da VM é o pico da soma da rotatividade de seus discos individuais durante cada minuto do período de criação de perfil.

**Tamanho da VM do Azure**: o tamanho ideal de máquina virtual dos serviços de nuvem do Azure mapeado para essa VM local. O mapeamento é baseado na memória da VM local, no número de discos/núcleos/NICs e no IOPS de leitura/gravação. A recomendação é sempre o menor tamanho de VM do Azure que corresponde a todas as características de VM local.

**Número de discos**: o número total de discos de máquina virtual (VMDKs) na VM.

**Tamanho do disco (GB)** : o tamanho total da configuração de todos os discos da VM. A ferramenta também mostra o tamanho do disco para os discos individuais na VM.

**Núcleos**: o número de núcleos de CPU na VM.

**Memória (MB)** : a RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: tipo de inicialização da VM. Pode ser BIOS ou EFI.  Atualmente Azure Site Recovery dá suporte a VMs EFI do Windows Server (Windows Server 2012, 2012 R2 e 2016) desde que o número de partições no disco de inicialização seja inferior a 4 e o tamanho do setor de inicialização seja de 512 bytes. Para proteger VMs EFI, Azure Site Recovery versão do serviço de mobilidade deve ser 9,13 ou superior. Somente o failover tem suporte para VMs EFI. Não há suporte para failback.  

**Tipo de so**: é o tipo de so da VM. Ele pode ser Windows ou Linux ou outro com base no modelo escolhido de VMware vSphere ao criar a VM.  

## <a name="incompatible-vms"></a>VMs incompatíveis

![Planilha do Excel de VMs incompatíveis
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Nome da VM**: o nome da VM ou o endereço IP que é usado no VMListFile quando um relatório é gerado. Essa coluna também lista os VMDKs que são anexados às VMs. Para distinguir VMs do vCenter com nomes duplicados ou endereços IP, os nomes incluem o nome do host ESXi. O host ESXi listado é aquele em que a VM foi colocada quando a ferramenta foi descoberta durante o período de criação de perfil.

**Compatibilidade de VM**: indica por que a VM fornecida é incompatível para uso com site Recovery. Os motivos são descritos para cada disco incompatível da VM e, com base nos [limites de armazenamento](https://aka.ms/azure-storage-scalbility-performance)publicados, pode ser qualquer um dos seguintes:

* O tamanho do disco é > 4095 GB. Atualmente, o armazenamento do Azure não dá suporte a tamanhos de disco de dados maiores que 4095 GB.

* O disco do sistema operacional é > 2048 GB. Atualmente, o armazenamento do Azure não dá suporte ao tamanho de disco do sistema operacional maior que 2048 GB.

* O tamanho total da VM (replicação + TFO) excede o limite de tamanho de conta de armazenamento com suporte (35 TB). Essa incompatibilidade geralmente ocorre quando um único disco na VM tem uma característica de desempenho que excede o limite máximo com suporte do Azure ou Site Recovery para o armazenamento Standard. Essa instância envia a VM para a zona de armazenamento Premium. No entanto, o tamanho máximo com suporte de uma conta de armazenamento Premium é de 35 TB e uma única VM protegida não pode ser protegida em várias contas de armazenamento. Observe também que, quando um failover de teste é executado em uma VM protegida, ele é executado na mesma conta de armazenamento em que a replicação está progredindo. Nessa instância, configure 2x o tamanho do disco para que a replicação funcione e o failover de teste seja bem sucedido em paralelo.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 7500 por disco.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 80.000 por VM.

* A rotatividade média de dados excede o limite de variação de dados Site Recovery com suporte de 20 MB/s para o tamanho médio de e/s para o disco.

* A rotatividade média de dados excede o limite de variação de dados Site Recovery com suporte de 25 MB/s para o tamanho médio de e/s para a VM (soma de toda a rotatividade de discos).

* A rotatividade de dados de pico em todos os discos na VM excede o máximo com suporte Site Recovery limite de variação de dados de pico de 54 MB/s por VM.

* O IOPS de gravação efetivo médio excede o limite de IOPS de 840 com Site Recovery suporte para o disco.

* O armazenamento de instantâneos calculado excede o limite de armazenamento de instantâneos com suporte de 10 TB.

* A rotatividade total de dados por dia excede o limite de rotatividade por dia de 2 TB com suporte por um servidor de processo.


**IOPS de l/g de pico (com fator de crescimento)** : o pico de IOPS de carga de trabalho no disco (o padrão é o 95 º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que o IOPS de leitura/gravação total da VM nem sempre é a soma do IOPS de leitura/gravação dos discos individuais da VM, pois o IOPS de leitura/gravação de pico da VM é o pico da soma dos IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação de dados de pico em Mbps (com fator de crescimento)** : a taxa de variação de pico no disco (padrão 95 º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que a rotatividade total de dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM, pois a variação de dados de pico da VM é o pico da soma da rotatividade de seus discos individuais durante cada minuto do período de criação de perfil.

**Número de discos**: o número total de VMDKs na VM.

**Tamanho do disco (GB)** : o tamanho total da configuração de todos os discos da VM. A ferramenta também mostra o tamanho do disco para os discos individuais na VM.

**Núcleos**: o número de núcleos de CPU na VM.

**Memória (MB)** : a quantidade de RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: tipo de inicialização da VM. Pode ser BIOS ou EFI.  Atualmente Azure Site Recovery dá suporte a VMs EFI do Windows Server (Windows Server 2012, 2012 R2 e 2016) desde que o número de partições no disco de inicialização seja inferior a 4 e o tamanho do setor de inicialização seja de 512 bytes. Para proteger VMs EFI, Azure Site Recovery versão do serviço de mobilidade deve ser 9,13 ou superior. Somente o failover tem suporte para VMs EFI. Não há suporte para failback.

**Tipo de so**: é o tipo de so da VM. Ele pode ser Windows ou Linux ou outro com base no modelo escolhido de VMware vSphere ao criar a VM.

## <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites de Azure Site Recovery. Esses limites se baseiam em nossos testes, mas não podem abranger todas as combinações de e/s de aplicativo possíveis. Os resultados reais podem variar com base na combinação de e/s do aplicativo. Para obter melhores resultados, mesmo após o planejamento da implantação, sempre recomendamos que você execute testes de aplicativos extensivos emitindo um failover de teste para obter a imagem de desempenho real do aplicativo.

**Destino de armazenamento de replicação** | **Tamanho médio de e/s do disco de origem** |**Variação média de dados do disco de origem** | **Total de rotatividade de dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou mais | 8 MB/s | 672 GB por disco
Disco Premium P20 ou p30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou p30 ou P40 ou P50 | 16 KB ou mais | 20 MB/s | 1684 GB por disco

**Rotatividade de dados de origem** | **Limite máximo**
---|---
Variação de dados de pico em todos os discos em uma VM | 54 MB/s
Rotatividade máxima de dados por dia com suporte de um servidor de processo | 2 TB

Esses são números médios supondo uma sobreposição de e/s de 30%. Site Recovery é capaz de lidar com a taxa de transferência mais alta com base na taxa de sobreposição, tamanhos de gravação maiores e comportamento de e/s de carga de trabalho real. Os números anteriores pressupõem uma pendência típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados e um ponto de recuperação é criado dentro de cinco minutos.


## <a name="cost-estimation"></a>Estimativa de custo
Saiba mais sobre a [estimativa de custo](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a [estimativa de custo](site-recovery-vmware-deployment-planner-cost-estimation.md).
