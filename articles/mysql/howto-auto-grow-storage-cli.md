---
title: Armazenamento de crescimento automático - Azure CLI - Banco de dados Azure para MySQL
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático usando o Azure CLI no Azure Database for MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062625"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Banco de dados Azure de crescimento automático para armazenamento MySQL usando o Azure CLI
Este artigo descreve como você pode configurar um banco de dados Azure para que o armazenamento do servidor MySQL cresça sem afetar a carga de trabalho.

O servidor [que atinge o limite de armazenamento](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)está definido como somente leitura. Se o crescimento automático de armazenamento for habilitado para servidores com menos de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado será aumentado em 5 GB assim que o armazenamento gratuito estiver abaixo do maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento livre está abaixo de 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento conforme especificado [aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [banco de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Habilite o crescimento automático do armazenamento do servidor MySQL

Habilite o armazenamento de crescimento automático do servidor em um servidor existente com o seguinte comando:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Habilite o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Próximas etapas

Saiba [como criar alertas em métricas.](howto-alert-on-metric.md)