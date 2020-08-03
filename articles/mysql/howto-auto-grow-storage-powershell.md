---
title: Aumento automático do armazenamento-Azure PowerShell-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático usando o PowerShell no banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d355e7557ff38c52872a89941025d33d01bd92d8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503303"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Crescimento automático do armazenamento no banco de dados do Azure para servidor MySQL usando o PowerShell

Este artigo descreve como você pode configurar um armazenamento de banco de dados do Azure para servidor MySQL para crescer sem afetar a carga de trabalho.

O crescimento automático de armazenamento impede que o servidor [atinja o limite de armazenamento](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) e se torne somente leitura. Para servidores com 100 GB ou menos de armazenamento provisionado, o tamanho aumenta em 5 GB quando o espaço livre está abaixo de 10%. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho aumenta em 5% quando o espaço livre está abaixo de 10 GB. Os limites de armazenamento máximos se aplicam conforme especificado na seção armazenamento do [tipo de preço banco de dados do Azure para MySQL](/azure/mysql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell está em versão prévia, você deve instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Depois que o módulo Az.MySql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

## <a name="enable-mysql-server-storage-auto-grow"></a>Habilitar o crescimento automático do armazenamento do servidor MySQL

Habilite o armazenamento de crescimento automático do servidor em um servidor existente com o seguinte comando:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Habilite o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o PowerShell](howto-read-replicas-powershell.md).
