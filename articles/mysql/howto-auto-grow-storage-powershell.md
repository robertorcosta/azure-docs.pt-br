---
title: Aumento automático do armazenamento-Azure PowerShell-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático usando o PowerShell no banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 09431a6690be751324bb796415d308d3b7727dad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254090"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-storage-using-powershell"></a>Crescimento automático do armazenamento no banco de dados do Azure para o armazenamento MySQL usando o PowerShell

Este artigo descreve como você pode configurar um armazenamento de banco de dados do Azure para servidor MySQL para crescer sem afetar a carga de trabalho.

O crescimento automático de armazenamento impede que o servidor [atinja o limite de armazenamento](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) e se torne somente leitura. Para servidores com 100 GB ou menos de armazenamento provisionado, o tamanho aumenta em 5 GB quando o espaço livre está abaixo de 10%. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho aumenta em 5% quando o espaço livre está abaixo de 10 GB. Os limites de armazenamento máximos se aplicam conforme especificado na seção armazenamento do [tipo de preço banco de dados do Azure para MySQL](/azure/mysql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo do PowerShell AZ. MySql está em versão prévia, você deve instalá-lo separadamente do módulo AZ PowerShell usando o seguinte `Install-Module -Name Az.MySql -AllowPrerelease`comando:.
> Depois que o módulo AZ. MySql PowerShell estiver disponível, ele se tornará parte das versões futuras do módulo do PowerShell AZ e estará disponível nativamente em Azure Cloud Shell.

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

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).