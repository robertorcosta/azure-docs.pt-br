---
title: Tutorial - Agende uma tarefa de ACR
description: Neste tutorial, aprenda a executar uma tarefa de registro de contêiner do Azure em um cronograma definido definindo um ou mais gatilhos de temporizador
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402872"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Execute uma tarefa ACR em um cronograma definido

Este tutorial mostra como executar uma [tarefa ACR](container-registry-tasks-overview.md) em um cronograma. Agende uma tarefa configurando um ou mais *gatilhos do temporizador*. Os gatilhos do temporizador podem ser usados sozinhos ou em combinação com outros gatilhos de tarefa.

Neste tutorial, aprenda sobre tarefas de agendamento e:

> [!div class="checklist"]
> * Crie uma tarefa com um gatilho temporizador
> * Gerenciar gatilhos do temporizador

Agendar uma tarefa é útil para cenários como:

* Execute uma carga de trabalho de contêiner para operações de manutenção programadas. Por exemplo, execute um aplicativo contêiner para remover imagens desnecessárias do seu registro.
* Execute um conjunto de testes em uma imagem de produção durante o dia de trabalho como parte do monitoramento ao vivo.

Você pode usar o Azure Cloud Shell ou uma instalação local do Azure CLI para executar os exemplos neste artigo. Se você quiser usá-lo localmente, a versão 2.0.68 ou posterior é necessária. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Sobre o agendamento de uma tarefa

