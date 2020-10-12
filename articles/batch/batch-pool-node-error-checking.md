---
title: Verificar erros no pool e nos nós
description: Este artigo aborda as operações em segundo plano que podem ocorrer, além de erros a verificar e como evitá-los ao criar pools e nós.
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: how-to
ms.openlocfilehash: 519b357e4e5fde30221f7dc804bb848ecec9704c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85979910"
---
# <a name="check-for-pool-and-node-errors"></a>Verificar erros no pool e nos nós

Ao criar e gerenciar os pools do Lote do Microsoft Azure, algumas operações acontecem imediatamente. No entanto, algumas operações são assíncronas e executadas em segundo plano e levam vários minutos para serem concluídas.

A detecção de falhas para operações que ocorrem imediatamente é simples, uma vez que quaisquer falhas serão retornadas imediatamente pela API, CLI ou interface do usuário.

Este artigo aborda as operações em segundo plano que podem ocorrer para pools e nós de pool. Ele especifica como se pode detectar e evitar falhas.

## <a name="pool-errors"></a>Erros do pool

### <a name="resize-timeout-or-failure"></a>Redimensionar tempo limite ou falha

Ao criar um novo pool ou redimensionar um existente, o número de destino de nós é especificado.  A operação de criação ou redimensionamento é concluída imediatamente, mas a alocação real de novos nós ou a remoção de nós existentes pode levar vários minutos.  Especifique o tempo limite de redimensionamento em [crie](/rest/api/batchservice/pool/add) ou [redimensionar](/rest/api/batchservice/pool/resize) a API. Se o Lote não puder obter o número de nós de destino durante o período de tempo limite do redimensionamento, o pool entrará em um estado estável e reportará erros de redimensionamento.

