---
title: Negar acesso à rede pública - portal Azure - Banco de Dados Azure para MariaDB
description: Saiba como configurar o Deny Public Network Access usando o portal Azure para o seu Banco de Dados Azure para MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375233"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Negar acesso à rede pública no banco de dados do Azure para O MariaDB usando o portal Azure

Este artigo descreve como você pode configurar um banco de dados Azure para o servidor MariaDB negar todas as configurações públicas e permitir que apenas conexões através de pontos finais privados para melhorar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

* Um [banco de dados Azure para MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Definir negar acesso à rede pública

Siga estas etapas para definir o servidor DoMADB Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione o banco de dados Azure existente para o servidor MariaDB.

1. Na página do servidor MariaDB, em **Configurações,** clique **em Segurança de conexão** para abrir a página de configuração de segurança de conexão.

1. Em Negar acesso à rede pública, selecione **Sim** para permitir negar acesso público ao seu servidor MariaDB.

    ![Banco de dados Azure para MariaDB Negar acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com sucesso.

    ![Banco de dados Azure para MariaDB Negar sucesso de acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Próximas etapas

Saiba [como criar alertas em métricas.](howto-alert-metric.md)