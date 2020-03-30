---
title: Negar acesso à rede pública - portal Azure - Banco de Dados Azure para PostgreSQL - Servidor único
description: Saiba como configurar o Deny Public Network Access usando o portal Azure para o seu banco de dados Azure para o servidor PostgreSQL Single
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375116"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negar acesso à rede pública no banco de dados do Azure para servidor single postgreSQL usando o portal Azure

Este artigo descreve como você pode configurar um banco de dados Azure para servidor PostgreSQL Único para negar todas as configurações públicas e permitir que apenas conexões através de pontos finais privados para melhorar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

* Um [banco de dados Azure para servidor único PostgreSQL](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Definir negar acesso à rede pública

Siga estas etapas para definir postgreSQL Único servidor negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione o banco de dados Azure existente para servidor PostgreSQL Single.

1. Na página do servidor PostgreSQL Single, em **Configurações,** clique em **Segurança de conexão** para abrir a página de configuração de segurança de conexão.

1. Em **Negar acesso à rede pública,** selecione **Sim** para permitir negar acesso público ao seu servidor PostgreSQL Single.

    ![Banco de dados Azure para postgreSQL Único servidor negar acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com sucesso.

    ![Banco de dados Azure para postgreSQL Único servidor Negar sucesso de acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Próximas etapas

Saiba [como criar alertas em métricas.](howto-alert-on-metric.md)