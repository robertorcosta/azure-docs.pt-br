---
title: Crie um cluster azure Data Explorer & DB com o Azure CLI
description: Saiba como criar um cluster e o banco de dados do Azure Data Explorer usando a CLI do Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 6b8c2924e50da095c3bc5c7db2d2bf48ef5a27c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561894"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Criar um cluster e um banco de dados do Azure Data Explorer usando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo ARM](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa de uma assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o Azure CLI localmente, este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Execute `az --version` para verificar sua versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configurar os parâmetros da CLI

As etapas a seguir não serão necessárias se você estiver executando comandos no Azure Cloud Shell. Se você estiver executando a CLI localmente, siga estas etapas para entrar no Azure e definir a assinatura atual:

1. Execute o comando a seguir para entrar no Azure:

    ```azurecli-interactive
    az login
    ```

1. Defina a assinatura em que deseja criar o cluster. Substitua `MyAzureSub` pelo nome da assinatura do Azure que deseja usar:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o seguinte comando:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | name | *azureclitest* | O nome desejado do cluster.|
   | sku | *D13_v2* | O SKU que será usado para o cluster. |
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há outros parâmetros opcionais que podem ser usados, como a capacidade do cluster.

1. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se o resultado contém `provisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o seguinte comando:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome do cluster | *azureclitest* | O nome do cluster em que o banco de dados será criado.|
   | name | *clidatabase* | O nome do banco de dados.|
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | soft-delete-period | *P365D* | Indica o tempo durante o qual os dados serão mantidos disponíveis para consulta. Consulte [política de retenção](/azure/kusto/concepts/retentionpolicy) para obter mais informações. |
   | hot-cache-period | *P31D* | Indica o tempo durante o qual os dados serão mantidos no cache. Consulte [política de cache](/azure/kusto/concepts/cachepolicy) para obter mais informações. |

1. Execute o seguinte comando para ver o banco de dados criado:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Próximas etapas

* [Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)
