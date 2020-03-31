---
title: Verifique se há erros de pool e dos nós - Lote do Microsoft Azure
description: Este artigo abrange as operações de fundo que podem ocorrer, juntamente com erros a serem verificados e como evitá-los ao criar pools e nós.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472938"
---
# <a name="check-for-pool-and-node-errors"></a>Verificar erros no pool e nos nós

Ao criar e gerenciar os pools do Lote do Microsoft Azure, algumas operações acontecem imediatamente. No entanto, algumas operações são assíncronas e são executadas em segundo plano, levando vários minutos para serem concluídas.

A detecção de falhas para operações que ocorrem imediatamente é simples, uma vez que quaisquer falhas serão retornadas imediatamente pela API, CLI ou interface do usuário.

Este artigo aborda as operações em segundo plano que podem ocorrer para pools e nós de pool. Ele especifica como se pode detectar e evitar falhas.

## <a name="pool-errors"></a>Erros do pool

### <a name="resize-timeout-or-failure"></a>Redimensionar tempo limite ou falha

Ao criar um novo pool ou redimensionar um existente, o número de destino de nós é especificado.  A operação criar ou redimensionar é concluída imediatamente, mas a alocação real de novos nódulos ou a remoção de nódulos existentes pode levar vários minutos.  Especifique o tempo limite de redimensionamento em [crie](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) a API. Se o Batch não conseguir obter o número de números-alvo durante o período de tempo limite de redimensionamento, o pool entrará em um estado estável e relatará erros de redimensionamento.

A propriedade [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) para a avaliação mais recente lista os erros ocorridos.

As causas comuns para redimensionar erros incluem:

- O tempo limite de redimensionamento é muito curto
  - Na maioria das circunstâncias, o tempo limite padrão de 15 minutos é longo o suficiente para que os nós de pool sejam alocados ou removidos.
  - Se estiver alocando um grande número de nós, recomendamos definir o tempo limite de redimensionamento para 30 minutos. Por exemplo, quando estiver redimensionando a mais de 1.000 nós de uma imagem do Microsoft Azure Marketplace ou a mais de 300 nós de uma imagem VM personalizada.
- Cota de núcleo insuficiente
  - Uma conta de lote é limitadas em um número de núcleos alocados em todos os pools. O lote não alocará nós após essa cota ter sido atingida. Você [pode aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) a cota de núcleos para esse lote possa alocar mais nós.
- IPs de sub-rede insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma sub-rede de rede virtual deve ter suficiente endereços IP não atribuídos para alocar cada nó de pool solicitado. Caso contrário, os nós não podem ser criados.
- Recursos insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - É possível criar recursos como grupos de segurança de rede, IPs públicos e grupo de segurança de rede na mesma assinatura que a conta do lote. Verifique se as cotas de assinatura para esses recursos são suficientes.
- Grandes pools com imagens de VM personalizadas
  - Grandes pools que usam imagens personalizadas de VM podem demorar mais para alocar, e podem ocorrer tempos limite de redimensionamento.  Consulte [Criar um pool com a Galeria de Imagens Compartilhadas](batch-sig-images.md) para obter recomendações sobre limites e configuração.

### <a name="automatic-scaling-failures"></a>Falhas de dimensionamento automático

Também se pode definir o Lote do Microsoft Azure para dimensionar automaticamente o número de nós em um pool. Defina os parâmetros da [fórmula de dimensionamento automático para um pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). O serviço de lote usa a fórmula para avaliar o número de nós no pool periodicamente e definir um novo número de destino. Os tipos de problemas a seguir podem ocorrer:

- A fórmula de dimensionamento automático falhar.
- A operação de redimensionamento resultante pode falhar e atingir o tempo limite.
- Um problema com a fórmula de dimensionamento automático pode resultar em valores de destino de nó incorretos. O redimensionamento funcionar ou atingir o tempo limite.

Obtenha informações sobre a última avaliação do dimensionamento automático usando a propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Essa propriedade informa o tempo de avaliação, os valores, o resultado e quaisquer erros de desempenho.

