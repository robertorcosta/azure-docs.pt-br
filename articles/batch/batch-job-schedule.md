---
title: Agende seus trabalhos
description: Use o agendamento de trabalho para gerenciar suas tarefas.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672427"
---
# <a name="schedule-jobs-for-efficiency"></a>Agendar trabalhos para eficiência

Agendar trabalhos em lote permite que você priorize os trabalhos que deseja executar primeiro, levando em conta tarefas que têm dependências de outras tarefas. Ao agendar seus trabalhos, você pode ter certeza de usar a menor quantidade de recursos. Os nós podem ser desativados quando não são necessários, tarefas que dependem de outras tarefas são giradas apenas no tempo otimizando os fluxos de trabalho. Apenas um trabalho de cada vez é executado. Um novo não começará até que o anterior seja concluído. Você pode definir o seu trabalho para completar automaticamente. 

## <a name="benefit-of-job-scheduling"></a>Benefício do agendamento de empregos

A vantagem de agendar empregos é que você pode especificar um cronograma para a criação de empregos. As tarefas que você agenda usando a tarefa de gerente de trabalho estão associadas a um trabalho. A tarefa de gerente de trabalho criará tarefas para o trabalho. Para isso, a tarefa de gerente de trabalho precisa autenticar com a conta Batch. Use o token de acesso AZ_BATCH_AUTHENTICATION_TOKEN. O token permitirá o acesso ao resto do trabalho. 

## <a name="use-the-portal-to-schedule-a-job"></a>Use o portal para agendar um trabalho

   1. Faça login no [portal Azure](https://portal.azure.com/).

   2. Selecione a conta Lote em que deseja agendar trabalhos.

   3. Selecione **Adicionar** para criar um novo cronograma de trabalho e preencha o **formulário Básico**.



![Agende um trabalho][1]

**Carteira de trabalho :** O identificador exclusivo para este horário de trabalho.

**Nome de exibição**: O nome de exibição para o trabalho não precisa ser único, mas tem um comprimento máximo de 1024 caracteres.

**Não corra até**: Especifica o tempo mais cedo que o trabalho será executado. Se você não definir isso, o cronograma fica pronto para executar trabalhos imediatamente.

**Não corra atrás**: Nenhum trabalho executado após o tempo que você definir aqui. Se você não especificar uma hora, então você está criando um cronograma de trabalho recorrente que permanece ativo até que você o encerre explicitamente.

**Intervalo de recorrência**: Você pode especificar a quantidade de tempo entre os trabalhos. Você pode ter apenas um emprego por vez agendado, portanto, se for a hora de criar um novo emprego um cronograma de trabalho, mas o trabalho anterior ainda está em execução, o serviço Batch não criará o novo emprego até que o trabalho anterior termine.  

**Janela de início**: Aqui você especifica o intervalo de tempo, a partir da hora em que o cronograma indica que um trabalho deve ser criado, até que ele deve ser concluído. Se o trabalho atual não for concluído durante a janela, o próximo trabalho não começará.

Na parte inferior do formulário básico, você especificará o pool no qual deseja que o trabalho seja executado. Para encontrar as informações de id do pool, selecione **Atualizar**. 

![Especificar pool][2]


**Identificação do pool :** Identifique o pool em que você executará o trabalho.

**Tarefa de configuração do trabalho**: Selecione **Atualizar** para nomear a tarefa de Job Manager, bem como as tarefas de preparação e liberação do trabalho, se você estiver usando-as.

**Prioridade:** Dar prioridade ao trabalho.

**Tempo máximo do relógio**de parede : Defina o tempo máximo que o trabalho pode executar. Se não for concluído dentro do prazo, Batch encerra o trabalho. Se você não definir isso, então não há limite de tempo para o trabalho.

**Contagem máxima de repetição de tarefas**: Especifique o número de vezes que uma tarefa pode ser repetida até um máximo de quatro vezes. Isso não é o mesmo que o número de repetições que uma chamada de API pode ter.

**Quando todas as tarefas são concluídas:** O padrão não é ação.

**Quando uma tarefa falha:** O padrão não é ação. Uma tarefa falha se a contagem de repetições estiver esgotada ou se houve um erro ao iniciar a tarefa. 

Depois de selecionar **Salvar**, se você for para **agendamentos de trabalho** na navegação à esquerda, você pode acompanhar a execução do trabalho, selecionando informações de **execução**.


## <a name="for-more-information"></a>Para obter mais informações

Para gerenciar um trabalho usando o Azure CLI, consulte [az batch job-schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Próximas etapas

[Crie dependências de tarefas para executar tarefas que dependem de outras tarefas.](batch-task-dependencies.md)





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


