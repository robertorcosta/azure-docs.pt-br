---
title: Configurar o cluster de Azure Dev Spaces para usar o Helm 3 (visualização)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Saiba como configurar o cluster de espaços de desenvolvimento para usar o Helm 3
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202254"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Configurar o cluster de Azure Dev Spaces para usar o Helm 3 (visualização)

O Azure Dev Spaces usa Helm 2 por padrão para instalar serviços de usuário em espaços de desenvolvimento no cluster AKS. Você pode habilitar Azure Dev Spaces para usar Helm 3 em vez de Helm 2 Instalando serviços de usuário em espaços de desenvolvimento. Independentemente da versão do Helm Azure Dev Spaces usa para instalar os serviços de usuário, você pode continuar usando o cliente Helm 2 ou 3 para gerenciar suas próprias versões no mesmo cluster.

Quando você habilita o Helm 3, o Azure Dev Spaces se comporta de maneira diferente ao instalar os serviços de usuário em espaços de desenvolvimento das seguintes maneiras:

* O gaveta não é mais implantado em seu cluster no namespace *azds* .
* O Helm armazena informações de versão no namespace em que um serviço é instalado em vez do namespace *azds* .
* As informações de versão do Helm 3 permanecem no namespace em que um serviço é instalado após a exclusão de um controlador.
* Você pode interagir diretamente com qualquer versão gerenciada pelo Azure Dev Spaces no cluster usando o cliente Helm 3.

Neste guia, você aprenderá a habilitar o Helm 3 para Azure Dev Spaces instalar os serviços de usuário em espaços de desenvolvimento.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Prerequisites

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure][azure-cli] instalada.

### <a name="register-the-helm3preview-preview-feature"></a>Registrar o recurso de visualização do Helm3Preview

Para permitir que Azure Dev Spaces use o Helm 3 para instalar os serviços de usuário em espaços de desenvolvimento, primeiro habilite o sinalizador de recurso *Helm3Preview* em sua assinatura usando o comando *AZ Feature Register* :

> [!WARNING]
> Qualquer cluster AKS no qual você habilitar Azure Dev Spaces com o sinalizador de recurso *Helm3Preview* usará essa experiência de visualização. Para continuar a habilitar o Azure Dev Spaces totalmente suportado em clusters AKS, não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura de teste ou desenvolvimento separada do Azure para testar os recursos de visualização.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Demora alguns minutos para que o registro seja concluído. Verifique o status do registro usando o comando *AZ Feature show* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Quando o *estado* for *registrado*, atualize o registro de *Microsoft. DevSpaces* usando *AZ Provider Register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam enquanto esse recurso está em versão prévia:

* Você não pode usar esse recurso em clusters AKS com cargas de trabalho existentes. Você deve criar um novo cluster AKS.

## <a name="create-your-cluster"></a>Criar o cluster

Crie um novo cluster AKS em uma região que tenha esse recurso de visualização. Os comandos abaixo criam um grupo de recursos chamado *MyResource* Group e um novo cluster AKs chamado *MyAKS* na região *do Sul EUA Central* :

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Habilitar Azure Dev Spaces

Use o comando *use-dev-Spaces* para habilitar espaços de desenvolvimento em seu cluster AKs e siga os prompts. O comando abaixo habilita espaços de desenvolvimento no cluster *MyAKS* no grupo *MyResource* Group e cria um espaço de desenvolvimento padrão.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Verificar se os espaços de desenvolvimento estão executando o Helm 3

Verifique se o gaveta não está em execução listando as implantações no namespace *azds* :

```cmd
kubectl get deployment -n azds
```

Confirmar que *a implantação do gaveta* não está em execução no namespace azds. Por exemplo:

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