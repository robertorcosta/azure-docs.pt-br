---
title: Configure o cluster Azure Dev Spaces para usar o Helm 3 (visualização)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Saiba como configurar seu cluster Dev Spaces para usar o Helm 3
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454288"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Configure o cluster Azure Dev Spaces para usar o Helm 3 (visualização)

O Azure Dev Spaces usa o Helm 2 por padrão para instalar serviços de usuário em espaços de dev em seu cluster AKS. Você pode habilitar o Azure Dev Spaces para usar o Helm 3 em vez do Helm 2 instalar serviços de usuário em espaços de dev. Independentemente da versão do Helm Azure Dev Spaces usada para instalar serviços de usuário, você pode continuar a usar o cliente Helm 2 ou 3 para gerenciar suas próprias versões no mesmo cluster.

Quando você habilita o Helm 3, o Azure Dev Spaces se comporta de forma diferente ao instalar serviços de usuário em espaços de v dev das seguintes maneiras:

* Tiller não está mais implantado no seu cluster no espaço de nome *azds.*
* O Helm armazena informações de lançamento no namespace onde um serviço é instalado em vez do namespace *do Azds.*
* As informações de versão do Helm 3 permanecem no namespace onde um serviço é instalado após a exclusão de um controlador.
* Você pode interagir diretamente com qualquer versão gerenciada pelo Azure Dev Spaces em seu cluster usando o cliente Helm 3.

Neste guia, você aprenderá como habilitar o Helm 3 para o Azure Dev Spaces para instalar serviços de usuário em espaços de dev.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure][azure-cli] instalada.

### <a name="register-the-helm3preview-preview-feature"></a>Registre o recurso de pré-visualização Helm3Preview

Para habilitar o Azure Dev Spaces a usar o Helm 3 para instalar serviços de usuário em espaços de dev, primeiro habilite o sinalizador de recurso *Helm3Preview* em sua assinatura usando o comando *az feature register:*

> [!WARNING]
> Qualquer cluster AKS que você habilitar a Azure Dev Spaces com o sinalizador de recurso *Helm3Preview* usará essa experiência de visualização. Para continuar a habilitar espaços Azure Dev totalmente suportados em clusters AKS, não habilite recursos de visualização em assinaturas de produção. Use uma assinatura separada do Teste ou desenvolvimento do Azure para testar recursos de visualização.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Leva alguns minutos para o registro ser concluído. Verifique o status de registro usando o comando *az feature show:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Quando o *estado* estiver *registrado, atualize*o registro do *Microsoft.DevSpaces* usando *o registro do provedor az*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam enquanto este recurso estiver em visualização:

* Você não pode usar esse recurso em clusters AKS com cargas de trabalho existentes. Você deve criar um novo cluster AKS.

## <a name="create-your-cluster"></a>Criar o cluster

Crie um novo cluster AKS em uma região que tenha esse recurso de visualização. Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um novo cluster AKS chamado *MyAKS*:

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Habilite os espaços de dev do Azure

Use o comando *use-dev-spaces* para ativar espaços de dev no cluster AKS e siga as instruções. O comando abaixo habilita o Dev Spaces no cluster *MyAKS*, no grupo *MyResourceGroup* e cria um espaço de desenvolvimento padrão.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Verificar que o Dev Spaces está executando o Helm 3

Verifique se o leme não está sendo executado listando as implantações no namespace *do azds:*

```cmd
kubectl get deployment -n azds
```

Confirme *que o implante de leme* não está sendo executado no namespace do AZDs. Por exemplo: 

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md