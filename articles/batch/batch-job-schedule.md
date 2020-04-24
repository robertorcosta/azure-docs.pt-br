---
title: Agendar seus trabalhos
description: Use o agendamento de trabalho para gerenciar suas tarefas.
ms.topic: article
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 49b2064d38f9f646c6189d859479d2414569ff60
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116869"
---
# <a name="schedule-jobs-for-efficiency"></a>Agendar trabalhos para eficiência

O agendamento de trabalhos em lotes permite priorizar os trabalhos que você deseja executar primeiro ao levar em conta as tarefas que têm dependências em outras tarefas. Ao agendar seus trabalhos, você pode se certificar de usar a quantidade mínima de recursos. Os nós podem ser descomissionados quando não forem necessários, as tarefas que dependem de outras tarefas são giradas just in time, otimizando os fluxos de trabalho. Apenas um trabalho de cada vez é executado. Um novo não será iniciado até que o anterior seja concluído. Você pode definir seu trabalho para preenchimento automático. 

## <a name="benefit-of-job-scheduling"></a>Benefício do agendamento de trabalho

O benefício do agendamento de trabalhos é que você pode especificar um agendamento para a criação do trabalho. As tarefas que você agenda usando a tarefa do Gerenciador de trabalho são associadas a um trabalho. A tarefa do Gerenciador de trabalho criará tarefas para o trabalho. Para fazer isso, a tarefa do Gerenciador de trabalho precisa ser autenticada com a conta do lote. Use o token de acesso AZ_BATCH_AUTHENTICATION_TOKEN. O token permitirá o acesso ao restante do trabalho. 

## <a name="use-the-portal-to-schedule-a-job"></a>Usar o portal para agendar um trabalho

   1. Entre no [Portal do Azure](https://portal.azure.com/).

   2. Selecione a conta do lote na qual você deseja agendar trabalhos.

   3. Selecione **Adicionar** para criar uma nova agenda de trabalho e concluir o **formulário básico**.



![Agendar um trabalho][1]

**ID do plano de trabalho**: o identificador exclusivo para esta agenda de trabalho.

**Nome de exibição**: o nome para exibição do trabalho não precisa ser exclusivo, mas tem um comprimento máximo de 1024 caracteres.

**Não executar até**: especifica a primeira vez em que o trabalho será executado. Se você não definir isso, a agenda se tornará pronta para executar trabalhos imediatamente.

Não **executar após**: nenhum trabalho será executado após a hora definida aqui. Se você não especificar uma hora, você estará criando um agendamento de trabalho recorrente que permanece ativo até que você o Finalize explicitamente.

**Intervalo de recorrência**: você pode especificar o período de tempo entre os trabalhos. Você pode ter apenas um trabalho de cada vez agendado, portanto, se for o momento de criar um novo trabalho em uma agenda de trabalho, mas o trabalho anterior ainda estiver em execução, o serviço de lote não criará o novo trabalho até que o trabalho anterior seja concluído.  

**Iniciar janela**: aqui você especifica o intervalo de tempo, começando a partir do momento em que o agendamento indica que um trabalho deve ser criado, até que ele deva ser concluído. Se o trabalho atual não for concluído durante sua janela, o trabalho seguinte não será iniciado.

Na parte inferior do formulário básico, você especificará o pool no qual deseja que o trabalho seja executado. Para localizar as informações de ID do pool, selecione **Atualizar**. 

![Especificar pool][2]


**ID do pool**: identifique o pool no qual o trabalho será executado.

**Tarefa de configuração de trabalho**: selecione **Atualizar** para nomear a tarefa do Gerenciador de trabalho, bem como as tarefas de preparação e liberação do trabalho, se você as estiver usando.

**Prioridade**: dê prioridade ao trabalho.

**Tempo máximo do relógio de parede**: defina a quantidade máxima de tempo durante o qual o trabalho pode ser executado. Se ele não for concluído dentro do período de tempo, o lote encerrará o trabalho. Se você não definir isso, não haverá limite de tempo para o trabalho.

**Contagem máxima de tentativas de tarefa**: especifique o número de vezes que uma tarefa pode ser repetida até um máximo de quatro vezes. Isso não é o mesmo que o número de repetições que uma chamada de API pode ter.

**Quando todas as tarefas forem concluídas**: o padrão é nenhuma ação.

**Quando uma tarefa falha**: o padrão é nenhuma ação. Uma tarefa falhará se a contagem de repetição for esgotada ou houve um erro ao iniciar a tarefa. 

Depois de selecionar **salvar**, se você for para **agendas de trabalho** no painel de navegação esquerdo, poderá acompanhar a execução do trabalho, selecionando **informações de execução**.


## <a name="for-more-information"></a>Para obter mais informações

Para gerenciar um trabalho usando o CLI do Azure, consulte [AZ batch Jobs-Schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Próximas etapas

[Crie dependências de tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


