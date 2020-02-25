---
title: Criar um cluster de Data Explorer do Azure & DB usando o PowerShell
description: Saiba como criar um cluster e banco de dados do Azure Data Explorer usando PowerShell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560568"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Criar um cluster e banco de dados do Azure Data Explorer usando PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo de ARM](create-cluster-database-resource-manager.md)  

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando o PowerShell. É possível executar cmdlets e scripts do PowerShell no Windows, no Linux ou no [Azure Cloud Shell](../cloud-shell/overview.md) com [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) para criar e configurar o banco de dados e clusters do Azure Data Explorer.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o CLI do Azure localmente, este artigo exigirá o CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para verificar sua versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Configurar parâmetros

As etapas a seguir não serão necessárias se você estiver executando comandos no Azure Cloud Shell. Se você estiver executando a CLI localmente, siga as etapas 1 e 2 para entrar no Azure e definir a assinatura atual:

1. Execute o comando a seguir para entrar no Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Defina a assinatura em que deseja criar o cluster:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Durante a execução da CLI do Azure localmente ou no Azure Cloud Shell, você precisa instalar o módulo Az.Kusto em seu dispositivo:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o seguinte comando:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | Nome | *mykustocluster* | O nome desejado do cluster.|
   | Sku | *D13_v2* | O SKU que será usado para o cluster. |
   | ResourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há outros parâmetros opcionais que podem ser usados, como a capacidade do cluster.

1. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Se o resultado contém `provisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o seguinte comando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | ClusterName | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | Nome | *mykustodatabase* | O nome do banco de dados.|
   | ResourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | SoftDeletePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos disponíveis para consulta. |
   | HotCachePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos no cache. |

1. Execute o seguinte comando para ver o banco de dados criado:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Próximas etapas

* [Comandos adicionais do Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Ingerir dados usando o SDK do .NET Standard no Azure Data Explorer (Versão prévia)](net-standard-ingest-data.md)
