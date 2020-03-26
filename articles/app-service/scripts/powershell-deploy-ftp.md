---
title: 'PowerShell: Fazer upload de arquivos usando o FTP'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como fazer upload de arquivos em um aplicativo usando o FTP.
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 1dfcbec9530a66570244e8168faa84bfe0759038
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685087"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>Carregar arquivos para um aplicativo Web usando FTP

Esse script de exemplo cria um aplicativo Web no Serviço de Aplicativo com recursos relacionados e, em seguida, implanta o código do aplicativo Web usando o FTP (por meio de [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)).

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo web. |
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | Obtenha perfis de publicação do aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
