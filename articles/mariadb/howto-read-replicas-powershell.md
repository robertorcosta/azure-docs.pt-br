---
title: Gerenciar réplicas de leitura-Azure PowerShell-banco de dados do Azure para MariaDB
description: Saiba como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 0280d69dc552b776457ff28d19968f6494a846ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707938"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o PowerShell

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o serviço MariaDB usando o PowerShell. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Você pode criar e gerenciar réplicas de leitura usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Embora o módulo Az.MariaDb do PowerShell esteja em versão prévia, você precisará instalá-lo separadamente por meio do módulo do Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Depois que o módulo Az.MariaDb do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e ficará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Assegure-se de que o servidor principal esteja em um desses níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Ao criar uma réplica para um mestre que não tem nenhuma réplica existente, primeiro o mestre será reiniciado para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O comando `New-AzMariaDbServerReplica` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos em que o servidor de réplica é criado.  |
| Nome | mydemoreplicaserver | O nome do novo servidor de réplica criado. |

Para criar uma réplica de leitura entre regiões, use o parâmetro **Location** . O exemplo a seguir cria uma réplica na região **oeste dos EUA** .

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md).

Por padrão, as réplicas de leitura são criadas com a mesma configuração de servidor que o mestre, a menos que o parâmetro **SKU** seja especificado.

> [!NOTE]
> Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou maiores que o mestre para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-master-server"></a>Listar réplicas para um servidor mestre

Para exibir todas as réplicas de um determinado servidor mestre, execute o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O comando `Get-AzMariaDReplica` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou a ID do servidor mestre. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Excluir um servidor mestre

> [!IMPORTANT]
> A exclusão de um servidor mestre interrompe a replicação para todos os servidores de réplica e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor mestre, você pode executar o `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reiniciar o banco de dados do Azure para o servidor MariaDB usando o PowerShell](howto-restart-server-powershell.md)
