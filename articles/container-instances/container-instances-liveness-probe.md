---
title: Configure o teste de vivacidade na instância do contêiner
description: Saiba como configurar investigações de atividade para reiniciar contêineres não íntegros em Instâncias de Contêiner do Azure
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934159"
---
# <a name="configure-liveness-probes"></a>Configurar investigações de atividade

As aplicações em contêiner podem funcionar por longos períodos de tempo, resultando em estados quebrados que podem precisar ser reparados reiniciando o contêiner. O Azure Container Instances suporta testes de vida para que você possa configurar seus contêineres dentro do grupo de contêineres para reiniciar se a funcionalidade crítica não estiver funcionando. A sonda de vida se comporta como uma [sonda kubernetes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de atividade, demonstrando a reinicialização automática de um contêiner não íntegro simulado.

O Azure Container Instances também suporta [testes de prontidão,](container-instances-readiness-probe.md)que você pode configurar para garantir que o tráfego chegue a um contêiner somente quando estiver pronto para ele.

> [!NOTE]
> Atualmente, você não pode usar um teste de vida em um grupo de contêineres implantado em uma rede virtual.

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

A implantação inclui `command` uma propriedade definindo um comando inicial que é executado quando o contêiner começa a ser executado pela primeira vez. Esta propriedade aceita uma matriz de strings. Este comando simula o recipiente entrando em um estado insalubre.

Primeiro, ele começa uma sessão de `healthy` bash `/tmp` e cria um arquivo chamado dentro do diretório. Em seguida, dorme por 30 segundos antes de excluir o arquivo e, em seguida, entra em um sono de 10 minutos:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Comando de atividade

Essa implantação define `livenessProbe` um que `exec` suporta um comando de vida que age como a verificação de vida. Se este comando sair com um valor não zero, o contêiner `healthy` é morto e reiniciado, sinalizando que o arquivo não poderia ser encontrado. Se este comando sair com sucesso com o código de saída 0, nenhuma ação será tomada.

A propriedade `periodSeconds` indica que o comando deve ser executado a cada cinco segundos.

## <a name="verify-liveness-output"></a>Verificar a saída da atividade

Nos 30 primeiros segundos, o arquivo `healthy` criado pelo comando inicial existe. Quando o comando liveness `healthy` verifica a existência do arquivo, o código de status retorna 0, sinalizando sucesso, de modo que não ocorre reinicialização.

Após 30 segundos, o `cat /tmp/healthy` comando começa a falhar, causando eventos insalubres e matando.

Esses eventos podem ser exibidos do Portal do Azure ou na CLI do Azure.

![Evento não íntegro no portal][portal-unhealthy]

Ao visualizar os eventos no portal Azure, eventos do tipo `Unhealthy` são acionados após a falha do comando liveness. O evento subseqüente é do tipo, `Killing`significando uma exclusão de contêiner para que uma reinicialização possa começar. A contagem de reinicialização para o contêiner aumenta cada vez que este evento ocorre.

As reinicializações são concluídas no local para que recursos como endereços IP públicos e conteúdos específicos de nós sejam preservados.

![Reinicialização de contador do portal][portal-restart]

Se o teste de vida falhar continuamente e desencadear muitas reinicializações, o contêiner entra em um atraso exponencial de recuo.

## <a name="liveness-probes-and-restart-policies"></a>Políticas de investigação de atividade e reinicialização

As políticas de reinicialização substituem o comportamento de reinicialização acionado pelas investigações de atividade. Por exemplo, se `restartPolicy = Never` você definir um *teste de* vida e uma sonda de vida, o grupo de contêineres não reiniciará por causa de uma verificação de vida com falha. Em vez disso, o grupo de contêineres `Never`adere à política de reinicialização do grupo de contêineres .

## <a name="next-steps"></a>Próximas etapas

Cenários com base em tarefas podem exigir que a investigação de atividade permita reinicializações automáticas se uma função pré-requisitada não estiver funcionando corretamente. Para obter mais informações sobre a execução de contêineres com base em tarefas, consulte [Executar tarefas em contêineres nas Instâncias de Contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
