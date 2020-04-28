---
title: Reiniciar o servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode reiniciar um banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74770077"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Reiniciar o banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para PostgreSQL. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.
 
O tempo necessário para concluir uma reinicialização depende do processo de recuperação do PostgreSQL. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Realizar a reinicialização do servidor

As etapas a seguir reiniciam o servidor PostgreSQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure para o servidor PostgreSQL.

2. Na barra de ferramentas da página de **Visão Geral** do servidor, clique em **Reiniciar**.

   ![Banco de Dados do Azure para PostgreSQL - Visão geral - botão Reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   ![Banco de Dados do Azure para PostgreSQL - Restaurar confirmação](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o status do servidor muda para "Reiniciando".

   ![Banco de Dados do Azure para PostgreSQL - Reiniciar status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme se a reinicialização do servidor foi bem-sucedida.

   ![Banco de Dados do Azure para PostgreSQL - Reiniciar com êxito](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como definir parâmetros no banco de dados do Azure para PostgreSQL](howto-configure-server-parameters-using-portal.md)