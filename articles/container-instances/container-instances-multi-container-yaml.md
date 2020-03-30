---
title: Tutorial - Implantar grupo multi-contêiner - YAML
description: Neste tutorial, você aprende a implantar um grupo de contêineres com vários contêineres em Instâncias de Contêiner do Azure usando um arquivo YAML com o Cli do Azure.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533601"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Tutorial: Implante um grupo de vários contêineres usando um arquivo YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Gerenciador de recursos](container-instances-multi-container-group.md)
>

As Instâncias de Contêiner do Azure são compatíveis com a implantação de vários contêineres em um único host utilizando um [grupo de contêineres](container-instances-container-groups.md). Um grupo de contêineres é útil ao criar um sidecar de aplicativo para registro, monitoramento ou qualquer outra configuração em que um serviço precise de um segundo processo conectado.

Neste tutorial, você segue etapas para executar uma configuração simples de sidecar de dois contêineres, implantando um [arquivo YAML](container-instances-reference-yaml.md) usando o Cli do Azure. Um arquivo YAML fornece um formato conciso para especificar as configurações de instância. Você aprenderá como:

> [!div class="checklist"]
> * Configure um arquivo YAML
> * Implantar o grupo de contêineres
> * Veja os registros dos contêineres

> [!NOTE]
> Grupos com vários contêineres são atualmente restritos a contêineres do Linux.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Configure um arquivo YAML

Para implantar um grupo de vários contêineres com o comando [az container create][az-container-create] no Azure CLI, você deve especificar a configuração do grupo de contêineres em um arquivo YAML. Em seguida, passe o arquivo YAML como um parâmetro para o comando.

Comece copiando o YAML a seguir em um novo arquivo chamado **deploy-aci.yaml**. No Azure Cloud Shell, você pode usar o Visual Studio Code para criar o arquivo em seu diretório de trabalho:

```
code deploy-aci.yaml
```

Esse arquivo YAML define um grupo de contêineres nomeado "myContainerGroup" com dois contêineres, um endereço IP público e duas portas expostas. Os contêineres são implantados a partir de imagens públicas da Microsoft. O primeiro contêiner no grupo executa um aplicativo Web voltado para a Internet. O segundo contêiner, o secundário, faz periodicamente solicitações HTTP para o aplicativo Web em execução no primeiro contêiner por meio da rede local do grupo de contêiner.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Para usar um registro privado `imageRegistryCredentials` de imagem de contêiner, adicione a propriedade ao grupo de contêineres, com valores modificados para o seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Implantar o grupo de contêineres

Crie um grupo de recursos com o comando [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o grupo de contêineres com o comando [az container create][az-container-create], passando o arquivo YAML como argumento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Exibir estado da implantação

Para exibir o estado da implantação, use o comando [az container show][az-container-show] a seguir:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se quiser exibir o aplicativo em execução, navegue até o endereço IP dele em seu navegador. Por exemplo, o IP é `52.168.26.124` nesta saída de exemplo:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Exibir logs do contêiner

Veja a saída de log de um contêiner usando o comando [az container logs][az-container-logs]. O argumento `--container-name` especifica o contêiner do qual efetuar pull dos logs. Neste exemplo, `aci-tutorial-app` o recipiente é especificado.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Saída:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os registros do contêiner sidecar, execute `aci-tutorial-sidecar` um comando semelhante especificando o contêiner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Saída:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Como você pode ver, o secundário está periodicamente fazendo uma solicitação HTTP ao aplicativo Web principal por meio da rede local do grupo a fim de garantir que ele esteja em execução. Este exemplo sidecar pode ser expandido para disparar um alerta `200 OK`se ele recebeu um código de resposta HTTP diferente de .

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um arquivo YAML para implantar um grupo de vários contêineres em Instâncias de Contêiner do Azure. Você aprendeu a:

> [!div class="checklist"]
> * Configure um arquivo YAML para um grupo de vários contêineres
> * Implantar o grupo de contêineres
> * Veja os registros dos contêineres

Você também pode especificar um grupo de vários contêineres usando um [modelo de Gerenciador de recursos](container-instances-multi-container-group.md). Um modelo de Gerenciador de recursos pode ser prontamente adaptado para cenários quando você precisar implantar recursos adicionais de serviço do Azure com o grupo de contêineres.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
