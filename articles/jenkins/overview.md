---
title: Visão geral do Jenkins e do Azure
description: Hospede o servidor de automação de build e implantação do Jenkins no Azure e use os recursos de computação e armazenamento do Azure para estender os pipelines de CI/CD (integração e implantação contínuas).
keywords: jenkins, azure, devops, visão geral
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: a9297ebc116d75cfe1d4f37d4e9ada7d5198beae
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620178"
---
# <a name="azure-and-jenkins"></a>Azure e Jenkins

O [Jenkins](https://jenkins.io/) é um servidor de automação de software livre popular usado para configurar a integração e entrega contínuas (CI/CD) em projetos de software. Você pode hospedar a implantação do Jenkins no Azure ou estender a configuração existente do Jenkins usando os recursos do Azure. Os plug-ins do Jenkins também estão disponíveis para simplificar a CI/CD dos aplicativos para o Azure.

Este artigo é uma introdução ao uso do Azure com o Jenkins, fornecendo detalhes dos principais recursos do Azure disponíveis para os usuários do Jenkins. Para obter mais informações sobre como começar seu próprio servidor Jenkins no Azure, consulte [Criar um servidor Jenkins no Azure](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hospedar os servidores do Jenkins no Azure

Hospede o Jenkins no Azure para centralizar a automação de build e dimensionar a implantação, conforme as necessidades de seus projetos de software aumentam. Implante o Jenkins no Azure usando:
 
- [O modelo de solução do Jenkins](install-jenkins-solution-template.md) no Azure Marketplace.
- [Máquinas virtuais do Azure](/azure/virtual-machines/linux/overview). Consulte nosso [tutorial](tutorial-jenkins-github-docker-cicd.md) para criar uma instância do Jenkins em uma VM.
- Em um cluster Kubernetes em execução no [Serviço de Contêiner do Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), consulte nossas [instruções](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Monitore e gerencie a implantação do Azure Jenkins usando [logs do Azure Monitor](/azure/log-analytics/log-analytics-overview) e a [CLI do Azure](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Dimensionar a automação de build sob demanda

Adicione agentes de build à implantação existente do Jenkins para dimensionar a capacidade de build do Jenkins conforme o número de builds e a complexidade dos trabalhos e pipelines aumentam. Execute esses agentes de build em máquinas virtuais do Azure usando o [plug-in de Agentes de VM do Azure](https://plugins.jenkins.io/azure-vm-agents). Consulte nosso [tutorial](/azure/jenkins/jenkins-azure-vm-agents) para obter mais detalhes.

Depois de configurados com uma [entidade de serviço do Azure](/azure/azure-resource-manager/resource-group-overview), os trabalhos e pipelines do Jenkins podem usar essa credencial para:

- Armazene e arquive com segurança artefatos de build no [Armazenamento do Azure](/azure/storage/common/storage-introduction) usando o [plug-in do Armazenamento do Azure](https://plugins.jenkins.io/windows-azure-storage). Examine as [instruções de armazenamento do Jenkins](storage-java-jenkins-continuous-integration-solution.md) para saber mais.
- Gerenciar e configurar os recursos do Azure com a [CLI do Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Implantar o código nos serviços do Azure

Use os plug-ins do Jenkins para implantar seus aplicativos no Azure como parte dos pipelines de CI/CD do Jenkins. A implantação no [Serviço de Aplicativo do Azure](/azure/app-service/) e no [Serviço de Contêiner do Azure](/azure/container-service/kubernetes/) permite preparar, testar e liberar atualizações para os aplicativos sem gerenciar a infraestrutura subjacente.

 Plug-ins estão disponíveis para serem implantados nos seguintes serviços e ambientes:

- [Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers/app-service-linux-intro). Consulte o [tutorial](java-deploy-webapp-tutorial.md) para começar.
- [Serviço de Aplicativo do Azure](/azure/app-service/overview). Consulte as [instruções](deploy-Jenkins-app-service-plugin.md) para começar.
