---
title: Configure o teste de prontidão na instância do contêiner
description: Saiba como configurar um teste para garantir que os contêineres no Azure Container Instances recebam solicitações somente quando estiverem prontos
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935691"
---
# <a name="configure-readiness-probes"></a>Configurar investigações de preparação

Para aplicativos contêiner que servem tráfego, você pode querer verificar se seu contêiner está pronto para lidar com solicitações recebidas. O Azure Container Instances suporta testes de prontidão para incluir configurações para que seu contêiner não possa ser acessado certas condições. A sonda de prontidão se comporta como uma [sonda de prontidão Kubernetes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) Por exemplo, um aplicativo de contêiner pode precisar carregar um grande conjunto de dados durante a inicialização, e você não quer que ele receba solicitações durante esse tempo.

Este artigo explica como implantar um grupo de contêineres que inclui uma sonda de prontidão, de modo que um contêiner só recebe tráfego quando a sonda é bem sucedida.

O Azure Container Instances também suporta [testes de vida,](container-instances-liveness-probe.md)que você pode configurar para causar a reinicialização automática de um recipiente insalubre.

> [!NOTE]
> Atualmente, você não pode usar um teste de prontidão em um grupo de contêineres implantado em uma rede virtual.

## <a name="yaml-configuration"></a>Configuração YAML

Como exemplo, crie `readiness-probe.yaml` um arquivo com o seguinte trecho que inclui um teste de prontidão. Este arquivo define um grupo de contêineres que consiste em um contêiner executando um pequeno aplicativo web. O aplicativo é implantado `mcr.microsoft.com/azuredocs/aci-helloworld` a partir da imagem pública. Este aplicativo contêiner também é demonstrado no [Deploy a container instance in Azure usando o Azure CLI](container-instances-quickstart.md) e outros quickstarts.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Comando inicial

A implantação inclui `command` uma propriedade definindo um comando inicial que é executado quando o contêiner começa a ser executado pela primeira vez. Esta propriedade aceita uma matriz de strings. Este comando simula uma época em que o aplicativo web é executado, mas o contêiner não está pronto. 

Primeiro, ele inicia uma sessão `node` de shell e executa um comando para iniciar o aplicativo web. Ele também inicia um comando para dormir por 240 `ready` segundos, `/tmp` após o qual ele cria um arquivo chamado dentro do diretório:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando de prontidão

Este arquivo YAML `readinessProbe` define um `exec` que suporta um comando de prontidão que age como a verificação de prontidão. Este exemplo de teste de comando `ready` de `/tmp` prontidão para a existência do arquivo no diretório.

Quando `ready` o arquivo não existe, o comando de prontidão sai com um valor não zero; o contêiner continua funcionando, mas não pode ser acessado. Quando o comando sai com sucesso com o código de saída 0, o contêiner está pronto para ser acessado. 

A `periodSeconds` propriedade designa que o comando de prontidão deve ser executado a cada 5 segundos. A sonda de prontidão é executada durante toda a vida do grupo de contêineres.

## <a name="example-deployment"></a>Exemplo de implantação

Execute o seguinte comando para implantar um grupo de contêineres com a configuração YAML anterior:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Ver verificações de prontidão

Neste exemplo, durante os primeiros 240 segundos, o comando `ready` de prontidão falha quando verifica a existência do arquivo. O código de status retornou sinais de que o contêiner não está pronto.

Esses eventos podem ser exibidos do Portal do Azure ou na CLI do Azure. Por exemplo, o portal `Unhealthy` mostra que eventos do tipo são acionados após a falha do comando de prontidão. 

![Evento não íntegro no portal][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verifique a prontidão do recipiente

Depois de iniciar o contêiner, você pode verificar se ele não está acessível inicialmente. Após o provisionamento, obtenha o endereço IP do grupo de contêineres:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Tente acessar o site enquanto o teste de prontidão falha:

```bash
wget <ipAddress>
```

A saída mostra que o site não está acessível inicialmente:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Após 240 segundos, o comando de prontidão é bem sucedido, sinalizando que o contêiner está pronto. Agora, quando você `wget` executa o comando, ele consegue:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Quando o contêiner estiver pronto, você também pode acessar o aplicativo da Web navegando até o endereço IP usando um navegador da Web.

> [!NOTE]
> A sonda de prontidão continua a funcionar durante toda a vida do grupo de contêineres. Se o comando de prontidão falhar mais tarde, o contêiner volta a ficar inacessível. 
> 

## <a name="next-steps"></a>Próximas etapas

Uma sonda de prontidão pode ser útil em cenários envolvendo grupos de vários contêineres que consistem em recipientes dependentes. Para obter mais informações sobre cenários de vários contêineres, consulte [grupos de contêineres em Instâncias de Contêiner do Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