* **Gatilho com expressão cron** - O gatilho do temporizador para uma tarefa usa uma *expressão cron*. A expressão é uma string com cinco campos especificando o minuto, hora, dia, mês e dia da semana para desencadear a tarefa. São suportadas freqüências de até uma vez por minuto.

  Por exemplo, `"0 12 * * Mon-Fri"` a expressão desencadeia uma tarefa ao meio-dia UTC em cada dia da semana. Veja [detalhes](#cron-expressions) mais tarde neste artigo.
* **Vários gatilhos de temporizador** - Adicionar vários temporizadores a uma tarefa é permitido, desde que os horários diferem.
    * Especifique vários gatilhos do temporizador quando criar a tarefa ou adicione-os mais tarde.
    * Opcionalmente, nomeie os gatilhos para facilitar o gerenciamento, ou as tarefas ACR fornecerão nomes de gatilho padrão.
    * Se os horários do temporsobre se sobrepõem de cada vez, o ACR Tasks aciona a tarefa na hora programada para cada temporizador.
* **Outros gatilhos de tarefas** - Em uma tarefa acionada pelo temporizador, você também pode habilitar gatilhos com base no [compromisso de código-fonte](container-registry-tutorial-build-task.md) ou [nas atualizações de imagem base](container-registry-tutorial-base-image-update.md). Como outras tarefas de ACR, você também pode [acionar manualmente][az-acr-task-run] uma tarefa programada.

## <a name="create-a-task-with-a-timer-trigger"></a>Crie uma tarefa com um gatilho temporizador

Quando você cria uma tarefa com o comando [az acr task create,][az-acr-task-create] você pode adicionar opcionalmente um gatilho temporizador. Adicione `--schedule` o parâmetro e passe uma expressão cron para o temporizador.

Como um exemplo simples, o comando `hello-world` a seguir dispara executando a imagem do Docker Hub todos os dias às 21:00 UTC. A tarefa é executada sem um contexto de código fonte.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Execute o comando [az acr task show][az-acr-task-show] para ver se o gatilho do temporizador está configurado. Por padrão, o gatilho de atualização de imagem base também está ativado.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Acione a tarefa manualmente com [a tarefa az acr executada][az-acr-task-run] para garantir que ela esteja configurada corretamente:

```azurecli
az acr task run --name mytask --registry myregistry
```

Se o contêiner for executado com sucesso, a saída será semelhante à seguinte:

```output
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Após o horário programado, execute o comando [az acr task-executruns][az-acr-task-list-runs] para verificar se o temporizador ativou a tarefa como esperado:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Quando o temporizador é bem sucedido, a saída é semelhante à seguinte:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Gerenciar gatilhos do temporizador

Use os comandos [do temporizador de tarefa az acr][az-acr-task-timer] para gerenciar os gatilhos do temporizador para uma tarefa ACR.

### <a name="add-or-update-a-timer-trigger"></a>Adicionar ou atualizar um gatilho do temporizador

Depois que uma tarefa é criada, adicione opcionalmente um gatilho de temporizador usando o comando [az acr tasktimer add.][az-acr-task-timer-add] O exemplo a seguir adiciona um temporizador de ativação *do temporizador2* ao *mytask* criado anteriormente. Este temporizador desencadeia a tarefa todos os dias às 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Atualize o cronograma de um gatilho existente ou altere seu status usando o comando [az acr task timer update.][az-acr-task-timer-update] Por exemplo, atualize o gatilho chamado *timer2* para ativar a tarefa às 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Gatilhos do temporizador de lista

O [comando az acr task timer list][az-acr-task-timer-list] mostra os gatilhos do temporizador configurados para uma tarefa:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Saída de exemplo:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Remova um gatilho do temporizador

Use o comando [az acr task timer remove][az-acr-task-timer-remove] para remover um gatilho de temporizador de uma tarefa. O exemplo a seguir remove o gatilho *timer2* da *minhatarefa:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Expressões cron

A Cr Tasks usa a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões de cron. Expressões suportadas em Tarefas ACR têm cinco campos necessários separados por espaço branco:

`{minute} {hour} {day} {month} {day-of-week}`

O fuso horário utilizado com as expressões cron é O Tempo Universal Coordenado (UTC). As horas são em formato 24 horas.

> [!NOTE]
> ACR Tasks não `{second}` suporta `{year}` o ou campo em expressões cron. Se você copiar uma expressão cron usada em outro sistema, certifique-se de remover esses campos, se eles forem usados.

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando disparado  |
|---------|---------|---------|
|Um valor específico |<nobr>`"5 * * * *"`</nobr>|cada hora em 5 minutos após a hora|
|Todos os valores (`*`)|<nobr>`"* 5 * * *"`</nobr>|cada minuto da hora a partir das 5:00 UTC (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>`"0 1-3 * * *"`</nobr>|3 vezes por dia, às 13:00, 2:00 e 3:00 UTC|
|Um conjunto de valores (`,` operador)|<nobr>`"20,30,40 * * * *"`</nobr>|3 vezes por hora, em 20 minutos, 30 minutos, e 40 minutos depois da hora|
|Um valor de intervalo (`/` operador)|<nobr>`"*/10 * * * *"`</nobr>|6 vezes por hora, em 10 minutos, 20 minutos, e assim por diante, depois da hora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Exemplos de cron

|Exemplo|Quando disparado  |
|---------|---------|
|`"*/5 * * * *"`|uma vez a cada cinco minutos|
|`"0 * * * *"`|uma vez a cada hora|
|`"0 */2 * * *"`|uma vez a cada duas horas|
|`"0 9-17 * * *"`|uma vez a cada hora das 9:00 às 17:00 UTC|
|`"30 9 * * *"`|às 9:30 UTC todos os dias|
|`"30 9 * * 1-5"`|às 9:30 UTC todos os dias da semana|
|`"30 9 * Jan Mon"`|às 9:30 UTC todas as segundas-feiras de janeiro|

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados nesta série tutorial, incluindo o registro ou registros de contêineres, instância do contêiner, cofre de chaves e diretor de serviço, emita os seguintes comandos:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar tarefas do Registro de Contêineres do Azure que são acionadas automaticamente por um temporizador. 

Para um exemplo de usar uma tarefa programada para limpar repositórios em um registro, consulte [Purgar automaticamente imagens de um registro de contêiner do Azure](container-registry-auto-purge.md).

Para exemplos de tarefas desencadeadas por compromissos de código-fonte ou atualizações de imagem base, consulte outros artigos na [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