Informações sobre todas as avaliações são capturadas automaticamente por um [evento completo de redimensionamento de pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Excluir

Ao excluir um pool que contém nós, o primeiro lote exclui os nós. Em seguida, ele exclui o próprio objeto do pool. A exclusão dos nós do pool pode levar alguns minutos para ser concluída.

O lote define o [estado do pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) será definido como **excluindo** durante o processo de exclusão. O aplicativo de chamada pode detectar se a exclusão do pool está demorando demais usando as propriedades **state** e **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Erros do nó de computação do pool

Mesmo quando batch aloca nós com sucesso em um pool, vários problemas podem fazer com que alguns dos nós sejam insalubres e incapazes de executar tarefas. Esses nódulos ainda incorrem em encargos, por isso é importante detectar problemas para evitar pagar por nódulos que não podem ser usados. Além de erros comuns de nó, conhecer o estado atual do [trabalho](/rest/api/batchservice/job/get#jobstate) é útil para a solução de problemas.

### <a name="start-task-failures"></a>Iniciar falhas de tarefa

Você talvez queira especificar um opcional [Iniciar tarefa](/rest/api/batchservice/pool/add#starttask) para um pool. Assim como acontece com qualquer tarefa, uma linha de comando e os arquivos de recurso para fazer o download do armazenamento podem ser especificados. A tarefa inicial é executada para cada nó depois que ele é iniciado. A propriedade **waitForSuccess** especifica se o lote aguarda até que a tarefa inicial seja concluída com êxito antes de agendar as tarefas para um nó.

E se o nó tiver sido configurado para aguardar a conclusão do iniciar tarefa com êxito, mas o iniciar tarefa falhar? Nesse caso, o nó não será utilizável, mas ainda incorrerá em acusações.

Falhas na tarefa inicial podem ser detectadas usando as propriedades [result](/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade de nó [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) de nível superior.

Uma tarefa inicial com falha também faz com que batch defina o [estado](/rest/api/batchservice/computenode/get#computenodestate) de nó para **iniciar tarefas falhou** se **waitForSuccess** foi definido como **true**.

Assim como acontece com qualquer tarefa, pode haver muitas causas para a falhar na tarefa inicial.  Para solucionar problemas, stdout, stderr e quaisquer arquivos de log de tarefas específicas adicionais devem ser verificados.

As tarefas iniciais devem ser re-participantes, pois é possível que a tarefa inicial seja executada várias vezes no mesmo nó; a tarefa inicial é executada quando um nó é regravado ou reiniciado. Em casos raros, uma tarefa inicial será executada após um evento causar uma reinicialização do nó, onde um dos discos operacionais ou efêmeros foi recapturado enquanto o outro não estava. Uma vez que as tarefas de início de lote (como todas as tarefas em lote) são executadas a partir do disco efêmero, isso normalmente não é um problema, mas em alguns casos em que a tarefa inicial é instalar um aplicativo no disco do sistema operacional e manter outros dados no disco efêmero, isso pode causar problemas porque as coisas estão fora de sincronia. Proteja seu aplicativo de acordo se estiver usando ambos os discos.

### <a name="application-package-download-failure"></a>Falha no download do pacote de aplicativos

Você pode especificar um ou mais pacotes de aplicativos de um pool. O batch baixa os arquivos de pacote especificados para cada nó e descompacta os arquivos após o início do nó, mas antes que as tarefas sejam agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicativos. Por exemplo, para copiar arquivos para um local diferente ou para executar a instalação.

A propriedade de [erros](/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha no download e descompactação de um pacote de aplicativos; o estado do nó é definido **como inutilizável**.

### <a name="container-download-failure"></a>Falha no download do contêiner

Você pode especificar uma ou mais referências de contêiner em uma piscina. O lote baixa os recipientes especificados para cada nó. A propriedade de [erros](/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha no download de um contêiner e define o estado do nó como **inutilizável**.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O Lote do Microsoft Azure pode definir o [estado do nó](/rest/api/batchservice/computenode/get#computenodestate) pode ser definido como **inutilizável** por muitos motivos. Com o estado do nó definido como **inutilizável**, as tarefas não podem ser agendadas para o nó, mas ainda incorrerá em encargos.

Nós em um estado **inutilizável,** mas sem [erros](/rest/api/batchservice/computenode/get#computenodeerror) significa que batch é incapaz de se comunicar com a VM. Neste caso, batch sempre tenta recuperar o VM. O batch não tentará recuperar automaticamente VMs que não conseguiram instalar pacotes de aplicativos ou contêineres, mesmo que seu estado esteja **inutilizável**.

Se o lote poder determinar a causa, a propriedade do nó [erros](/rest/api/batchservice/computenode/get#computenodeerror) irá reportá-lo.

Alguns outros exemplos de causas para nós **inutilizáveis** incluem:

- A imagem de VM personalizada é inválida. Por exemplo, uma imagem que não é preparada corretamente.

- Uma VM é movida devido a uma falha de infraestrutura ou de uma atualização de nível baixo. O lote recupera o nó.

- Uma imagem VM foi implantada em hardware que não a suporta. Por exemplo, tentar executar uma imagem Do CentOS HPC em um [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM.

- As VMs estão em uma [rede virtual Azure](batch-virtual-network.md), e o tráfego foi bloqueado em portas-chave.

- As VMs estão em uma rede virtual, mas o tráfego de saída para o armazenamento do Azure está bloqueado.

- As VMs estão em uma rede virtual com uma configuração de DNS do cliente e o servidor DNS não pode resolver o armazenamento DoZure.

### <a name="node-agent-log-files"></a>Arquivos de log do agente de nó

O processo de agente batch que é executado em cada nó de pool pode fornecer arquivos de log que podem ser úteis se você precisar entrar em contato com o suporte sobre um problema de nó de pool. Os arquivos de log para um nó podem ser carregados por meio do portal do Microsoft Azure, do Batch Explorer ou de uma [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e salvar os arquivos de log. Depois disso, pode-se excluir o nó ou o pool para economizar o custo de nós em execução.

### <a name="node-disk-full"></a>Disco de nó completo

A unidade temporária para um vm de nó de pool é usada pela Batch para arquivos de trabalho, arquivos de tarefas e arquivos compartilhados.

- Arquivos de pacotes de aplicativos
- Arquivos de recursos de tarefa
- Arquivos específicos de aplicativos baixados para uma das pastas batch
- Arquivos Stdout e stderr para cada execução de aplicativo de tarefa
- Arquivos de saída específicos do aplicativo

Alguns desses arquivos são gravados apenas uma vez quando os nós de pool são criados, como pacotes de aplicativos de pool ou arquivos de recursos de início de pool. Mesmo que apenas escrito uma vez quando o nó é criado, se esses arquivos são muito grandes, eles poderiam preencher a unidade temporária.

Outros arquivos são escritos para cada tarefa que é executada em um nó, como stdout e stderr. Se um grande número de tarefas for executada no mesmo nó e/ou os arquivos de tarefas forem muito grandes, eles podem preencher a unidade temporária.

O tamanho da unidade temporária depende do tamanho da VM. Uma consideração ao escolher um tamanho de VM é garantir que a unidade temporária tenha espaço suficiente.

- No portal Azure ao adicionar um pool, a lista completa de tamanhos de VM pode ser exibida e há uma coluna 'Tamanho do disco de recursos'.
- Os artigos que descrevem todos os tamanhos de VM têm tabelas com uma coluna 'Armazenamento temporário'; por [exemplo, tamanhos de VM otimizados para computação](/azure/virtual-machines/windows/sizes-compute)

Para arquivos gravados por cada tarefa, um tempo de retenção pode ser especificado para cada tarefa que determina quanto tempo os arquivos de tarefa são mantidos antes de serem automaticamente limpos. O tempo de retenção pode ser reduzido para reduzir os requisitos de armazenamento.


Se o disco temporário ficar sem espaço (ou estiver muito perto de ficar sem espaço), o nó se moverá para o estado [inutilizável](/rest/api/batchservice/computenode/get#computenodestate) e um erro de nó será relatado dizendo que o disco está cheio.

### <a name="what-to-do-when-a-disk-is-full"></a>O que fazer quando um disco está cheio

Determine por que o disco está cheio: Se você não tem certeza do que está ocupando espaço no nó, é recomendável controlar o nó e investigar manualmente para onde o espaço foi. Você também pode fazer uso da [API Arquivos de lista](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) de lotes para examinar arquivos em pastas gerenciadas em lote (por exemplo, saídas de tarefas). Observe que esta API só lista arquivos nos diretórios gerenciados do Batch e se suas tarefas criaram arquivos em outro lugar, você não os verá.

Certifique-se de que todos os dados necessários foram recuperados do nó ou carregados em uma loja durável. Toda a mitigação do problema do disco completo envolve a exclusão de dados para liberar espaço.

### <a name="recovering-the-node"></a>Recuperando o nó

1. Se o pool for um pool [C.loudServiceConfiguration,](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) você poderá revisualizar o nó através da [API de reimagem batch](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage). Isso vai limpar todo o disco. A reimagem não é suportada no momento para pools [VirtualMachineConfiguration.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)

2. Se o seu pool for uma [Configuração VirtualMachine,](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)você pode remover o nó da piscina usando a [API removenós](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes). Então, você pode crescer a piscina novamente para substituir o nó ruim por um novo.

3.  Exclua trabalhos antigos concluídos ou tarefas antigas concluídas cujos dados de tarefa ainda estão nos nós. Para obter uma dica sobre quais dados de trabalhos/tarefas estão nos nós, você pode olhar na [coleção RecentTasks](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) no nó ou nos [arquivos no nó](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode). A exclusão do trabalho excluirá todas as tarefas do trabalho e a exclusão das tarefas no trabalho acionará dados nos diretórios de tarefas no nó a ser excluído, liberando assim espaço. Depois de liberar espaço suficiente, reinicie o nó e ele deve sair do estado "Inutilizável" para "Ocioso" novamente.

## <a name="next-steps"></a>Próximas etapas

Verifique se definiu seu aplicativo para implementar a verificação de erro abrangente, especialmente para operações assíncronas. Isso pode ser fundamental para detectar e diagnosticar problemas imediatamente.
