---
title: Atualização de versão principal-portal do Azure-banco de dados do Azure para MySQL-servidor único
description: Este artigo descreve como você pode atualizar a versão principal para o banco de dados do Azure para MySQL-servidor único usando o portal do Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686614"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Atualização de versão principal no banco de dados do Azure para MySQL servidor único usando o portal do Azure

> [!IMPORTANT]
> A atualização de versão principal para o banco de dados do Azure para MySQL servidor único está em visualização pública.

Este artigo descreve como você pode atualizar sua versão principal do MySQL in-loco no banco de dados do Azure para MySQL servidor único.

Esse recurso permitirá que os clientes realizem atualizações in-loco de seus servidores MySQL 5,6 para o MySQL 5,7 com um clique no botão sem nenhuma movimentação de dados ou a necessidade de qualquer alteração na cadeia de conexão do aplicativo.

> [!Note]
> * A atualização de versão principal só está disponível para atualização de versão principal do MySQL 5,6 para MySQL 5,7<br>
> * A atualização da versão principal ainda não tem suporte no servidor de réplica.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [servidor único do banco de dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Executar a atualização da versão principal do MySQL 5,6 para o MySQL 5,7

Siga estas etapas para executar a atualização da versão principal para o banco de dados do Azure do servidor MySQL 5,6

1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor de banco de dados do Azure para MySQL 5,6 existente.

2. Na página **visão geral** , clique no botão **Atualizar** na barra de ferramentas.

3. Na seção **atualização** , selecione **OK** para atualizar o servidor do banco de dados do Azure para MySQL 5,6 para o servidor 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Banco de dados do Azure para MySQL-visão geral-atualização":::

4. Uma notificação confirmará que a atualização foi bem-sucedida.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a política de controle de versão do banco de dados do Azure para MySQL](concepts-version-policy.md).