---
title: Analisar o relatório de Planejador de Implantações do Azure Site Recovery para recuperação de desastre de VMs do Hyper-V para o Azure | Microsoft Docs
description: Este artigo descreve como analisar um relatório gerado pelo Planejador de Implantações do Azure Site Recovery para recuperação de desastres de VMs do Hyper-V para o Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: aafeeb59446ac914bba25874f74871fc5f189498
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693581"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analisar o relatório de Planejador de Implantações do Azure Site Recovery
Este artigo discute as planilhas contidas no relatório do Excel geradas pelo Planejador de Implantações do Azure Site Recovery para um cenário do Hyper-V para o Azure.

## <a name="on-premises-summary"></a>Resumo local
A planilha de resumo local fornece uma visão geral do ambiente do Hyper-V com perfil.

![Resumo local](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Data de início** e **data de término**: as datas de início e de término dos dados de criação de perfil considerados para geração de relatórios. Por padrão, a data de início é a data de início da criação de perfil e a data de término é a data em que a criação de perfil é interrompida. Essas informações podem ser os valores de "StartDate" e "EndDate" se o relatório for gerado com esses parâmetros.

**Número total de dias de criação de perfil**: o número total de dias de criação de perfil entre as datas de início e término para as quais o relatório é gerado.

**Número de máquinas virtuais compatíveis**: o número total de VMs compatíveis para as quais a largura de banda de rede necessária, o número necessário de contas de armazenamento e os núcleos do Azure são calculados.

**Número total de discos em todas as máquinas virtuais compatíveis**: o número total de discos em todas as VMs compatíveis.

**Número médio de discos por máquina virtual compatível**: o número médio de discos calculados em todas as VMs compatíveis.

**Tamanho médio do disco (GB)** : o tamanho médio do disco calculado em todas as VMs compatíveis.

**RPO desejado (minutos)** : o objetivo do ponto de recuperação padrão ou o valor passado para o parâmetro "DesiredRPO" no momento da geração do relatório para estimar a largura de banda necessária.

**Largura de banda desejada (Mbps)** : o valor que você passou para o parâmetro "largura de banda" no momento da geração de relatórios para estimar o RPO (objetivo de ponto de recuperação) atingível.

**Rotatividade de dados típica observada por dia (GB)** : a variação média de dados observada em todos os dias de criação de perfil.

## <a name="recommendations"></a>Recomendações 
A folha de recomendações do Azure para o relatório do Hyper-V tem os seguintes detalhes de acordo com o RPO selecionado:

![Recomendações do Hyper-V para o relatório do Azure](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Dados de perfil
![Dados de perfil](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Período de dados**analisado: o período durante o qual a criação de perfil foi executada. Por padrão, a ferramenta inclui todos os dados de perfil no cálculo. Se você usou a opção StartDate e EndDate na geração de relatórios, ele gera o relatório para o período específico. 

**Número de servidores Hyper-v com perfil**criado: o número de servidores Hyper-v cujo relatório de VMs é gerado. Selecione o número para exibir o nome dos servidores Hyper-V. A folha de requisitos de armazenamento local é aberta para mostrar todos os servidores junto com seus requisitos de armazenamento. 

**RPO desejado**: o objetivo de ponto de recuperação para sua implantação. Por padrão, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na planilha. Se você usou o parâmetro DesiredRPOinMin ao gerar o relatório, esse valor será mostrado no resultado de RPO desejado.

### <a name="profiling-overview"></a>Visão geral da criação de perfil
![Visão geral da criação de perfil](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Total de máquinas virtuais com perfil**criado: o número total de VMs cujos dados de perfil estão disponíveis. Se o VMListFile tiver nomes de qualquer VM que não tenha sido criado com perfil, essas VMs não serão consideradas na geração de relatórios e serão excluídas da contagem total de VMs com criação de perfil.

**Máquinas virtuais compatíveis**: o número de VMs que podem ser protegidas no Azure usando Azure site Recovery. É o número total de VMs compatíveis para as quais a largura de banda de rede necessária, o número de contas de armazenamento e o número de núcleos do Azure são calculados. Os detalhes de cada VM compatível estão disponíveis na seção "VMs compatíveis".

**Máquinas virtuais incompatíveis**: o número de VMs com perfil que são incompatíveis para proteção com site Recovery. Os motivos para incompatibilidade são indicados na seção "VMs incompatíveis". Se o VMListFile tiver nomes de qualquer VM que não tenha sido criado com perfil, essas VMs serão excluídas da contagem de VMs incompatíveis. Essas VMs são listadas como "dados não encontrados" no final da seção "VMs incompatíveis".

**RPO desejado**: seu objetivo de ponto de recuperação desejado, em minutos. O relatório é gerado para três valores de RPO: 15 (padrão), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base na sua seleção na lista suspensa **RPO desejado** no canto superior direito da planilha. Se você gerou o relatório usando o parâmetro-DesiredRPO com um valor personalizado, esse valor personalizado será exibido como o padrão na lista suspensa **RPO desejado** .

### <a name="required-network-bandwidth-mbps"></a>Largura de banda de rede necessária (Mbps)
![Largura de banda de rede necessária](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Para atender ao rpo 100% do tempo**: a largura de banda recomendada em Mbps a ser alocada para atender ao rpo desejado 100% do tempo. Essa quantidade de largura de banda deve ser dedicada para a replicação delta de estado estável de todas as suas VMs compatíveis para evitar violações de RPO.

**Para atender ao rpo 90% do tempo**: Talvez devido a preços de banda larga ou outro motivo, você não pode definir a largura de banda necessária para atender ao RPO desejado 100% do tempo. Se esse for o caso, você poderá usar uma configuração de largura de banda menor que possa atender ao RPO desejado de 90% do tempo. Para entender as implicações de definir essa largura de banda menor, o relatório fornece uma análise hipotética sobre o número e a duração das violações de RPO esperadas.

**Taxa de transferência obtida**: a taxa de transferência do servidor no qual você executa o comando getthroughput para a região do Azure onde a conta de armazenamento está localizada. Esse número de taxa de transferência indica o nível estimado que você pode obter ao proteger as VMs compatíveis usando Site Recovery. As características de armazenamento e de rede do servidor Hyper-V devem permanecer as mesmas do servidor do qual você executa a ferramenta.

Para todas as implantações de Site Recovery empresarial, recomendamos que você use o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Contas de armazenamento necessárias
O gráfico a seguir mostra o número total de contas de armazenamento (Standard e Premium) que são necessárias para proteger todas as VMs compatíveis. Para saber qual conta de armazenamento usar para cada VM, consulte a seção "posicionamento de armazenamento de VM".

![Contas de armazenamento do Azure necessárias](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Número necessário de núcleos do Azure
Esse resultado é o número total de núcleos a serem configurados antes do failover ou failover de teste de todas as VMs compatíveis. Se houver poucos núcleos disponíveis na assinatura, Site Recovery falha ao criar VMs no momento do failover de teste ou do failover.

![Número necessário de núcleos do Azure](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Requisito de armazenamento local adicional

O armazenamento livre total necessário em servidores Hyper-V para replicação inicial e replicação delta bem-sucedidas para garantir que a replicação da VM não cause nenhum tempo de inatividade indesejável para seus aplicativos de produção. Mais informações sobre cada requisito de volume estão disponíveis no [requisito de armazenamento local](#on-premises-storage-requirement). 

Para entender por que o espaço livre é necessário para a replicação, consulte a seção [requisito de armazenamento local](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) .

![Requisito de armazenamento local e frequência de cópia](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Frequência máxima de cópia
A frequência de cópia máxima recomendada deve ser definida para que a replicação alcance o RPO desejado. O padrão é cinco minutos. Você pode definir a frequência de cópia como 30 segundos para obter um RPO melhor.

### <a name="what-if-analysis"></a>Análise de hipóteses
![What-If-Analysis ](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) esta análise descreve quantas violações podem ocorrer durante o período de criação de perfil quando você define uma largura de banda menor para que o RPO desejado seja atendido somente 90% do tempo. Uma ou mais violações de RPO podem ocorrer em um determinado dia. O grafo mostra o RPO de pico do dia. Com base nessa análise, você pode decidir se o número de violações de RPO em todos os dias e o pico de impacto de RPO por dia é aceitável com a menor largura de banda especificada. Se for aceitável, você poderá alocar a menor largura de banda para replicação. Se for inaceitável, aloque mais largura de banda como sugerida para atender ao RPO desejado de 100% do tempo. 

### <a name="recommendation-for-successful-initial-replication"></a>Recomendação para replicação inicial bem-sucedida
Esta seção discute o número de lotes nos quais as VMs devem ser protegidas e a largura de banda mínima necessária para concluir a replicação inicial (IR) com êxito. 

![Envio em lote de IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

As VMs devem ser protegidas na ordem de lote determinada. Cada lote tem uma lista específica de VMs. As VMs do lote 1 devem ser protegidas antes das VMs do lote 2. As VMs do lote 2 devem ser protegidas antes das VMs do lote 3 e assim por diante. Depois que a replicação inicial das VMs do lote 1 for concluída, você poderá habilitar a replicação para VMs do lote 2. Da mesma forma, depois que a replicação inicial das VMs do lote 2 for concluída, você poderá habilitar a replicação para VMs do lote 3 e assim por diante. 

Se a ordem do lote não for seguida, a largura de banda suficiente para a replicação inicial poderá não estar disponível para as VMs que são protegidas posteriormente. O resultado é que as VMs nunca terminam a replicação inicial ou algumas VMs protegidas podem entrar no modo de ressincronização. O envio em lote do IR para a planilha de RPO selecionada tem as informações detalhadas sobre quais VMs devem ser incluídas em cada lote.

O gráfico aqui mostra a distribuição de largura de banda para replicação inicial e replicação delta entre lotes na ordem de lote determinada. Quando você protege o primeiro lote de VMs, a largura de banda completa está disponível para replicação inicial. Após a conclusão da replicação inicial para o primeiro lote, parte da largura de banda é necessária para a replicação delta. A largura de banda restante está disponível para a replicação inicial do segundo lote de VMs. 

A barra do lote 2 mostra a largura de banda de replicação delta necessária para VMs do lote 1 e a largura de banda disponível para a replicação inicial para VMs do lote 2. Da mesma forma, a barra do lote 3 mostra a largura de banda necessária para a replicação delta para lotes anteriores (VMs do lote 1 e do lote 2) e a largura de banda disponível para a replicação inicial do lote 3, e assim por diante. Depois que a replicação inicial de todos os lotes for concluída, a última barra mostrará a largura de banda necessária para a replicação delta para todas as VMs protegidas.

**Por que preciso de envio em lote de replicação inicial?**
O tempo de conclusão da replicação inicial é baseado no tamanho do disco da VM, no espaço em disco usado e na taxa de transferência de rede disponível. O detalhe está disponível em envio em lote de IR para uma planilha de RPO selecionada.

### <a name="cost-estimation"></a>Estimativa de custo
O grafo mostra a exibição resumida do custo total de recuperação de desastre (DR) estimado para o Azure de sua região de destino escolhida e a moeda que você especificou para a geração de relatórios.

![Resumo de estimativa de custo](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

O resumo ajuda a entender o custo de que você precisa para pagar pelo armazenamento, computação, rede e licenciamento ao proteger todas as suas VMs compatíveis com o Azure usando Site Recovery. O custo é calculado para VMs compatíveis e não para todas as VMs com perfil. 
 
Você pode exibir o custo mensal ou anual. Saiba mais sobre as [regiões de destino com suporte](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) e as [moedas com suporte](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes**: o custo total de recuperação de desastres é dividido em quatro componentes: custo de licença de computação, armazenamento, rede e site Recovery. O custo é calculado com base no consumo incorrido durante a replicação e no tempo de análise de DR. Computação, armazenamento (Premium e Standard), ExpressRoute/VPN configurados entre o site local e o Azure, e a licença de Site Recovery são usadas para os cálculos.

**Custo por Estados**: o custo total de recuperação de desastres é categorizado com base em dois Estados diferentes: replicação e análise de Dr. 

**Custo de replicação**: o custo incorrido durante a replicação. Ele abrange o custo de armazenamento, rede e licença de Site Recovery. 

**Custo de análise de recuperação de desastre**: o custo incorrido durante os failovers de teste. Site Recovery gira as VMs durante o failover de teste. O custo de análise de DR cobre o custo de computação e armazenamento das VMs em execução. 

**Custo de armazenamento do Azure por mês/ano**: o gráfico de barras mostra o custo total de armazenamento incorrido para o armazenamento Premium e Standard para replicação e análise de Dr. Você pode exibir a análise de custo detalhada por VM na folha de [estimativa de custo](hyper-v-deployment-planner-cost-estimation.md) .

### <a name="growth-factor-and-percentile-values-used"></a>Fator de crescimento e valores de percentil usados
Esta seção na parte inferior da planilha mostra o valor de percentil usado para todos os contadores de desempenho das VMs com perfil criado (o padrão é o 95 º percentil). Ele também mostra o fator de crescimento (o padrão é 30%) usado em todos os cálculos.

![Fator de crescimento e valores de percentil usados](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações com largura de banda disponível como entrada
![Visão geral da criação de perfil com entrada de largura de banda](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Você pode ter uma situação em que você sabe que não é possível definir uma largura de banda de mais de x Mbps para replicação de Site Recovery. Você pode usar a ferramenta para inserir a largura de banda disponível (usando o parâmetro-Bandwidth durante a geração de relatórios) e obter o RPO atingível em minutos. Com esse valor de RPO atingível, você pode decidir se precisa provisionar largura de banda adicional ou se está satisfeito com uma solução de recuperação de desastres com esse RPO.

![RPO atingível](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Recomendação de posicionamento de armazenamento de VM 
![Posicionamento de armazenamento de VM](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Tipo de armazenamento em disco**: uma conta de armazenamento Standard ou Premium, que é usada para replicar todas as VMs correspondentes mencionadas na coluna **VMs para o local** .

**Prefixo sugerido**: o prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento. Você pode usar seu próprio prefixo, mas a sugestão da ferramenta segue a [Convenção de nomenclatura de partição para contas de armazenamento](https://aka.ms/storage-performance-checklist).

**Nome da conta sugerido**: o nome da conta de armazenamento depois que você inclui o prefixo sugerido. Substitua o nome entre os colchetes angulares (< e >) pela sua entrada personalizada.

**Conta de armazenamento de log**: todos os logs de replicação são armazenados em uma conta de armazenamento padrão. Para VMs que são replicadas para uma conta de armazenamento Premium, configure uma conta de armazenamento padrão adicional para o armazenamento de log. Uma única conta de armazenamento de log padrão pode ser usada por várias contas de armazenamento de replicação Premium. As VMs que são replicadas para contas de armazenamento Standard usam a mesma conta de armazenamento para logs.

**Nome da conta de log sugerido**: o nome da conta de log de armazenamento depois de incluir o prefixo sugerido. Substitua o nome entre os colchetes angulares (< e >) pela sua entrada personalizada.

**Resumo de posicionamento**: um resumo da carga total das VMs na conta de armazenamento no momento da replicação e do failover de teste ou do failover. O resumo inclui:

* Número total de VMs mapeadas para a conta de armazenamento. 
* IOPS de leitura/gravação total em todas as VMs que estão sendo colocadas nesta conta de armazenamento.
* IOPS de gravação total (replicação).
* Tamanho total da instalação em todos os discos.
* Número total de discos.

**VMs para colocar**: uma lista de todas as VMs que devem ser colocadas na conta de armazenamento determinada para otimizar o desempenho e o uso.

## <a name="compatible-vms"></a>VMs compatíveis
O relatório do Excel gerado pelo Site Recovery Planejador de Implantações fornece todos os detalhes de VMs compatíveis na planilha "VMs compatíveis".

![VMs compatíveis](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Nome da VM**: o nome da VM que é usado no VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VHDs) que são anexados às VMs. Os nomes incluem os nomes de host do Hyper-V em que as VMs foram colocadas quando a ferramenta as descobriu durante o período de criação de perfil.

**Compatibilidade de VM**: os valores são **sim** e **Sim** \*. **Sim** \* é para instâncias nas quais a VM é uma opção para [SSDs Premium](../virtual-machines/windows/disks-types.md). Aqui, o disco de alta rotatividade ou IOPS com perfil se ajusta em um tamanho de disco Premium superior ao tamanho mapeado para o disco. A conta de armazenamento decide em qual tipo de disco de armazenamento Premium o qual mapear um disco, com base em seu tamanho: 
* < 128 GB é um P10.
* 128 GB a 256 GB é um P15.
* 256 GB a 512 GB é um P20.
* 512 GB a 1.024 GB é um p30.
* 1\.025 GB a 2.048 GB é um P40.
* 2\.049 GB a 4.095 GB é um P50.

Por exemplo, se as características de carga de trabalho de um disco o colocarem na categoria P20 ou p30, mas o tamanho o mapear para um tipo de disco de armazenamento Premium inferior, a ferramenta marcará essa VM como **sim** \*. A ferramenta também recomenda que você altere o tamanho do disco de origem para se ajustar ao tipo de disco de armazenamento Premium recomendado ou altere o tipo de disco de destino após o failover.

**Tipo de armazenamento**: Standard ou Premium.

**Prefixo sugerido**: o prefixo de conta de armazenamento de três caracteres.

**Conta de armazenamento**: o nome que usa o prefixo de conta de armazenamento sugerido.

**IOPS de l/g de pico (com fator de crescimento)** : IOPS de leitura/gravação de carga de trabalho de pico no disco (o padrão é o 95 º percentil) junto com o fator de crescimento futuro (o padrão é 30%). O IOPS de leitura/gravação total de uma VM nem sempre é a soma do IOPS de leitura/gravação dos discos individuais da VM. O IOPS de leitura/gravação de pico da VM é o pico da soma dos IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação de dados de pico em MB/s (com fator de crescimento)** : a taxa de variação de pico no disco (o padrão é o 95 º percentil), juntamente com o fator de crescimento futuro (o padrão é 30%). A rotatividade total de dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM. A rotatividade de dados de pico da VM é o pico da soma da rotatividade de seus discos individuais durante cada minuto do período de criação de perfil.

**Tamanho da VM do Azure**: o tamanho ideal da VM dos serviços de nuvem do Azure mapeados para essa VM local. O mapeamento é baseado na memória da VM local, no número de discos/núcleos/NICs e no IOPS de leitura/gravação. A recomendação é sempre o menor tamanho de VM do Azure que corresponde a todas as características de VM local.

**Número de discos**: o número total de VHDs (discos de máquina virtual) na VM.

**Tamanho do disco (GB)** : o tamanho total de todos os discos da VM. A ferramenta também mostra o tamanho do disco para os discos individuais na VM.

**Núcleos**: o número de núcleos de CPU na VM.

**Memória (MB)** : a RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: o tipo de inicialização da VM. Pode ser BIOS ou EFI.

## <a name="incompatible-vms"></a>VMs incompatíveis
O relatório do Excel gerado pelo Site Recovery Planejador de Implantações fornece todos os detalhes de VMs incompatíveis na planilha "VMs incompatíveis".

![VMs incompatíveis](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Nome da VM**: o nome da VM que é usado no VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VHDs) que são anexados às VMs. Os nomes incluem os nomes de host do Hyper-V em que as VMs foram colocadas quando a ferramenta as descobriu durante o período de criação de perfil.

**Compatibilidade de VM**: indica por que a VM fornecida é incompatível para uso com site Recovery. Os motivos são descritos para cada disco incompatível da VM e, com base nos [limites de armazenamento](https://aka.ms/azure-storage-scalbility-performance)publicados, pode ser qualquer um dos seguintes:

* O tamanho do disco é maior que 4.095 GB. Atualmente, o armazenamento do Azure não dá suporte a tamanhos de disco de dados maiores que 4.095 GB.

* O disco do sistema operacional é maior que 2.047 GB para a VM de geração 1 (tipo de inicialização do BIOS). Site Recovery não dá suporte ao tamanho de disco do sistema operacional maior que 2.047 GB para VMs de geração 1.

* O disco do sistema operacional é maior que 300 GB para a VM de geração 2 (tipo de inicialização EFI). Site Recovery não dá suporte ao tamanho de disco do sistema operacional maior que 300 GB para VMs de geração 2.

* Não há suporte para um nome de VM com nenhum dos seguintes caracteres: "" [] '. A ferramenta não pode obter dados de perfil para VMs que tenham qualquer um desses caracteres em seus nomes. 

* Um VHD é compartilhado por duas ou mais VMs. O Azure não dá suporte a VMs com um VHD compartilhado.

* Não há suporte para uma VM com Fiber Channel virtual. Site Recovery não dá suporte a VMs com Fiber Channel virtual.

* Um cluster Hyper-V não contém um agente de replicação. Site Recovery não oferece suporte a uma VM em um cluster Hyper-V se o agente de réplica do Hyper-V não estiver configurado para o cluster.

* Uma VM não está altamente disponível. O Site Recovery não dá suporte a uma VM de um nó de cluster do Hyper-V cujos VHDs são armazenados no disco local, e não no disco de cluster. 

* O tamanho total da VM (replicação + failover de teste) excede o limite de tamanho de conta de armazenamento Premium com suporte (35 TB). Essa incompatibilidade geralmente ocorre quando um único disco na VM tem uma característica de desempenho que excede o limite máximo com suporte do Azure ou Site Recovery para o armazenamento Standard. Essa instância envia a VM para a zona de armazenamento Premium. No entanto, o tamanho máximo com suporte de uma conta de armazenamento Premium é de 35 TB. Uma única VM protegida não pode ser protegida em várias contas de armazenamento. 

    Quando um failover de teste é executado em uma VM protegida e, se um disco não gerenciado estiver configurado para failover de teste, ele será executado na mesma conta de armazenamento em que a replicação está progredindo. Nessa instância, a mesma quantidade de espaço de armazenamento adicional é necessária para a replicação. Ele garante que a replicação para o andamento e o failover de teste sejam bem-sucedidos em paralelo. Quando um disco gerenciado é configurado para failover de teste, nenhum espaço adicional precisa ser contabilizado com a VM de failover de teste.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 7.500 por disco.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 80.000 por VM.

* A variação média de dados da VM de origem excede o limite de variação de dados Site Recovery com suporte de 20 MB/s para o tamanho médio de e/s.

* A média de IOPS de gravação efetiva da VM de origem excede o limite de IOPS de Site Recovery com suporte de 840.

* O armazenamento de instantâneos calculado excede o limite de armazenamento de instantâneos com suporte de 10 TB.

**IOPS de l/g de pico (com fator de crescimento)** : o pico de IOPS de carga de trabalho no disco (o padrão é o 95 º percentil) junto com o fator de crescimento futuro (o padrão é 30%). O IOPS de leitura/gravação total da VM nem sempre é a soma do IOPS de leitura/gravação dos discos individuais da VM. O IOPS de leitura/gravação de pico da VM é o pico da soma dos IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação de dados de pico (MB/s) (com fator de crescimento)** : a taxa de variação de pico no disco (o padrão é o 95 º percentil) junto com o fator de crescimento futuro (o padrão é 30%). Observe que a rotatividade total de dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM. A rotatividade de dados de pico da VM é o pico da soma da rotatividade de seus discos individuais durante cada minuto do período de criação de perfil.

**Número de discos**: o número total de VHDS na VM.

**Tamanho do disco (GB)** : o tamanho total da configuração de todos os discos da VM. A ferramenta também mostra o tamanho do disco para os discos individuais na VM.

**Núcleos**: o número de núcleos de CPU na VM.

**Memória (MB)** : a quantidade de RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: o tipo de inicialização da VM. Pode ser BIOS ou EFI.

## <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites de Site Recovery. Esses limites se baseiam em testes, mas não podem abranger todas as combinações de e/s de aplicativo possíveis. Os resultados reais podem variar com base na combinação de e/s do aplicativo. Para obter melhores resultados, mesmo após o planejamento da implantação, execute testes de aplicativo extensivos emitindo um failover de teste para obter a imagem de desempenho real do aplicativo.
 
**Destino de armazenamento de replicação** | **Tamanho médio de e/s da VM de origem** |**Variação média de dados da VM de origem** | **Variação total de dados de VM de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s por VM | 168 GB por VM
Armazenamento Premium | 8 KB  | 5 MB/s por VM | 421 GB por VM
Armazenamento Premium | 16 KB ou mais| 20 MB/s por VM | 1684 GB por VM

Esses limites são números médios supondo uma sobreposição de e/s de 30%. Site Recovery é capaz de lidar com a taxa de transferência mais alta com base na taxa de sobreposição, tamanhos de gravação maiores e comportamento de e/s de carga de trabalho real. Os números anteriores pressupõem uma pendência típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados e um ponto de recuperação é criado dentro de cinco minutos.

## <a name="on-premises-storage-requirement"></a>Requisito de armazenamento local

A planilha fornece o requisito de espaço de armazenamento total livre para cada volume dos servidores Hyper-V (em que os VHDs residem) para replicação inicial e Delta com êxito. Antes de habilitar a replicação, adicione o espaço de armazenamento necessário nos volumes para garantir que a replicação não cause nenhum tempo de inatividade indesejável de seus aplicativos de produção. 

  Site Recovery Planejador de Implantações identifica o requisito de espaço de armazenamento ideal com base no tamanho do VHD e na largura de banda da rede usada para replicação.

![Requisito de armazenamento local](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Por que preciso de espaço livre no servidor Hyper-V para a replicação?
* Quando você habilita a replicação de uma VM, Site Recovery Obtém um instantâneo de cada VHD da VM para a replicação inicial. Enquanto a replicação inicial está em andamento, novas alterações são gravadas nos discos pelo aplicativo. Site Recovery controla essas alterações delta nos arquivos de log, o que exige espaço de armazenamento adicional. Até que a replicação inicial seja concluída, os arquivos de log são armazenados localmente. 

    Se não houver espaço suficiente disponível para os arquivos de log e o instantâneo (AVHDX), a replicação entrará no modo de ressincronização e a replicação nunca será concluída. Na pior das hipóteses, você precisa de 100 por cento de espaço livre adicional do tamanho do VHD para a replicação inicial.
* Após a conclusão da replicação inicial, a replicação delta é iniciada. Site Recovery rastreia essas alterações delta nos arquivos de log, que são armazenados no volume onde residem os VHDs da VM. Esses arquivos de log são replicados para o Azure em uma frequência de cópia configurada. Com base na largura de banda de rede disponível, os arquivos de log levam algum tempo para serem replicados no Azure. 

    Se não houver espaço livre suficiente para armazenar os arquivos de log, a replicação será pausada. Em seguida, o status de replicação da VM entra em "ressincronização necessária".
* Se a largura de banda da rede não for suficiente para enviar os arquivos de log para o Azure, os arquivos de log serão empilhadosdos no volume. Em um cenário de pior caso, quando o tamanho dos arquivos de log for aumentado para 50% do tamanho do VHD, a replicação da VM entrará em "ressincronização necessária". Na pior das hipóteses, você precisa de 50 por cento de espaço livre adicional do tamanho do VHD para a replicação delta.

**Host Hyper-v**: a lista de servidores Hyper-v com perfil. Se um servidor fizer parte de um cluster Hyper-V, todos os nós de cluster serão agrupados.

**Volume (caminho do VHD)** : cada volume de um host Hyper-V em que os VHDs/VHDXs estão presentes. 

**Espaço livre disponível (GB)** : o espaço livre disponível no volume.

**Espaço de armazenamento total necessário no volume (GB)** : o espaço livre de armazenamento total necessário no volume para replicação inicial e Delta com êxito. 

**Total de armazenamento adicional a ser provisionado no volume para replicação bem-sucedida (GB)** : recomenda o espaço adicional total que deve ser provisionado no volume para replicação inicial e Delta com êxito.

## <a name="initial-replication-batching"></a>Envio em lote de replicação inicial 

### <a name="why-do-i-need-initial-replication-batching"></a>Por que preciso de envio em lote de replicação inicial?
Se todas as VMs estiverem protegidas ao mesmo tempo, o requisito de armazenamento gratuito será muito maior. Se o armazenamento suficiente não estiver disponível, a replicação das VMs entrará no modo de ressincronização. Além disso, o requisito de largura de banda de rede é muito maior para concluir a replicação inicial de todas as VMs juntas com êxito. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Envio em lote de replicação inicial para um RPO selecionado
Esta planilha fornece a exibição de detalhes de cada lote para IR. Para cada RPO, uma folha de envio em lote de IR separada é criada. 

Depois de seguir a recomendação de requisito de armazenamento local para cada volume, as informações principais que você precisa replicar são a lista de VMs que podem ser protegidas em paralelo. Essas VMs são agrupadas em um lote e pode haver vários lotes. Proteger as VMs na ordem de lote determinada. Primeiro, proteja as VMs do lote 1. Após a conclusão da replicação inicial, proteja as VMs do lote 2 e assim por diante. Você pode obter a lista de lotes e VMs correspondentes desta planilha. 

![Detalhes do envio em lote IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Detalhes adicionais de envio em lote de IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Cada lote fornece as seguintes informações 
**Host Hyper-v**: o host Hyper-v da VM a ser protegida.

**Máquina virtual**: a VM a ser protegida. 

**Comentários**: se qualquer ação for necessária para um volume específico de uma VM, o comentário será fornecido aqui. Por exemplo, se não houver espaço livre suficiente disponível em um volume, o comentário diz "Adicionar armazenamento adicional para proteger essa VM".

**Volume (caminho do VHD)** : o nome do volume em que os VHDS da VM residem. 

**Espaço livre disponível no volume (GB)** : o espaço livre em disco disponível no volume para a VM. Ao calcular o espaço livre disponível nos volumes, ele considera o espaço em disco usado para a replicação delta pelas VMs dos lotes anteriores cujos VHDs estão no mesmo volume. 

Por exemplo, VM1, VM2 e VM3 residem em um volume, digamos, E:\VHDpath. Antes da replicação, o espaço livre no volume é de 500 GB. VM1 faz parte do lote 1, VM2 faz parte do lote 2 e VM3 é parte de lote 3. Para VM1, o espaço livre disponível é de 500 GB. Para VM2, o espaço livre disponível é 500 – espaço em disco necessário para a replicação delta para VM1. Se VM1 exigir 300 GB de espaço para a replicação delta, o espaço livre disponível para VM2 é 500 GB – 300 GB = 200 GB. Da mesma forma, o VM2 requer 300 GB para a replicação delta. O espaço livre disponível para VM3 é 200 GB-300 GB =-100 GB.

**Armazenamento necessário no volume para replicação inicial (GB)** : o espaço livre de armazenamento necessário no volume para a VM para a replicação inicial.

**Armazenamento necessário no volume para replicação delta (GB)** : o espaço livre de armazenamento necessário no volume para a VM para replicação delta.

**Armazenamento adicional necessário com base no déficit para evitar falha de replicação (GB)** : espaço de armazenamento adicional necessário no volume para a VM. É o máximo de requisitos de espaço de armazenamento da replicação inicial e de replicação delta menos o espaço livre disponível no volume.

**Largura de banda mínima necessária para a replicação inicial (Mbps)** : a largura de banda mínima necessária para a replicação inicial da VM.

**Largura de banda mínima necessária para a replicação delta (Mbps)** : a largura de banda mínima necessária para a replicação delta para a VM.

### <a name="network-utilization-details-for-each-batch"></a>Detalhes de utilização de rede para cada lote 
Cada tabela de lote fornece um resumo da utilização de rede do lote.

**Largura de banda disponível para o lote**: a largura de banda disponível para o lote depois de considerar a largura de banda de replicação delta do lote anterior.

**Largura de banda aproximada disponível para a replicação inicial do lote**: a largura de banda disponível para a replicação inicial das VMs do lote. 

**Largura de banda aproximada consumida para replicação delta de lote**: a largura de banda necessária para a replicação delta das VMs do lote. 

**Tempo estimado de replicação inicial para o lote (hh: mm)** : o tempo estimado de replicação inicial em horas: minutos.



## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a [estimativa de custo](hyper-v-deployment-planner-cost-estimation.md).
