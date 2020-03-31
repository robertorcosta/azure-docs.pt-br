---
title: Melhores práticas - Lote Azure
description: Aprenda as melhores práticas e dicas úteis para desenvolver sua solução Azure Batch.
author: LauraBrenner
ms.author: labrenne
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: 16fb2786f180b1e28b76d9246d599a871278d00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022725"
---
# <a name="azure-batch-best-practices"></a>Práticas recomendadas do Azure Batch

Este artigo discute uma coleção de práticas recomendadas para o uso do serviço Azure Batch de forma eficaz e eficiente. Essas melhores práticas são derivadas de nossa experiência com a Batch e das experiências dos clientes batch. É importante entender este artigo para evitar armadilhas de design, problemas potenciais de desempenho e anti-padrões ao desenvolver e usar o Batch.

Neste artigo, você aprenderá o seguinte:

> [!div class="checklist"]
> - Quais são as melhores práticas
> - Por que você deve usar as melhores práticas
> - O que pode acontecer se você não seguir as melhores práticas
> - Como seguir as melhores práticas

## <a name="pools"></a>Pools

Os pools de lotes são os recursos computacionais para execução de trabalhos no serviço Batch. As seções a seguir fornecem orientações sobre as melhores práticas a seguir ao trabalhar com pools batch.

### <a name="pool-configuration-and-naming"></a>Configuração e nomeação do pool

