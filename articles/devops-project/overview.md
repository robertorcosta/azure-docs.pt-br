---
title: Visão geral do Azure DevOps Starter | Microsoft Docs
description: Entender o valor do Azure DevOps Starter
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
ms.openlocfilehash: 99a1fdb8caff9953041c996d0f5581318ce11c66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233679"
---
# <a name="overview-of-azure-devops-starter"></a>Visão geral do Azure DevOps Starter

 Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Ele ajuda você a iniciar seu tipo de aplicativo favorito no serviço do Azure de sua escolha em apenas algumas etapas rápidas do portal do Azure. 

 O DevOps Starter configura tudo o que você precisa para desenvolver, implantar e monitorar seu aplicativo. Você pode usar o painel inicial do DevOps para monitorar confirmações, compilações e implantações de código, tudo a partir de uma única exibição no portal do Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantagens de usar o DevOps Starter

  O iniciante do DevOps automatiza a configuração de um pipeline de integração contínua (CI) e entrega contínua (CD) para o Azure.  É possível iniciar com o código existente ou usar um dos aplicativos de exemplo fornecidos. Em seguida, você poderá implantar rapidamente esse aplicativo em vários serviços do Azure, como Máquinas Virtuais, Serviço de Aplicativo, AKS (Serviço de Kubernetes do Azure), Banco de Dados SQL do Azure e Azure Service Fabric.  

  O iniciante DevOps faz todo o trabalho para a configuração inicial de um pipeline DevOps, incluindo tudo, desde a configuração do repositório git inicial, a configuração do pipeline de CI/CD, a criação de um recurso Application Insights para monitoramento e o fornecimento de uma única exibição de toda a solução com a criação de um painel de DevOps Projects no portal do Azure.

Você pode usar o DevOps Starter para:

* Implantar rapidamente seu aplicativo no Azure
* Automatizar a configuração de um pipeline de CI/CD
* Exibir e compreender como configurar corretamente um pipeline de CI/CD
* Personalizar ainda mais os pipelines de lançamento com base em seus cenários específicos

## <a name="how-to-use-devops-starter"></a>Como usar o DevOps Starter?

  O iniciante DevOps está disponível na portal do Azure. Você cria um recurso inicial do DevOps exatamente como você cria qualquer outro recurso do Azure no Portal. O DevOps Projects fornece uma experiência passo a passo do tipo assistente para as várias opções de configuração.  

Você pode escolher várias opções de configuração como parte da instalação inicial. Estas opções incluem:

* Usar o aplicativo de exemplo fornecido ou trazer seu próprio código
* Selecionar uma linguagem de aplicativo
* Escolher uma estrutura de aplicativo com base na linguagem
* Selecionar um serviço do Azure (destino de implantação)
* Criar uma nova organização do Azure DevOps ou usar uma organização existente 
* Escolher sua assinatura do Azure
* Escolher o local dos serviços do Azure
* Escolher entre vários tipos de preços dos serviços do Azure

Depois de usar o DevOps Starter, você também pode excluir todos os recursos de um único lugar do painel de início do DevOps no portal do Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps início e integração do Azure DevOps

O iniciante do DevOps é equipado com o Azure DevOps. O iniciante do DevOps automatiza todo o trabalho necessário no Azure Pipelines para configurar um pipeline de CI/CD. Ele cria um repositório Git em uma organização nova ou existente do Azure DevOps, depois confirma um aplicativo de exemplo ou seu código existente para um novo repositório Git.  

A automação também estabelece um gatilho de CI para o build, de modo que cada nova confirmação de código inicia um build. O iniciante do DevOps cria um gatilho de CD e implanta todas as novas compilações bem-sucedidas para o serviço do Azure de sua escolha.  

Os pipelines de build e de lançamento podem ser personalizados para cenários adicionais. Também é possível clonar os pipelines de build e de lançamento para usar em outros projetos.

Depois de criar o iniciador do DevOps, você pode:

* Personalizar seu pipeline de compilação e versão
* Usar solicitações de pull para gerenciar o fluxo de seu código e manter a qualidade alta
* Teste e compile cada confirmação antes de mesclar seu código a fim de elevar o nível de qualidade
* Acompanhar a lista de pendências e os problemas do aplicativo

## <a name="getting-started-with-devops-starter"></a>Introdução ao DevOps Starter

* [Introdução ao DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Vídeos de início do DevOps

* [Criar CI/CD com o iniciador DevOps do Azure](https://www.youtube.com/watch?v=NuYDAs3kNV8)
