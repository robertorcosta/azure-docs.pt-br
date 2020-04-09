---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso de instância de contêiner Do Azure a partir do Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875092"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Crie um recurso Azure Container Instance a partir da CLI do Azure

O YAML abaixo define o recurso Azure Container Instance. Copie e cole o conteúdo em `my-aci.yaml` um novo arquivo, nomeado e substitua os valores comentados pelos seus. Consulte o [formato do modelo][template-format] para YAML válido. Consulte os [repositórios e imagens][repositories-and-images] do contêiner para obter os nomes de imagem disponíveis e seu repositório correspondente. Para obter mais informações sobre a referência YAML para instâncias de contêiner, consulte [referência YAML: Azure Container Instances][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nem todos os locais têm a mesma disponibilidade de CPU e memória. Consulte a tabela [de localização e recursos][location-to-resource] para a listagem de recursos disponíveis para contêineres por localização e SISTEMA OPERACIONAL.

Vamos contar com o arquivo YAML que [`az container create`][azure-container-create] criamos para o comando. A partir da CLI do `az container create` Azure, execute o comando substituindo o `<resource-group>` seu. Além disso, para garantir valores dentro de uma implantação YAML, consulte [valores seguros][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A saída do `Running...` comando é válida, após algum tempo a saída muda para uma seqüência JSON representando o recurso ACI recém-criado. A imagem do contêiner é mais do que provável que não esteja disponível por um tempo, mas o recurso agora está implantado.

> [!TIP]
> Preste muita atenção aos locais de visualização pública das ofertas do Azure Cognitive Service, pois o YAML precisará ser ajustado de acordo com o local.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