- **Modo de alocação de pool**  
    Ao criar uma conta em lote, você pode escolher entre dois modos de alocação de pool: **Serviço em lote** ou assinatura do **usuário**. Para a maioria dos casos, você deve usar o modo de serviço batch padrão, no qual os pools são alocados nos bastidores em assinaturas gerenciadas em lote. No modo de assinatura alternativo do usuário, as VMs do Lote e outros recursos são criados diretamente em sua assinatura, quando um pool é criado. As contas de assinatura de usuário são usadas principalmente para permitir um subconjunto importante, mas pequeno de cenários. Você pode ler mais sobre o modo de assinatura do usuário [em configuração adicional para o modo de assinatura do usuário](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Considere o tempo de execução do trabalho e da tarefa ao determinar o trabalho para pool mapeamento.**  
    Se você tem trabalhos compostos principalmente por tarefas de curto prazo, e a contagem total de tarefas esperada é pequena, de modo que o tempo de execução global esperado do trabalho não seja longo, não aloque um novo pool para cada trabalho. O tempo de alocação dos nódulos diminuirá o tempo de execução do trabalho.

- **Os pools devem ter mais de um nó computacional.**  
    Os nós individuais não são garantidos para estarem sempre disponíveis. Embora incomum, falhas de hardware, atualizações do sistema operacional e uma série de outros problemas podem fazer com que nós individuais estejam offline. Se a carga de trabalho do lote exigir progresso determinístico e garantido, você deve alocar pools com vários nós.

- **Não reutilize nomes de recursos.**  
    Recursos em lote (empregos, pools, etc.) muitas vezes vêm e vão com o tempo. Por exemplo, você pode criar um pool na segunda-feira, excluí-lo na terça-feira e, em seguida, criar outro pool na quinta-feira. Cada novo recurso criado deve receber um nome único que você não tenha usado antes. Isso pode ser feito usando um GUID (seja como nome de recurso inteiro, ou como parte dele) ou incorporando o tempo em que o recurso foi criado no nome do recurso. O batch suporta [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), que pode ser usado para dar a um recurso um nome legível humano, mesmo que o ID de recurso real seja algo que não seja tão amigável. O uso de nomes exclusivos torna mais fácil para você diferenciar qual recurso específico fez algo em logs e métricas. Ele também remove a ambiguidade se você tiver que arquivar um caso de suporte para um recurso.

- **Continuidade durante a manutenção e falha da piscina.**  
    É melhor que seus trabalhos usem piscinas dinamicamente. Se seus trabalhos usam a mesma piscina para tudo, há uma chance de que seus trabalhos não funcionarão se algo der errado com a piscina. Isso é especialmente importante para cargas de trabalho sensíveis ao tempo. Para corrigir isso, selecione ou crie um pool dinamicamente quando você agenda cada trabalho ou tenha uma maneira de substituir o nome do pool para que você possa contornar um pool insalubre.

- **Continuidade de negócios durante a manutenção e falha do pool**  
    Existem muitas causas possíveis que podem impedir que um pool cresça para o tamanho necessário, como erros internos, restrições de capacidade, etc. Por essa razão, você deve estar pronto para redirecionar trabalhos em um pool diferente (possivelmente com um tamanho de VM diferente - O lote suporta isso via [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)) se necessário. Evite usar um ID de pool estático com a expectativa de que ele nunca será excluído e nunca mudará.

### <a name="pool-lifetime-and-billing"></a>Vida útil da piscina e faturamento

A vida útil do pool pode variar dependendo do método de alocação e das opções aplicadas à configuração do pool. Os pools podem ter uma vida arbitrária e um número variado de nódulos computacionais na piscina a qualquer momento. É sua responsabilidade gerenciar os nódulos de computação no pool explicitamente ou através de recursos fornecidos pelo serviço (autoscale ou autopool).

- **Mantenha as piscinas frescas.**  
    Você deve redimensionar suas piscinas para zero a cada poucos meses para garantir que você obtenha as atualizações mais recentes do agente de nó e correções de bugs. Seu pool não receberá atualizações do agente de nó a menos que seja recriado ou redimensionado para 0 nomes computacionais. Antes de recriar ou redimensionar seu pool, recomenda-se baixar quaisquer logs de agente de nó para fins de depuração, conforme discutido na seção [Dedos.](#nodes)

- **Recriação de piscinas**  
    Em uma nota semelhante, não é recomendável excluir e recriar seus pools diariamente. Em vez disso, crie um novo pool, atualize seus trabalhos existentes para apontar para o novo pool. Uma vez que todas as tarefas tenham sido movidas para o novo pool, exclua a piscina antiga.

- **Eficiência e faturamento do pool**  
    O lote em si não incorre em cobranças extras, mas você incorre em encargos pelos recursos de cálculo usados. Você é cobrado por cada nó de computação na piscina, independentemente do estado em que está. Isso inclui quaisquer encargos necessários para que o nó seja executado, como custos de armazenamento e rede. Para saber mais as melhores práticas, consulte [análise de custos e orçamentos para o Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Falhas de alocação de pool

Falhas de alocação de pool podem acontecer a qualquer momento durante a primeira alocação ou redimensionamentos subsequentes. Isso pode ser devido ao esgotamento temporário da capacidade em uma região ou falhas em outros serviços do Azure que batch depende. Sua cota principal não é uma garantia, mas sim um limite.

### <a name="unplanned-downtime"></a>Tempo de inatividade não planejado

É possível que as piscinas batch experimentem eventos de inatividade no Azure. Isso é importante ter em mente ao planejar e desenvolver seu cenário ou fluxo de trabalho para Batch.

No caso de um nó falhar, batch automaticamente tenta recuperar esses nós de computação em seu nome. Isso pode desencadear o reagendamento de qualquer tarefa em execução no nó recuperado. Consulte [Designing para repetições](#designing-for-retries-and-re-execution) para saber mais sobre tarefas interrompidas.

- **Dependência da região do Azure**  
    É aconselhável não depender de uma única região Azure se você tiver uma carga de trabalho de tempo sensível ou de produção. Embora raros, há problemas que podem afetar toda uma região. Por exemplo, se o processamento precisar começar em um momento específico, considere escalar a piscina na sua região primária *bem antes do horário de início*. Se essa escala de piscina falhar, você pode voltar a escalar uma piscina em uma região de backup (ou regiões). Os pools em várias contas em diferentes regiões fornecem um backup pronto e facilmente acessível se algo der errado com outra piscina. Para obter mais informações, consulte [Design seu aplicativo para alta disponibilidade](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Trabalhos

Um trabalho é um contêiner projetado para conter centenas, milhares ou até milhões de tarefas.

- **Coloque muitas tarefas em um trabalho**  
    Usar um trabalho para executar uma única tarefa é ineficiente. Por exemplo, é mais eficiente usar um único trabalho contendo 1000 tarefas em vez de criar 100 empregos que contenham 10 tarefas cada. Executar 1000 empregos, cada um com uma única tarefa, seria a abordagem menos eficiente, mais lenta e mais cara a tomar.  

    Não projete uma solução Batch que exija milhares de empregos ativos simultaneamente. Não há contingente para tarefas, portanto, executar o maior número de tarefas em menos de trabalho possível utiliza eficientemente suas [cotas de trabalho e horário de trabalho.](batch-quota-limit.md#resource-quotas)

- **Vida útil do trabalho**  
    Um trabalho de lote tem uma vida indefinida até ser excluído do sistema. O estado de um trabalho designa se ele pode aceitar mais tarefas para agendamento ou não. Um trabalho não se move automaticamente para estado concluído, a menos que seja explicitamente encerrado. Isso pode ser acionado automaticamente através da propriedade [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) ou [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Há uma [cota de emprego e agenda de empregos ativas inadimplentes.](batch-quota-limit.md#resource-quotas) Empregos e horários de trabalho em estado concluído não contam para esse contingente.

## <a name="tasks"></a>Tarefas

Tarefas são unidades individuais de trabalho que compõem um trabalho. As tarefas são enviadas pelo usuário e agendadas por Batch para calcular nós. Existem várias considerações de design a serem consideradas ao criar e executar tarefas. As seções a seguir explicam cenários comuns e como projetar suas tarefas para lidar com problemas e executar de forma eficiente.

- **Salve os dados da tarefa como parte da tarefa.**  
    Nós computados são por sua natureza efêmeros. Existem muitos recursos em Batch, como autopool e autoscale que facilitam o desaparecimento de nós. Quando os nós saem do pool (devido a um redimensionamento ou uma exclusão de pool) todos os arquivos nesses nós também são excluídos. Por causa disso, recomenda-se que antes de uma tarefa ser concluída, ele mova sua saída para fora do nó em que está sendo executado e para uma loja durável, da mesma forma, se uma tarefa falhar, ele deve mover logs necessários para diagnosticar a falha em uma loja durável. O Batch tem suporte integrado ao Azure Storage para carregar dados via [OutputFiles,](batch-task-output-files.md)bem como uma variedade de sistemas de arquivos compartilhados, ou você pode executar o upload você mesmo em suas tarefas.

### <a name="task-lifetime"></a>Tempo de vida da tarefa

- **Exclua tarefas quando estiverem concluídas.**  
    Exclua tarefas quando elas não forem mais necessárias ou defina uma restrição de tarefa [retentionTime.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) Se `retentionTime` a estiver definida, batch limpa automaticamente o espaço de `retentionTime` disco usado pela tarefa quando o expirar.  

    Excluir tarefas realiza duas coisas. Ele garante que você não tenha um acúmulo de tarefas no trabalho, tornando a consulta/localização da tarefa em que você está interessado mais difícil (porque você terá que filtrar através das tarefas concluídas). Também limpa os dados de tarefa correspondentes no `retentionTime` nó (desde que ainda não tenha sido atingido). Isso garante que seus nós não se preencham com dados de tarefas e ficam sem espaço em disco.

### <a name="task-submission"></a>Submissão de tarefas

- **Envie um grande número de tarefas em uma coleção.**  
    As tarefas podem ser submetidas individualmente ou em coleções. Envie [tarefas](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) em coleções de até 100 de cada vez ao fazer a submissão em massa de tarefas para reduzir o tempo de sobrecarga e submissão.

### <a name="task-execution"></a>Execução das tarefas

- **Escolhendo suas tarefas máximas por nó**  
    O batch suporta atribuir tarefas em cima de nós (executar mais tarefas do que um nó tem núcleos). Cabe a você garantir que suas tarefas "se encaixem" nos nódulos da sua piscina. Por exemplo, você pode ter uma experiência degradada se tentar agendar oito tarefas que consomem 25% de uso da CPU em um nó (em um pool com `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Projetando para repetições e reexecução

As tarefas podem ser repetidas automaticamente por Batch. Existem dois tipos de repetições: controlada pelo usuário e interna. As repetições controladas pelo usuário são especificadas pelo [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)da tarefa . Quando um programa especificado na tarefa sai com um código de saída não-zero, `maxTaskRetryCount`a tarefa é repetida novamente até o valor do .

Embora raro, uma tarefa pode ser repetida internamente devido a falhas no nó de computação, como não ser capaz de atualizar o estado interno ou uma falha no nó enquanto a tarefa estiver em execução. A tarefa será repetida novamente no mesmo nó de computação, se possível, até um limite interno antes de desistir da tarefa e adiar a tarefa a ser reagendada pela Batch, potencialmente em um nó de computação diferente.

- **Construa tarefas duráveis**  
    As tarefas devem ser projetadas para resistir à falha e acomodar a repetição. Isso é especialmente importante para tarefas de longa duração. Para isso, certifique-se de que as tarefas gerem o mesmo resultado único, mesmo que sejam executadas mais de uma vez. Uma maneira de conseguir isso é fazer com que suas tarefas "busquem objetivos". Outra maneira é garantir que suas tarefas sejam idempotentes (as tarefas terão o mesmo resultado, não importa quantas vezes sejam executadas).

    Um exemplo comum é uma tarefa para copiar arquivos para um nó de computação. Uma abordagem simples é uma tarefa que copia todos os arquivos especificados toda vez que é executado, o que é ineficiente e não é construído para resistir à falha. Em vez disso, crie uma tarefa para garantir que os arquivos estejam no nó de computação; uma tarefa que não recopia arquivos que já estão presentes. Desta forma, a tarefa retoma de onde parou se foi interrompida.

- **Nódulos de baixa prioridade**  
    Não há diferenças de design ao executar suas tarefas em nós dedicados ou de baixa prioridade. Se uma tarefa é antecipada durante a execução em um nó de baixa prioridade ou interrompida devido a uma falha em um nó dedicado, ambas as situações são atenuadas projetando a tarefa para resistir à falha.

- **Tempo de execução da tarefa**  
    Evite tarefas com pouco tempo de execução. Tarefas que só funcionam de um a dois segundos não são ideais. Você deve tentar fazer uma quantidade significativa de trabalho em uma tarefa individual (mínimo de 10 segundos, indo até horas ou dias). Se cada tarefa estiver sendo executada por um minuto (ou mais), então a sobrecarga de agendamento como uma fração do tempo total de computação é pequena.

## <a name="nodes"></a>Nós

- **Tarefas iniciais devem ser idempotentes**  
    Semelhante a outras tarefas, a tarefa de início do nó deve ser impotente, pois será reexecutada toda vez que o nó inicializar. Uma tarefa idempotente é simplesmente aquela que produz um resultado consistente quando executada várias vezes.

- **Gerencie serviços de longa duração através da interface de serviços do sistema operacional.**  
    Às vezes há a necessidade de executar outro agente ao lado do agente Batch no nó, por exemplo, para coletar dados do nó e denunciá-los. Recomendamos que esses agentes sejam implantados como serviços `systemd` de SO, como um serviço Windows ou um serviço Linux.  

    Ao executar esses serviços, eles não devem fazer bloqueios de arquivos em quaisquer arquivos em diretórios gerenciados em lote no nó, porque caso contrário, batch não será capaz de excluir esses diretórios devido aos bloqueios de arquivos. Por exemplo, se instalar um serviço do Windows em uma tarefa inicial, em vez de iniciar o serviço diretamente do diretório de trabalho da tarefa inicial, copie os arquivos em outro lugar (se os arquivos existirem basta pular a cópia). Instale o serviço a partir desse local. Quando batch reexecutar sua tarefa inicial, ele excluirá o diretório de trabalho da tarefa inicial e a criará novamente. Isso funciona porque o serviço tem bloqueios de arquivo no outro diretório e não no diretório de trabalho da tarefa inicial.

- **Evite criar junções de diretório no Windows**  
    As junções de diretório, às vezes chamadas de hard-links de diretório, são difíceis de lidar durante a tarefa e a limpeza do trabalho. Use simelos (soft-links) em vez de links rígidos.

- **Coletar os logs do agente batch se houver um problema**  
    Se você notar um problema envolvendo o comportamento de um nó ou tarefas em execução em um nó, recomenda-se coletar os registros do agente Batch antes de negociar os nós em questão. Os logs de agente batch podem ser coletados usando a API de logs de serviço Upload Batch. Esses registros podem ser fornecidos como parte de um bilhete de suporte para a Microsoft e ajudarão na solução de problemas e resolução de problemas.

## <a name="security"></a>Segurança

### <a name="security-isolation"></a>Isolamento de segurança

Para fins de isolamento, se o seu cenário requer isolar os trabalhos uns dos outros, então você deve isolar esses trabalhos, tendo-os em piscinas separadas. Um pool é o limite de isolamento de segurança em Batch e, por padrão, dois pools não são visíveis ou capazes de se comunicar entre si. Evite usar contas separadas em lote como meio de isolamento.

## <a name="moving"></a>Movendo

### <a name="move-batch-account-across-regions"></a>Mover conta em lote entre regiões 

Existem vários cenários em que você gostaria de mover sua conta de lote existente de uma região para outra. Por exemplo, você pode querer se mudar para outra região como parte do planejamento de recuperação de desastres.

As contas do Azure Batch não podem ser movidas de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente da sua conta Batch.  Em seguida, você pode encenar o recurso em outra região exportando a conta Batch para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo para a nova região. Depois de carregar o modelo para a nova região, você terá que recriar certificados, horários de trabalho e pacotes de aplicativos. Para efetuar as alterações e concluir a movimentação da conta Batch, lembre-se de excluir a conta batch original ou o grupo de recursos.  

Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


