---
title: Aumento automático do armazenamento-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático usando o CLI do Azure no banco de dados do Azure para MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3c7fc5165766ff5b492f05047000814475235280
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516417"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Aumento automático do banco de dados do Azure para armazenamento MariaDB usando o CLI do Azure
Este artigo descreve como você pode configurar um banco de dados do Azure para o armazenamento do MariaDB Server para crescer sem afetar a carga de trabalho.

O servidor que está [alcançando o limite de armazenamento](concepts-pricing-tiers.md#reaching-the-storage-limit)está definido como somente leitura. Se o crescimento automático do armazenamento estiver habilitado, para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado será aumentado em 5 GB assim que o armazenamento livre estiver abaixo do maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos conforme especificado [aqui](concepts-pricing-tiers.md#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia:

- Você precisa de um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2,0 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Habilitar o crescimento automático do armazenamento do MariaDB Server

Habilite o armazenamento de crescimento automático do servidor em um servidor existente com o seguinte comando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Habilite o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-metric.md).
