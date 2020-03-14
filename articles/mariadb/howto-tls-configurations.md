---
title: Configuração de TLS-portal do Azure-banco de dados do Azure para MariaDB
description: Saiba como definir a configuração de TLS usando portal do Azure para o banco de dados do Azure para MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 063fc82131a66012c7329f590c0fdf2ce4d4da79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79374999"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Definindo configurações de TLS no banco de dados do Azure para MariaDB usando portal do Azure

Este artigo descreve como você pode configurar um banco de dados do Azure para o servidor MariaDB para impor conexões para uma versão mínima do TLS para percorrer e negar todas as conexões com a versão mais baixa do TLS, melhorando assim a segurança da rede.

Agora, os clientes têm a capacidade de impor a versão do TLS para se conectar ao banco de dados do Azure para MariaDB definindo a versão mínima do TLS para seu servidor de banco de dados. Por exemplo, definir a versão mínima de configuração de TLS como TLS 1,0 significa que o servidor permitirá conexões de clientes usando TLS 1,0, 1,1 e 1.2 +. Como alternativa, definir isso como 1,2 significa que você só permite conexões de clientes que usam TLS 1,2 e todas as conexões com TLS 1,0 e TLS 1,1 serão rejeitadas.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Para concluir este guia de instruções, você precisa:

* Um [banco de dados do Azure para MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Definir configurações de TLS para o banco de dados do Azure para MariaDB

Siga estas etapas para definir a versão mínima do TLS do servidor MariaDB:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure existente para o servidor MariaDB.

1. Na página do servidor do MariaDB, em **configurações**, clique em **segurança de conexão** para abrir a página configuração de segurança da conexão.

1. Em **versão mínima do TLS**, selecione **1,2** para negar conexões com a versão tls inferior ao TLS 1,2 para seu servidor MariaDB.

    ![Banco de dados do Azure para configuração do MariaDB TLS](./media/howto-tls-configurations/tls-configurations.png)

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com êxito.

    ![Êxito na configuração do banco de dados do Azure para MariaDB TLS](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-metric.md).
