---
title: Aumento automático do armazenamento-Azure PowerShell-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático usando o PowerShell no banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.openlocfilehash: 8519d7e66ae57b00a96adc60cf09c953c6ee7114
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120017"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Crescimento automático do armazenamento no banco de dados do Azure para MariaDB Server usando o PowerShell

Este artigo descreve como você pode configurar um banco de dados do Azure para o armazenamento do MariaDB Server para crescer sem afetar a carga de trabalho.

O crescimento automático de armazenamento impede que o servidor [atinja o limite de armazenamento](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit) e se torne somente leitura. Para servidores com 100 GB ou menos de armazenamento provisionado, o tamanho aumenta em 5 GB quando o espaço livre está abaixo de 10%. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho aumenta em 5% quando o espaço livre está abaixo de 10 GB. Os limites de armazenamento máximos se aplicam conforme especificado na seção armazenamento do [banco de dados do Azure para tipos de preço MariaDB](/azure/mariadb/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Embora o módulo Az.MariaDb do PowerShell esteja em versão prévia, você precisará instalá-lo separadamente por meio do módulo do Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Depois que o módulo Az.MariaDb do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e ficará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-mariadb-server-storage-auto-grow"></a>Habilitar o crescimento automático do armazenamento do servidor MariaDB

Habilite o armazenamento de crescimento automático do servidor em um servidor existente com o seguinte comando:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Habilite o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o PowerShell](howto-read-replicas-powershell.md).
