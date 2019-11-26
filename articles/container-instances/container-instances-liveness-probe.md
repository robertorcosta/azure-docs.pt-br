---
title: Set up liveness probe on container instance
description: Saiba como configurar investigações de atividade para reiniciar contêineres não íntegros em Instâncias de Contêiner do Azure
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 96d98d18a3f0ac666fb2c057216f7844b176d177
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481675"
---
# <a name="configure-liveness-probes"></a>Configurar investigações de atividade

Containerized applications may run for extended periods of time, resulting in broken states that may need to be repaired by restarting the container. Azure Container Instances supports liveness probes so that you can configure your containers within your container group to restart if critical functionality is not working. The liveness probe behaves like a [Kubernetes liveness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de atividade, demonstrando a reinicialização automática de um contêiner não íntegro simulado.

Azure Container Instances also supports [readiness probes](container-instances-readiness-probe.md), which you can configure to ensure that traffic reaches a container only when it's ready for it.

## <a name="yaml-deployment"></a>Implantação do YAML

Crie um arquivo `liveness-probe.yaml` com o snippet a seguir. Esse arquivo define um grupo de contêineres composto por um contêiner NGNIX que eventualmente se tornará não íntegro.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Execute o seguinte comando para implantar esse grupo de contêineres com a configuração YAML acima:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Comando inicial

The deployment defines a starting command to be run when the container first starts running, defined by the `command` property, which accepts an array of strings. Neste exemplo, ela inicia uma sessão de bash e crie um arquivo chamado `healthy` dentro do diretório `/tmp` por meio deste comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 It will then sleep for 30 seconds before deleting the file, then enters a 10-minute sleep.

### <a name="liveness-command"></a>Comando de atividade

This deployment defines a `livenessProbe` that supports an `exec` liveness command that acts as the liveness check. Se a saída desse comando for um valor diferente de zero, o contêiner será interrompido e reiniciado, sinalizando que não foi possível encontrar o arquivo `healthy`. Se a o comando for encerrado com êxito com o código de saída 0, nenhuma ação será executada.

A propriedade `periodSeconds` indica que o comando deve ser executado a cada cinco segundos.

## <a name="verify-liveness-output"></a>Verificar a saída da atividade

Nos 30 primeiros segundos, o arquivo `healthy` criado pelo comando inicial existe. Quando o comando de atividade verificar a existência do arquivo `healthy`, o código de status retornará um zero, indicando sucesso, portanto nenhuma reinicialização será executada.

Depois de 30 segundos, o `cat /tmp/healthy` começará a falhar, provocando a falta de integridade e eventos de eliminação.

Esses eventos podem ser exibidos do Portal do Azure ou na CLI do Azure.

![Evento não íntegro no portal][portal-unhealthy]

Ao exibir os eventos no Portal do Azure, os eventos do tipo `Unhealthy` serão acionados após a falha do comando de atividade. O evento subsequente será do tipo `Killing`, indicando a exclusão do contêiner para que a reinicialização possa começar. The restart count for the container increments each time this event  occurs.

As reinicializações ocorrem in-loco, portanto, recursos como endereços IP públicos e conteúdo específico ao nó serão preservados.

![Reinicialização de contador do portal][portal-restart]

Se investigação de atividade falhar continuamente e acionar muitas reinicializações, seu contêiner inserirá uma atraso de retirada exponencial.

## <a name="liveness-probes-and-restart-policies"></a>Políticas de investigação de atividade e reinicialização

As políticas de reinicialização substituem o comportamento de reinicialização acionado pelas investigações de atividade. For example, if you set a `restartPolicy = Never` *and* a liveness probe, the container group will not restart because of a failed liveness check. Em vez disso, o grupo de contêineres seguirá a política de reinicialização do grupo de contêineres de `Never`.

## <a name="next-steps"></a>Próximos passos

Cenários com base em tarefas podem exigir que a investigação de atividade permita reinicializações automáticas se uma função pré-requisitada não estiver funcionando corretamente. Para obter mais informações sobre a execução de contêineres com base em tarefas, consulte [Executar tarefas em contêineres nas Instâncias de Contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
