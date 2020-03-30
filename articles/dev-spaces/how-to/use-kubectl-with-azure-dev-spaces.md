---
title: Como usar kubectl com espaços de dev do Azure
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Aprenda a usar comandos kubectl dentro de um espaço de dev em um cluster azure Kubernetes Service com Azure Dev Spaces ativado
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438358"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usar kubectl com um Azure Dev Space

É possível acessar o cluster do Kubernetes em um Azure Dev Space e usar ferramentas do Kubernetes existentes, como `kubectl`.

Executar o comando `az aks use-dev-spaces` adicionará automaticamente um contexto de configuração `kubectl`, portanto, o kubectl já deverá estar conectado ao cluster do Kubernetes do Azure Dev Spaces. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts`. 
- Alterar contexto: `kubectl config use-context <context-name>`
- Exiba o painel do Kubernetes: execute `kubectl proxy` e, em seguida, abra o navegador no endereço que este comando emitir (acrescente `/ui` à URL para navegar até o painel do Kubernetes).
- Liste os serviços em execução no espaço padrão azure Dev Spaces chamado *default*:`kubectl get services --namespace=default`

