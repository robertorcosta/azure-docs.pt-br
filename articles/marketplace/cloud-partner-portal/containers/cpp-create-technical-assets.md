---
title: Criar ativos técnicos de imagem do Azure Containers | Mercado Azure
description: Crie os recursos técnicos para um contêiner do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280058"
---
# <a name="prepare-your-container-technical-assets"></a>Prepare seus recursos técnicos de contêiner

Este artigo descreve as etapas e requisitos para configurar uma oferta de contêiner do Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Revise a documentação [Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances), que fornece o Quickstarts, Tutoriais e Amostras.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses recursos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta.
 
Além do domínio da solução, a equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

-   Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/) 
-   Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento prático de [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar sua imagem do contêiner:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao ambiente de desenvolvimento:

-   [Explorador de armazenamento azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Código visual do estúdio](https://code.visualstudio.com/)
    *   Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também é recomendável que você revise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Crie a imagem do contêiner

Veja o seguinte para obter mais informações:

* [Tutorial: Crie uma imagem de contêiner para implantação no Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Tutorial: Construa e implante imagens de contêiner na nuvem com tarefas de registro de contêineres do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Próximas etapas

[Criar sua oferta de contêiner](./cpp-create-offer.md)
