---
title: Montar volume dir vazio para grupo de contêineres
description: Saiba como montar um volume emptyDir para compartilhar dados entre os contêineres em um grupo de contêineres em Instâncias de Contêiner do Azure
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117746"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montar um volume emptyDir em Instâncias de Contêiner do Azure

Saiba como montar um volume *emptyDir* para compartilhar dados entre os contêineres em um grupo de contêineres em Instâncias de Contêiner do Azure. Use volumes *vaziosDir* como caches efêmeros para suas cargas de trabalho contêiner.

> [!NOTE]
> A montagem de um volume *emptyDir* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para os contêineres do Windows, você pode encontrar diferenças atuais da plataforma na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>Volume emptyDir

O volume *emptyDir* fornece um diretório gravável acessível para cada contêiner em um grupo de contêineres. Os contêineres no grupo podem ler e gravar os mesmos arquivos no volume, e ele pode ser montado usando-se os mesmos caminhos ou caminhos diferentes em cada contêiner.

Alguns exemplos usam para um volume *emptyDir*:

* Espaço transitório
* Ponto de verificação durante tarefas de longa execução
* Armazenar dados recuperados por um contêiner secundário e fornecidos por um contêiner de aplicativos

Os dados em um volume *emptyDir* são mantidos mesmo após falhas de contêiner. Os contêineres reiniciados, entretanto, não têm a garantia de manter os dados em um volume *emptyDir*. Se você parar um grupo de contêineres, o volume *vazioDir* não será persistido.

O tamanho máximo de um volume *de Dirdir vazio do* Linux é de 50 GB.

## <a name="mount-an-emptydir-volume"></a>Montar um volume emptyDir

Para montar um volume Dir vazio em uma instância de contêiner, você pode implantar usando um [modelo do Azure Resource Manager,](/azure/templates/microsoft.containerinstance/containergroups)um [arquivo YAML](container-instances-reference-yaml.md)ou outros métodos programáticos para implantar um grupo de contêineres.

Primeiro, preencha `volumes` a matriz `properties` na seção grupo de contêiner do arquivo. Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar o volume *emptyDir*, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em dois contêineres, cada um montando o volume *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver exemplos de implantação de grupo de contêineres, consulte [Implantar um grupo de vários contêineres usando um modelo de Gerenciador de recursos](container-instances-multi-container-group.md) e implantar um grupo de vários [contêineres usando um arquivo YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure](container-instances-volume-azure-files.md)
* [Montar um volume gitRepo em Instâncias de Contêiner do Azure](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em Instâncias de Contêiner do Azure](container-instances-volume-secret.md)