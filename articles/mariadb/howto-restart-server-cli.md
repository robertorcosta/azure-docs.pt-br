---
title: Reinicie o servidor-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode reiniciar um banco de dados do Azure para MariaDB Server usando o CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ccbd0790692f065cc313b5f95d5944ad3ff716b
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515397"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Reiniciar o banco de dados do Azure para MariaDB Server usando o CLI do Azure
Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para MariaDB. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.

O tempo necessário para concluir uma reinicialização depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções:

- Você precisa de um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2,0 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.


## <a name="restart-the-server"></a>Reiniciar o servidor

Reinicie o servidor com o seguinte comando:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como definir parâmetros no banco de dados do Azure para MariaDB](howto-configure-server-parameters-cli.md)
