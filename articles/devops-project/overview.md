---
title: Visão geral do Azure DevOps Starter | Microsoft Docs
description: Saiba como o Azure DevOps Starter ajuda você a iniciar, implantar e gerenciar seu aplicativo de uma só exibição no portal do Azure.
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 3e6f28da37980717e15b05ab94c582aa5c77e9e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856855"
---
# <a name="overview-of-azure-devops-starter"></a>Visão geral do Azure DevOps Starter

 Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Ele ajuda você a iniciar seu tipo de aplicativo favorito no serviço do Azure de sua escolha em apenas algumas etapas rápidas do portal do Azure. 

 O DevOps Starter configura tudo de que você precisa para desenvolver, implantar e monitorar seu aplicativo. É possível usar o painel do DevOps Starter para monitorar confirmações, builds e implantações de código, tudo em uma só exibição no portal do Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantagens de usar o DevOps Starter

  O DevOps Starter automatiza a instalação de um pipeline inteiro de CI (integração contínua) e CD (entrega contínua) para o Azure.  É possível iniciar com o código existente ou usar um dos aplicativos de exemplo fornecidos. Em seguida, você poderá implantar rapidamente esse aplicativo em vários serviços do Azure, como Máquinas Virtuais, Serviço de Aplicativo, AKS (Serviço de Kubernetes do Azure), Banco de Dados SQL do Azure e Azure Service Fabric.  

  O DevOps Starter faz todo o trabalho de configuração inicial de um pipeline de DevOps, incluindo tudo desde a configuração do repositório inicial do Git, configuração do pipeline de CI/CD, criação de um recurso do Application Insights para monitorar e fornecer uma única exibição de toda a solução com a criação de um painel do DevOps Projects no portal do Azure.

É possível usar o DevOps Starter para:

* Implantar rapidamente seu aplicativo no Azure
* Automatizar a configuração de um pipeline de CI/CD
* Exibir e compreender como configurar corretamente um pipeline de CI/CD
* Personalizar ainda mais os pipelines de lançamento com base em seus cenários específicos

## <a name="how-to-use-devops-starter"></a>Como usar o DevOps Starter?

  O DevOps Starter está disponível no portal do Azure. Crie um recurso do DevOps Starter assim como você cria qualquer outro recurso do Azure no portal. O DevOps Projects fornece uma experiência passo a passo do tipo assistente para as várias opções de configuração.  

Você pode escolher várias opções de configuração como parte da instalação inicial. Estas opções incluem:

* Usar o aplicativo de exemplo fornecido ou trazer seu próprio código
* Selecionar uma linguagem de aplicativo
* Escolher uma estrutura de aplicativo com base na linguagem
* Selecionar um serviço do Azure (destino de implantação)
* Criar uma nova organização do Azure DevOps ou usar uma organização existente 
* Escolher sua assinatura do Azure
* Escolher o local dos serviços do Azure
* Escolher entre vários tipos de preços dos serviços do Azure

Depois de usar o DevOps Starter, também é possível excluir todos os recursos de um único local no painel do DevOps Starter no portal do Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>Integração entre o DevOps Starter e o Azure DevOps

O DevOps Starter faz parte da plataforma do Azure DevOps. O DevOps Starter automatiza todo o trabalho necessário no Azure Pipelines para configurar um pipeline de CI/CD. Ele cria um repositório Git em uma organização nova ou existente do Azure DevOps, depois confirma um aplicativo de exemplo ou seu código existente para um novo repositório Git.  

A automação também estabelece um gatilho de CI para o build, de modo que cada nova confirmação de código inicia um build. O DevOps Starter cria um gatilho de CD e implanta cada novo build bem-sucedido no serviço do Azure de sua escolha.  

Os pipelines de build e de lançamento podem ser personalizados para cenários adicionais. Também é possível clonar os pipelines de build e de lançamento para usar em outros projetos.

Depois de criar seu DevOps Starter, você pode:

* Personalizar seu pipeline de compilação e versão
* Usar solicitações de pull para gerenciar o fluxo de seu código e manter a qualidade alta
* Teste e compile cada confirmação antes de mesclar seu código a fim de elevar o nível de qualidade
* Acompanhar a lista de pendências e os problemas do aplicativo

## <a name="getting-started-with-devops-starter"></a>Introdução ao DevOps Starter

* [Introdução ao DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Vídeos sobre o DevOps Starter

* [Criar CI/CD com o Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)