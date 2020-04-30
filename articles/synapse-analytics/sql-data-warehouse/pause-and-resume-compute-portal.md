---
title: Pausar e retomar a computação no pool de SQL do Synapse por meio do portal do Azure
description: Use o portal do Azure para pausar a computação para o pool de SQL para economizar custos. Retomar computação quando você estiver pronto para usar o Data Warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80350970"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Início Rápido: Pausar e retomar a computação no pool de SQL do Synapse por meio do portal do Azure

Você pode usar o portal do Azure para pausar e retomar os recursos de computação do pool de SQL do Synapse (data warehouse). Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um pool de SQL chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausar computação

Para reduzir custos, você pode pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. 
>[!NOTE]
>Você não será cobrado por recursos de computação enquanto o banco de dados estiver em pausa. No entanto, você continuará sendo cobrado pelo armazenamento. 

Siga estas etapas para pausar um pool de SQL:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Clique em **Azure Synapse Analytics (antigo SQL DW)** na página de navegação à esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** na página **Azure Synapse Analytics (antigo SQL DW)** para abrir o pool de SQL. 
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Online**.

    ![Computação on-line](././media/pause-and-resume-compute-portal/compute-online.png)

4. Para pausar o pool de SQL, clique no botão **Pausar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Aguarde alguns minutos e, em seguida, observe se **Status** indica **Pausando**.

    ![Pausando](./media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa for concluída, o status indica **Em Pausa** e o botão de opção é **Retomar**.
8. Os recursos de computação do pool de SQL agora estão offline. Você não será cobrado por computação até você reiniciar o serviço.

    ![Computação offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar a computação

Siga estas etapas para retomar o pool de SQL.

1. Clique em **Azure Synapse Analytics (antigo SQL DW)** na página à esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** na página **Azure Synapse Analytics (antigo SQL DW)** para abrir a página do pool de SQL. 
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Pausado**.

    ![Computação offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar o pool de SQL, clique em **Retomar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Observe se o **Status** indica **Continuando**.

    ![Continuando](./media/pause-and-resume-compute-portal/resuming.png)

7. Quando o pool de SQL estiver novamente online, o status indicará **Online** e o botão de opção será **Pausar**.
8. Os recursos de computação para o pool de SQL estão online e você pode usar o serviço. Encargos de computação foram retomados.

    ![Computação on-line](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu pool de SQL. Esses recursos de computação e armazenamento são cobrados separadamente. 

- Se você quiser manter os dados no armazenamento, pause a computação.
- Se você quiser remover encargos futuros, poderá excluir o pool de SQL. 

Siga estas etapas para limpar os recursos conforme desejado.

1. Entre no [portal do Azure](https://portal.azure.com) e clique no seu pool de SQL.

    ![Limpar os recursos](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Para pausar a computação, clique no botão **Pausar**. 

2. Para remover o pool de SQL para que não seja cobrado pela computação ou pelo armazenamento, clique em **Excluir**.

3. Para remover o SQL Server criado, clique em **sqlpoolservername.database.windows.net** e, em seguida, clique em **Excluir**.  

   > [!CAUTION]
   > Tenha cuidado com essa exclusão, uma vez que a exclusão do servidor também exclui todos os bancos de dados atribuídos ao servidor.

5. Para remover o grupo de recursos, clique em **meuGrupoDeRecursos** e, em seguida, clique em **Excluir grupo de recursos**.


## <a name="next-steps"></a>Próximas etapas

Você agora colocou em pausa e retomou a computação para o pool de SQL. Passe para o próximo artigo para saber mais sobre como [Carregar dados para o pool de SQL](load-data-from-azure-blob-storage-using-polybase.md). Para obter informações adicionais sobre como gerenciar funcionalidades de computação, confira o artigo [Gerenciar visão geral de computação](sql-data-warehouse-manage-compute-overview.md). 

