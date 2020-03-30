---
title: Verifique a saúde do registro
description: Saiba como executar um comando de diagnóstico rápido para identificar problemas comuns ao usar um registro de contêiner do Azure, incluindo configuração local do Docker e conectividade com o registro
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456431"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verifique a saúde de um registro de contêineres do Azure

Ao usar um registro de contêiner do Azure, você pode ocasionalmente encontrar problemas. Por exemplo, você pode não ser capaz de puxar uma imagem de contêiner por causa de um problema com o Docker em seu ambiente local. Ou, um problema de rede pode impedi-lo de se conectar ao registro. 

Como primeiro passo de diagnóstico, execute o comando [az acr check-health][az-acr-check-health] para obter informações sobre a saúde do ambiente e, opcionalmente, acesso a um registro de destino. Este comando está disponível na versão 2.0.67 do Azure CLI ou posterior. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Executar az acr check-health

Os exemplos a seguir mostram `az acr check-health` maneiras diferentes de executar o comando.

> [!NOTE]
> Se você executar o comando no Azure Cloud Shell, o ambiente local não será verificado. No entanto, você pode verificar o acesso a um registro de destino.

### <a name="check-the-environment-only"></a>Verifique apenas o ambiente

Para verificar a configuração local do daemon do Docker, da versão CLI e do cliente Helm, execute o comando sem parâmetros adicionais:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Verifique o ambiente e um registro de destino

Para verificar o acesso a um registro, bem como realizar verificações de ambiente local, passe o nome de um registro de destino. Por exemplo: 

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Relatório de erros

O comando registra informações na saída padrão. Se um problema for detectado, ele fornece um código de erro e uma descrição. Para obter mais informações sobre os códigos e possíveis soluções, consulte a referência de [erro](container-registry-health-error-reference.md).

Por padrão, o comando pára sempre que encontra um erro. Você também pode executar o comando para que ele forneça saída para todas as verificações de saúde, mesmo se erros forem encontrados. Adicione `--ignore-errors` o parâmetro, como mostrado nos exemplos a seguir:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Saída de exemplo:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre códigos de erro retornados pelo comando [az acr check-health,][az-acr-check-health] consulte a [referência de erro de verificação de saúde](container-registry-health-error-reference.md).

Consulte o [FAQ](container-registry-faq.md) para perguntas frequentes e outras questões conhecidas sobre o Registro de Contêineres do Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
