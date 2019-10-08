---
title: Atualizar o script de implantação de modelo do Visual Studio para usar Az PowerShell
description: Atualizar o script de implantação de modelo do Visual Studio do AzureRM para o Az PowerShell
author: cweining
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: 483b20f0003994f88ec35cc9f31a77cf571804f3
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695680"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Atualizar o script de implantação de modelo do Visual Studio para usar o Az PowerShell

O Visual Studio 16.4 dá suporte ao uso do Az PowerShell no script de implantação de modelo. O Visual Studio não instala o Az PowerShell nem atualiza automaticamente o script de implantação em seu projeto de grupo de recursos. Para usar o Az PowerShell mais recente, é necessário fazer estas quatro coisas:
1. Desinstalar o AzureRM PowerShell
1. Instalar o Az PowerShell
1. Atualizar para o Virtual Studio 16.4
1. Atualizar o script de implantação em seu projeto.

## <a name="uninstall-azurerm-powershell"></a>Desinstalar o AzureRM PowerShell
Siga estas [instruções](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) para desinstalar o AzureRM PowerShell.

## <a name="install-az-powershell"></a>Instalar o Az PowerShell
Siga estas [instruções](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) para instalar o Az PowerShell.

## <a name="update-visual-studio-to-164"></a>Atualizar o Visual Studio para 16.4
Atualize para o Visual Studio 16.4 quando estiver disponível. Durante a atualização, verifique se o componente do Azure PowerShell não está marcado. Como você instalou o Az PowerShell por meio da galeria, você não quer a versão do AzureRM do PowerShell instalada com o Visual Studio.

Se você já tiver atualizado para a versão 16.4 e o componente do Azure PowerShell tiver sido marcado, será possível desinstalá-la executando o Instalador do Visual Studio e desmarcando o componente do Azure PowerShell na Carga de Trabalho do Azure ou na página de componentes individuais.

## <a name="update-the-deployment-script-in-your-project"></a>Atualizar o script de implantação em seu projeto
Substitua todas as ocorrências da cadeia de caracteres “AzureRM” por “Az” em seu script de implantação. É possível consultar as revisões neste [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) para ver as alterações. Veja esta [documentação](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) para obter mais informações sobre como atualizar scripts para o Az PowerShell.


