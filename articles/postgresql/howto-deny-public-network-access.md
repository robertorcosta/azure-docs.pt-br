---
title: Negar acesso à rede pública-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como configurar o acesso de rede pública Deny usando portal do Azure para o banco de dados do Azure para PostgreSQL servidor único
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375116"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negar acesso à rede pública no banco de dados do Azure para PostgreSQL servidor único usando portal do Azure

Este artigo descreve como você pode configurar um servidor único do banco de dados do Azure para PostgreSQL para negar todas as configurações públicas e permitir somente conexões por meio de pontos de extremidade privados para aprimorar ainda mais a segurança da rede.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Para concluir este guia de instruções, você precisa:

* Um [servidor único do banco de dados do Azure para PostgreSQL](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Definir acesso de rede pública de negação

Siga estas etapas para definir o acesso de rede pública de negação de servidor único do PostgreSQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure existente para PostgreSQL servidor único.

1. Na página servidor único PostgreSQL, em **configurações**, clique em **segurança de conexão** para abrir a página configuração de segurança de conexão.

1. Em **negar acesso à rede pública**, selecione **Sim** para habilitar o acesso público negado para o servidor único PostgreSQL.

    ![Banco de dados do Azure para PostgreSQL servidor único negar acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com êxito.

    ![Banco de dados do Azure para PostgreSQL servidor único negar êxito de acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).