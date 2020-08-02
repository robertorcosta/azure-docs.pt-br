---
title: Reiniciar o servidor-Azure PowerShell-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode reiniciar um servidor de banco de dados do Azure para MySQL usando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71d10078a704b2905cf055347f5ed4272ca8ef72
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502776"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Reiniciar o banco de dados do Azure para servidor MySQL usando o PowerShell

Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para MySQL. Talvez seja necessário reiniciar o servidor por motivos de manutenção, o que causa uma pequena interrupção durante a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.

A quantidade de tempo necessária para concluir uma reinicialização depende do processo de recuperação do MySQL. Para reduzir o tempo de reinicialização, recomendamos que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell está em versão prévia, você deve instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Depois que o módulo Az.MySql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Reiniciar o servidor

Reinicie o servidor com o seguinte comando:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um servidor de banco de dados do Azure para MySQL usando o PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)
