---
title: Negar acesso à rede pública - portal Azure - Banco de Dados Azure para MySQL
description: Saiba como configurar o Deny Public Network Access usando o portal Azure para o seu banco de dados Azure para MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374947"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Negar acesso à rede pública no banco de dados Do Azure para MySQL usando o portal Azure

Este artigo descreve como você pode configurar um banco de dados Azure para servidor MySQL para negar todas as configurações públicas e permitir que apenas conexões através de pontos finais privados para melhorar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

* Um [banco de dados Azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Definir negar acesso à rede pública

Siga estas etapas para definir o servidor MySQL Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione o banco de dados Azure existente para o servidor MySQL.

1. Na página do servidor MySQL, em **Configurações,** clique **em Segurança de conexão** para abrir a página de configuração de segurança de conexão.

1. Em **Negar acesso à rede pública,** selecione **Sim** para permitir negar acesso público ao seu servidor MySQL.

    ![Banco de dados Azure para acesso à rede MySQL Deny](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Clique em **Salvar** para salvar as alterações.

1. Uma notificação confirmará que a configuração de segurança de conexão foi habilitada com sucesso.

    ![Banco de dados Azure para o sucesso de acesso à rede MySQL Deny](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Próximas etapas

Saiba [como criar alertas em métricas.](howto-alert-on-metric.md)