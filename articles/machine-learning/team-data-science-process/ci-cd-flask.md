---
title: Crie um pipeline de CI/CD com a Azure Pipelines - Team Data Science Process
description: Crie um pipeline de integração contínua e entrega contínua para aplicações de Inteligência Artificial (IA) usando Docker e Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721822"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Crie pipelines de CI/CD para aplicativos de IA usando Azure Pipelines, Docker e Kubernetes

Um aplicativo de Inteligência Artificial (IA) é um código de aplicação incorporado com um modelo de aprendizado de máquina pré-treinado (ML). Há sempre dois fluxos de trabalho para um aplicativo de IA: cientistas de dados constroem o modelo ML, e desenvolvedores de aplicativos constroem o aplicativo e o expõem aos usuários finais para consumir. Este artigo descreve como implementar um pipeline de integração contínua e entrega contínua (CI/CD) para um aplicativo de IA que incorpora o modelo ML no código-fonte do aplicativo. O código de exemplo e o tutorial usam um aplicativo web Python Flask e buscam um modelo pré-treinado de uma conta privada de armazenamento blob do Azure. Você também pode usar uma conta de armazenamento AWS S3.

> [!NOTE]
> O processo a seguir é uma das várias maneiras de fazer CI/CD. Há alternativas para este ferramentae os pré-requisitos.

## <a name="source-code-tutorial-and-prerequisites"></a>Código-fonte, tutorial e pré-requisitos

Você pode baixar [código fonte](https://github.com/Azure/DevOps-For-AI-Apps) e um [tutorial detalhado](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) do GitHub. Siga as etapas tutoriais para implementar um pipeline de CI/CD para sua própria aplicação.

Para usar o código-fonte e tutorial baixados, você precisa dos seguintes pré-requisitos: 

- O [repositório de código-fonte](https://github.com/Azure/DevOps-For-AI-Apps) bifurcou na sua conta do GitHub
- Uma [Organização DevOps Azure](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- Um [cluster Azure Container Service for Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para executar comandos e buscar configuração a partir do cluster AKS 
- Uma [conta ACR (ACR) do Azure Container Registry](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Resumo do pipeline CI/CD

Cada novo git commit começa o oleoduto Build. A compilação puxa com segurança o modelo ML mais recente de uma conta de armazenamento blob e o embala com o código do aplicativo em um único recipiente. Essa dissociação do desenvolvimento de aplicativos e fluxos de trabalho de ciência de dados garante que o aplicativo de produção esteja sempre executando o código mais recente com o modelo ML mais recente. Se o aplicativo passar nos testes, o pipeline armazena com segurança a imagem de compilação em um contêiner Docker no ACR. O pipeline de liberação então implanta o contêiner usando AKS. 

## <a name="cicd-pipeline-steps"></a>Etapas do gasoduto CI/CD

O diagrama e as etapas a seguir descrevem a arquitetura do gasoduto CI/CD:

![Arquitetura do gasoduto CI/CD](./media/ci-cd-flask/architecture.png)

1. Os desenvolvedores trabalham no código do aplicativo no IDE de sua escolha.
2. Os desenvolvedores comprometem o código com o Azure Repos, GitHub ou outro provedor de controle de origem do Git. 
3. Separadamente, os cientistas de dados trabalham no desenvolvimento de seu modelo ML.
4. Os cientistas de dados publicam o modelo acabado em um repositório modelo, neste caso uma conta de armazenamento blob. 
5. A Azure Pipelines inicia uma construção baseada no compromisso git.
6. O pipeline Build puxa o modelo ML mais recente do armazenamento blob e cria um contêiner.
7. O pipeline empurra a imagem de compilação para o repositório de imagem privado no ACR.
8. O gasoduto Release começa com base na construção bem sucedida.
9. O pipeline puxa a imagem mais recente do ACR e a implanta através do cluster Kubernetes no AKS.
10. As solicitações dos usuários para o aplicativo passam pelo servidor DNS.
11. O servidor DNS passa as solicitações para um balanceador de carga e envia respostas de volta aos usuários.

## <a name="see-also"></a>Confira também

- [TDSP (Processo de Ciência de dados de Equipe)](/azure/machine-learning/team-data-science-process/)
- [AML (Azure Machine Learning)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [AKS (Serviço de Kubernetes do Azure)](/azure/aks/intro-kubernetes)
