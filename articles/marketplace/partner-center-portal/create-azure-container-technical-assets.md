---
title: Criar um ativo técnico do Azure VM
description: Este artigo descreve as etapas e requisitos para configurar uma oferta de contêiner no Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730632"
---
# <a name="create-an-azure-vm-technical-asset"></a>Criar um ativo técnico do Azure VM

> [!IMPORTANT]
> Estamos mudando o gerenciamento de suas ofertas de contêineres Do Portal de Parceiros da Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [Prepare seus ativos técnicos de Contêiner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) para o Cloud Partner Portal para gerenciar suas ofertas.

Este artigo descreve as etapas e requisitos para configurar uma oferta de contêiner no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Para obter inicialização rápida, tutoriais e amostras, consulte [Instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar esses ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure quanto das tecnologias usadas para construir a oferta.

Além do domínio da solução, sua equipe de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho de [máquinas virtuais azure,](https://azure.microsoft.com/services/virtual-machines/) [armazenamento azure](https://azure.microsoft.com/services/?filter=storage)e [rede Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de trabalho da [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerenciar sua imagem de Contêiner:

- [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Recomendamos adicionar essas ferramentas ao seu ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Revise as ferramentas disponíveis na página Ferramentas para Desenvolvedores do [Azure.](https://azure.microsoft.com/) Se você estiver usando o Visual Studio, revise as ferramentas disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Crie a imagem do contêiner

Para obter mais informações, consulte os seguintes tutoriais:

- [Tutorial: Crie uma imagem de contêiner para implantação no Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Tutorial: Construa e implante imagens de contêiner na nuvem com tarefas de registro de contêiner do Azure.](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-step"></a>Próxima etapa

- [Crie sua oferta de contêiner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
