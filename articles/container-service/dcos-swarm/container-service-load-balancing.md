---
title: (PRETERIDO) Balancear carga de contêineres em um cluster DC/SO do Azure
description: Balancear a carga entre vários contêineres em um cluster do Serviço de Contêiner do Azure DC/OS.
author: rgardler
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a8f863f16888e6eca2dbc72c5dd612c38edbe46e
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273369"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(PRETERIDO) Balancear a carga de contêineres em um cluster de controlador de domínio/sistema operacional do Serviço de Contêiner do Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, exploramos como criar um balanceador de carga interno em um Serviço de Contêiner do Azure gerenciado por DC/SO usando o Marathon-LB. Essa configuração permite que você dimensione seus aplicativos horizontalmente. Também permite que você aproveite os clusters de agentes públicos e privados colocando seus balanceadores de carga no cluster público e seus contêineres de aplicativo no cluster privado. Neste tutorial, você:

> [!div class="checklist"]
> * Configurar um balanceador de carga do Marathon
> * Implantar um aplicativo usando o balanceador de carga
> * Configurar um Azure Load Balancer

É necessário um cluster de DC/SO do ACS para concluir as etapas neste tutorial. Se necessário, [este exemplo de script](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar um para você.

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Visão geral do balanceamento de carga

Há duas camadas de balanceamento de carga em um cluster de DC/SO do Serviço de Contêiner do Azure: 

O **Azure Load Balancer** fornece pontos de entrada públicos (aqueles que os usuários finais acessarão). Um Azure LB é fornecido automaticamente pelo Serviço de Contêiner do Azure e, por padrão, é configurado para exibir as portas 80, 443 e 8080.

O **Balanceador de Carga Marathon** (marathon-lb) encaminha as solicitações de entrada às instâncias do contêiner que atendem a essas solicitações. Conforme dimensionamos os contêineres fornecendo nosso serviço Web, o marathon-lb adapta-se dinamicamente. Esse balanceador de carga não é fornecido por padrão em seu Serviço de Contêiner, mas é muito fácil de instalar.

## <a name="configure-marathon-load-balancer"></a>Configurar Balanceador de Carga Marathon

O Balanceador de Carga do Marathon reconfigura dinamicamente baseado nos contêineres que você implantou. Ele também é resistente à perda de um contêiner ou de um agente. Se isso ocorrer, o Apache Mesos reiniciará o contêiner em outro lugar e o marathon-lb se adaptará.

Acesse [https://shell.azure.com](https://shell.azure.com) para abrir o Cloud Shell no navegador.

Execute o seguinte comando para instalar o balanceador de carga Marathon no cluster do agente público.

```console
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Implantar aplicativo com balanceamento de carga

Agora que temos o pacote marathon-lb, podemos implantar um contêiner de aplicativo para o qual desejamos balancear a carga. 

Primeiro, obtenha o FQDN dos agentes expostos publicamente.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Em seguida, crie um arquivo chamado *hello-web.json* e copie o seguinte conteúdo. O rótulo `HAPROXY_0_VHOST` precisa ser atualizado com o FQDN dos agentes DC/SO. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Use a CLI do DC/SO para executar o aplicativo. Por padrão, o Marathon implanta o aplicativo no cluster particular. Isso significa que a implantação acima está acessível apenas por meio do balanceador de carga, que geralmente é o comportamento desejado.

```console
dcos marathon app add hello-web.json
```

Depois que o aplicativo tiver sido implantado, navegue até o FQDN do cluster do agente para exibir o aplicativo com balanceamento de carga.

![Imagem do aplicativo com balanceamento de carga](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Configurar o Azure Load Balancer

Por padrão, o Azure Load Balancer expõe as portas 80, 8080 e 443. Se você estiver usando uma dessas três portas (como ocorre no exemplo acima), não haverá mais nada a fazer. Você deve conseguir atingir o FQDN do balanceador de carga do agente e, cada vez que atualizar, encontrará um dos três servidores Web em round robin. 

Se você usar uma porta diferente, precisará adicionar uma regra de round robin e uma investigação ao balanceador de carga para a porta usada. Você pode fazer isso na [CLI do Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) com os comandos `azure network lb rule create` e `azure network lb probe create`.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre balanceamento de carga no ACS com os maratona balanceadores de carga Marathon e Azure, incluindo as seguintes ações:

> [!div class="checklist"]
> * Configurar um balanceador de carga do Marathon
> * Implantar um aplicativo usando o balanceador de carga
> * Configurar um Azure Load Balancer

Avance para o próximo tutorial para saber mais sobre como integrar o armazenamento do Azure ao DC/SO no Azure.

> [!div class="nextstepaction"]
> [Montar o compartilhamento de arquivos do Azure no cluster de DC/SO](container-service-dcos-fileshare.md)