A propriedade [ResizeError](/rest/api/batchservice/pool/get#resizeerror) da avaliação mais recente lista os erros ocorridos.

As causas comuns dos erros de redimensionamento incluem:

- O tempo limite de redimensionamento é muito curto
  - Na maioria das circunstâncias, o tempo limite padrão de 15 minutos é longo o suficiente para que os nós de pool sejam alocados ou removidos.
  - Se estiver alocando um grande número de nós, recomendamos definir o tempo limite de redimensionamento para 30 minutos. Por exemplo, quando estiver redimensionando a mais de 1.000 nós de uma imagem do Microsoft Azure Marketplace ou a mais de 300 nós de uma imagem VM personalizada.
- Cota de núcleo insuficiente
  - Uma conta de lote é limitadas em um número de núcleos alocados em todos os pools. O lote não alocará nós após essa cota ter sido atingida. Você [pode aumentar](./batch-quota-limit.md) a cota de núcleos para esse lote possa alocar mais nós.
- IPs de sub-rede insuficientes quando um [pool está em uma rede virtual](./batch-virtual-network.md)
  - Uma sub-rede de rede virtual deve ter suficiente endereços IP não atribuídos para alocar cada nó de pool solicitado. Caso contrário, os nós não podem ser criados.
- Recursos insuficientes quando um [pool está em uma rede virtual](./batch-virtual-network.md)
  - É possível criar recursos como grupos de segurança de rede, IPs públicos e grupo de segurança de rede na mesma assinatura que a conta do lote. Verifique se as cotas de assinatura para esses recursos são suficientes.
- Grandes pools com imagens de VM personalizadas
  - Grandes pools que usam imagens personalizadas de VM podem demorar mais para alocar, e podem ocorrer tempos limite de redimensionamento.  Confira [Criar um pool com a Galeria de Imagens Compartilhadas](batch-sig-images.md) para obter recomendações sobre limites e configurações.

### <a name="automatic-scaling-failures"></a>Falhas de dimensionamento automático

Também se pode definir o Lote do Microsoft Azure para dimensionar automaticamente o número de nós em um pool. Defina os parâmetros da [fórmula de dimensionamento automático para um pool](./batch-automatic-scaling.md). O serviço de lote usa a fórmula para avaliar o número de nós no pool periodicamente e definir um novo número de destino. Os tipos de problemas a seguir podem ocorrer:

- A fórmula de dimensionamento automático falhar.
- A operação de redimensionamento resultante pode falhar e atingir o tempo limite.
- Um problema com a fórmula de dimensionamento automático pode resultar em valores de destino de nó incorretos. O redimensionamento funcionar ou atingir o tempo limite.

Obtenha informações sobre a última avaliação do dimensionamento automático usando a propriedade [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun). Essa propriedade informa o tempo de avaliação, os valores, o resultado e quaisquer erros de desempenho.

Informações sobre todas as avaliações são capturadas automaticamente por um [evento completo de redimensionamento de pool](./batch-pool-resize-complete-event.md).

### <a name="delete"></a>Excluir

Ao excluir um pool que contém nós, o primeiro lote exclui os nós. Em seguida, ele exclui o próprio objeto do pool. A exclusão dos nós do pool pode levar alguns minutos para ser concluída.

O lote define o [estado do pool](/rest/api/batchservice/pool/get#poolstate) será definido como **excluindo** durante o processo de exclusão. O aplicativo de chamada pode detectar se a exclusão do pool está demorando demais usando as propriedades **state** e **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Erros do nó de computação do pool

Mesmo quando o Lote aloca com êxito os nós em um pool, diversos problemas podem fazer com que alguns dos nós sejam não íntegros e não consigam executar tarefas. Esses nós ainda incorrem em encargos, portanto, é importante detectar problemas para evitar o pagamento de nós que não podem ser usados. Além dos erros de nós comuns, é útil saber qual o [estado atual](/rest/api/batchservice/job/get#jobstate) do trabalho ao solucionar problemas.

### <a name="start-task-failures"></a>Falhas ao iniciar tarefa

Você talvez queira especificar um opcional [Iniciar tarefa](/rest/api/batchservice/pool/add#starttask) para um pool. Assim como acontece com qualquer tarefa, uma linha de comando e os arquivos de recurso para fazer o download do armazenamento podem ser especificados. A tarefa inicial é executada para cada nó depois que ele é iniciado. A propriedade **waitForSuccess** especifica se o lote aguarda até que a tarefa inicial seja concluída com êxito antes de agendar as tarefas para um nó.

E se o nó tiver sido configurado para aguardar a conclusão do iniciar tarefa com êxito, mas o iniciar tarefa falhar? Nesse caso, o nó não será utilizável, mas ainda incorrerá em encargos.

Falhas na tarefa inicial podem ser detectadas usando as propriedades [result](/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade de nó [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) de nível superior.

Uma falha ao iniciar tarefa também faz com que o lote defina o nó [state](/rest/api/batchservice/computenode/get#computenodestate) em **starttaskfailed** se **waitForSuccess** estiver definido como **true**.

Assim como acontece com qualquer tarefa, pode haver muitas causas para a falhar na tarefa inicial.  Para solucionar problemas, stdout, stderr e quaisquer arquivos de log de tarefas específicas adicionais devem ser verificados.

As tarefas iniciais devem ser de reinserção, pois é possível que a tarefa inicial seja executada várias vezes no mesmo nó; a tarefa inicial é executada quando um nó é reinicializado ou tem a imagem refeita. Em casos raros, uma tarefa inicial será executada depois que um evento tiver causado uma reinicialização de nó, na qual foi refeita a imagem de um dos discos efêmeros ou de sistema operacional enquanto o outro não passou por isso. Como as tarefas de início do Lote (como todas as tarefas do Lote) são executadas a partir do disco efêmero, isso normalmente não é um problema, mas, em alguns casos em que a tarefa inicial está instalando um aplicativo no disco do sistema operacional e mantendo outros dados no disco efêmero, isso pode causar problemas porque as coisas estarão fora de sincronia. Proteja seu aplicativo adequadamente se você estiver usando ambos os discos.

### <a name="application-package-download-failure"></a>Falha no download do pacote de aplicativos

Você pode especificar um ou mais pacotes de aplicativos de um pool. O Lote baixa os arquivos de pacote especificados para cada nó e descompacta os arquivos depois que o nó for iniciado, mas antes que as tarefas forem agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicativos. Por exemplo, para copiar arquivos para um local diferente ou para executar a instalação.

Uma falha ao baixar e descompactar um pacote de aplicativos será relatada pela propriedade [erros](/rest/api/batchservice/computenode/get#computenodeerror) do nó; o estado do nó é definido como **inutilizável**.

### <a name="container-download-failure"></a>Falha ao baixar contêiner

Você pode especificar uma ou mais referências de contêiner em um pool. O Lote baixa os contêineres especificados para cada nó. A propriedade [erros](/rest/api/batchservice/computenode/get#computenodeerror) do nó relata uma falha ao baixar um contêiner e define o estado do nó como **inutilizável**.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O Lote do Microsoft Azure pode definir o [estado do nó](/rest/api/batchservice/computenode/get#computenodestate) pode ser definido como **inutilizável** por muitos motivos. Com o estado do nó definido como **inutilizável**, as tarefas não podem ser agendadas para o nó, mas ainda incorrerá em encargos.

Nós em um estado **inutilizável**, mas sem [erros](/rest/api/batchservice/computenode/get#computenodeerror), indicam que o Lote não consegue se comunicar com a VM. Nesse caso, o Lote sempre tenta recuperar a VM. O Lote não tentará recuperar automaticamente as VMs que falharem ao instalar contêineres ou pacotes de aplicativos mesmo se o estado for **inutilizável**.

Se o lote poder determinar a causa, a propriedade do nó [erros](/rest/api/batchservice/computenode/get#computenodeerror) irá reportá-lo.

Alguns outros exemplos de causas para nós **inutilizáveis** incluem:

- A imagem de VM personalizada é inválida. Por exemplo, uma imagem que não é preparada corretamente.

- Uma VM é movida devido a uma falha de infraestrutura ou de uma atualização de nível baixo. O lote recupera o nó.

- Uma imagem de VM foi implantada em hardware que não dá suporte a ela. Por exemplo, tentar executar uma imagem do CentOS HPC em uma VM [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md).

- As VMs estão em uma [rede virtual do Azure](batch-virtual-network.md) e o tráfego para as principais portas foi bloqueado.

- As VMs estão em uma rede virtual, mas o tráfego de saída para o armazenamento do Azure está bloqueado.

- As VMs estão em uma rede virtual com uma configuração de DNS do cliente e o servidor DNS não consegue resolver o armazenamento do Azure.

### <a name="node-agent-log-files"></a>Arquivos de log do agente de nó

O processo do agente do Lote que é executado em cada nó do pool pode fornecer arquivos de log que podem ser úteis se você precisar contatar o suporte sobre um problema de nó do pool. Os arquivos de log para um nó podem ser carregados por meio do portal do Microsoft Azure, do Batch Explorer ou de uma [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e salvar os arquivos de log. Depois disso, pode-se excluir o nó ou o pool para economizar o custo de nós em execução.

### <a name="node-disk-full"></a>Disco do nó cheio

A unidade temporária de uma VM de nó de pool é usada pelo Lote para arquivos compartilhados, de trabalhos e de tarefas.

- Arquivos de pacotes de aplicativos
- Arquivos de recursos de tarefas
- Arquivos específicos do aplicativo baixados em uma das pastas do Lote
- Arquivos stdout e stderr para cada execução de aplicativo de tarefas
- Arquivos de saída específicos do aplicativo

Alguns desses arquivos são gravados apenas uma vez quando nós de pool são criados, como pacotes de aplicativos de pool ou arquivos de recursos de tarefas de início de pool. Mesmo que sejam gravados apenas uma vez quando o nó for criado, se esses arquivos forem muito grandes, poderão preencher a unidade temporária.

Outros arquivos são gravados para cada tarefa executada em um nó, como stdout e stderr. Se um grande número de tarefas for executado no mesmo nó e/ou os arquivos de tarefas forem muito grandes, eles poderão preencher a unidade temporária.

O tamanho da unidade temporária depende do tamanho da VM. Ao escolher um tamanho de VM, considere garantir que a unidade temporária tenha espaço suficiente.

- No portal do Azure, ao adicionar um pool, é possível exibir a lista completa de tamanhos de VM e há a coluna "Tamanho do Disco do Recurso".
- Os artigos que descrevem todos os tamanhos de VM têm tabelas com a coluna "Armazenamento Temporário"; por exemplo, [Tamanhos de VM de Computação Otimizada](../virtual-machines/sizes-compute.md)

Para arquivos gravados por cada tarefa, é possível especificar um tempo de retenção para cada tarefa que determine por quanto tempo os arquivos de tarefa serão mantidos antes de serem limpos automaticamente. O tempo de retenção pode ser reduzido para diminuir os requisitos de armazenamento.


Se o disco temporário ficar sem espaço (ou estiver muito perto de ficar sem espaço), o nó será movido para o estado [inutilizável](/rest/api/batchservice/computenode/get#computenodestate) e um erro de nó será relatado, informando que o disco está cheio.

### <a name="what-to-do-when-a-disk-is-full"></a>O que fazer quando um disco está cheio

Determine por que o disco está cheio: se você não tiver certeza do que está ocupando espaço no nó, é recomendável deixar o nó como remoto e investigar manualmente como o espaço acabou. Você também pode usar a [API de Arquivos de Lista de Lote](/rest/api/batchservice/file/listfromcomputenode) para examinar arquivos em pastas gerenciadas do Lote (por exemplo, saídas de tarefas). Observe que essa API só lista os arquivos nos diretórios gerenciados pelo Lote e, se suas tarefas criaram arquivos em outro lugar, você não os verá.

Verifique se todos os dados necessários foram recuperados do nó ou carregados em um repositório durável. Resolver problemas de disco cheio sempre envolve a exclusão de dados para liberar espaço.

### <a name="recovering-the-node"></a>Recuperação do nó

1. Se o pool for um pool for de [C.loudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration), você poderá refazer a imagem do nó por meio da [API para refazer imagem do Lote](/rest/api/batchservice/computenode/reimage), o que limpará o disco inteiro. A ação de refazer imagem não tem suporte no momento para pools de [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).

2. Se o pool for de [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration), você poderá remover o nó do pool usando a [API para remover nós](/rest/api/batchservice/pool/removenodes). Em seguida, você pode aumentar novamente o pool para substituir o nó inadequado por um novo.

3.  Exclua tarefas ou trabalhos antigos concluídos cujos dados de tarefa ainda estejam nos nós. Para obter uma dica sobre quais dados de trabalhos ou tarefas estão nos nós, você pode procurar na [coleção RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) do nó ou nos [arquivos no nó](/rest/api/batchservice/file/listfromcomputenode). A exclusão do trabalho excluirá todas as tarefas no trabalho e a exclusão das tarefas no trabalho disparará os dados nos diretórios de tarefas no nó a ser excluído, dessa forma liberando o espaço. Depois de liberar espaço suficiente, reinicialize o nó e ele deverá sair do estado "Inutilizável", retornando para "ocioso".

## <a name="next-steps"></a>Próximas etapas

Verifique se definiu seu aplicativo para implementar a verificação de erro abrangente, especialmente para operações assíncronas. Isso pode ser fundamental para detectar e diagnosticar problemas imediatamente.
