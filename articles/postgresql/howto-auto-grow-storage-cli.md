---
title: Armazenamento de crescimento automático - Azure CLI - Banco de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como você pode configurar o armazenamento de crescimento automático usando o Azure CLI no Azure Database for PostgreSQL - Single Server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767935"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Banco de dados Azure de crescimento automático para armazenamento PostgreSQL - Servidor Único usando o Cli Azure
Este artigo descreve como você pode configurar um banco de dados Azure para que o armazenamento do servidor PostgreSQL cresça sem afetar a carga de trabalho.

O servidor [que atinge o limite de armazenamento](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)está definido como somente leitura. Se o crescimento automático de armazenamento for habilitado para servidores com menos de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado será aumentado em 5 GB assim que o armazenamento gratuito estiver abaixo do maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento livre está abaixo de 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento conforme especificado [aqui](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Habilite o crescimento automático do armazenamento do servidor PostgreSQL

Habilite o armazenamento de crescimento automático do servidor em um servidor existente com o seguinte comando:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Habilite o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Próximas etapas

Saiba [como criar alertas em métricas.](howto-alert-on-metric.md)