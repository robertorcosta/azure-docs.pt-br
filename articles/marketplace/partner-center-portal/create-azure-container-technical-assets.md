---
title: Conceitos técnicos para ofertas de contêiner do Azure-Microsoft Commercial Marketplace
description: Recursos técnicos e diretrizes para ajudá-lo a configurar uma oferta de contêiner no Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: b3c6f88df151cc497f0de670d5d78a05c7477459
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791877"
---
# <a name="create-an-azure-container-offer"></a>Criar uma oferta de contêiner do Azure

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de contêiner do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [preparar seus ativos técnicos do contêiner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) para Portal do Cloud Partner para gerenciar suas ofertas.

Este artigo fornece recursos técnicos e recomendações para ajudá-lo a criar uma oferta de contêiner no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Para obter guias de início rápido, tutoriais e exemplos, consulte a [documentação de instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta.

Além do seu domínio de solução, sua equipe de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage)e [rede do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de trabalho do [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerenciar sua imagem de contêiner:

- [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

É recomendável adicionar essas ferramentas ao seu ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [melhorar JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Examine as ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/) . Se você estiver usando o Visual Studio, examine as ferramentas disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Crie a imagem do contêiner

Para obter mais informações, consulte os seguintes tutoriais:

- [Tutorial: criar uma imagem de contêiner para implantação em instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Tutorial: criar e implantar imagens de contêiner na nuvem com tarefas de registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Próximas etapas

- [Crie sua oferta de contêiner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
