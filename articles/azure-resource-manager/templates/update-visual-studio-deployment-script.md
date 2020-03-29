---
title: Atualize o script de implantação de modelos do Visual Studio para usar o Az PowerShell
description: Atualize o script de implantação do modelo visual studio do AzureRM para o Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963862"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Atualizar o script de implantação do modelo do Visual Studio para usar o módulo Az PowerShell

O Visual Studio 16.4 suporta o uso do módulo Az PowerShell no script de implantação do modelo. No entanto, o Visual Studio não instala automaticamente esse módulo. Para usar o módulo Az, você precisa dar quatro passos:

1. [Desinstalar o módulo AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instale o módulo AZ](/powershell/azure/install-az-ps)
1. Atualizar o Visual Studio para 16.4
1. Atualizar o script de implantação em seu projeto.

## <a name="update-visual-studio-to-164"></a>Atualizar o Visual Studio para 16.4

Atualize sua instalação do Visual Studio para a versão 16.4 ou posterior. Durante a atualização, certifique-se de que o componente Azure PowerShell não seja verificado. Uma vez que você instalou o módulo Az através da galeria, você não quer reinstalar o módulo AzureRM.

Se você já atualizou para 16.4 e o componente Azure PowerShell foi verificado, você pode desinstalá-lo executando o Visual Studio Installer. Não selecione o componente Azure PowerShell na carga de trabalho do Azure ou na página de componentes individuais.

## <a name="update-the-deployment-script-in-your-project"></a>Atualizar o script de implantação em seu projeto

Substitua todas as ocorrências da cadeia de caracteres “AzureRM” por “Az” em seu script de implantação. É possível consultar as revisões neste [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) para ver as alterações. Para obter mais informações sobre como atualizar scripts para o módulo Az, consulte [Migrate Azure PowerShell do AzureRM para Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o projeto Visual Studio, consulte [Criando e implantando projetos de grupo de recursos do Azure através do Visual Studio](create-visual-studio-deployment-project.md).
