---
title: Conceitos técnicos para ofertas de contêiner do Azure – Marketplace comercial da Microsoft
description: Recursos técnicos e diretrizes para ajudá-lo a configurar uma oferta de contêiner no Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700972"
---
# <a name="create-an-azure-container-offer"></a>Criar uma oferta de contêiner do Azure

Este artigo fornece recursos técnicos e recomendações para ajudá-lo a criar uma oferta de contêiner no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Confira a [documentação das Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances) para ver início rápido, tutoriais e amostras.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta.

Além do domínio da solução, a equipe de engenharia deve ter conhecimento das seguintes tecnologias Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento prático de [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar sua imagem do contêiner:

- [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Analise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/). Se você estiver usando o Visual Studio, examine as ferramentas disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Crie a imagem do contêiner

Para saber mais, confira os tutorais a seguir:

- [Tutorial: Criar uma imagem de contêiner para implantação nas Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Tutorial: Criar e implantar imagens de contêineres na nuvem com as Tarefas do Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Próximas etapas

- [Criar oferta de contêiner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
