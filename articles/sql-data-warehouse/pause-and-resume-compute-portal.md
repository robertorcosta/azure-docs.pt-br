---
title: 'Início Rápido: pausar & retomar computação – portal do Azure '
description: Use o portal do Microsoft Azure para pausar a computação no SQL Data Warehouse do Azure para economizar custos. Retomar computação quando você estiver pronto para usar o Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 14f66f71948f75a723c9fdbed7490d54c2c3e2b2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693005"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Início Rápido: Pausar e retomar computação para um SQL Data Warehouse do Azure no portal do Azure

Use o portal do Microsoft Azure para pausar a computação no SQL Data Warehouse do Azure para economizar custos. [Retomar computação](sql-data-warehouse-manage-compute-overview.md) quando você estiver pronto para usar o data warehouse.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Criar e conectar - portal](create-data-warehouse-portal.md) para criar um Data Warehouse chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausar computação

Para economizar custos, é possível pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. Você não será cobrado por recursos de computação enquanto o banco de dados estiver em pausa. No entanto, você continuará sendo cobrado pelo armazenamento. 

Siga estas etapas para pausar um SQL Data Warehouse.

1. Clique em **Bancos de Dados SQL** na página esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** da página **Bancos de Dados SQL**. Isso abre o Data Warehouse. 
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Online**.

    ![Computação on-line](media/pause-and-resume-compute-portal/compute-online.png)

4. Para pausar o Data Warehouse, clique no botão **Pausar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Aguarde alguns minutos e, em seguida, observe se **Status** indica **Pausando**.

    ![Pausando](media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa for concluída, o status indica **Pausado** e o botão de opção é **Iniciar**.
8. Os recursos de computação para o Data Warehouse agora estão offline. Você não será cobrado por computação até você reiniciar o serviço.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar a computação

Siga estas etapas para retomar um SQL Data Warehouse.

1. Clique em **Bancos de Dados SQL** na página esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** da página **Bancos de Dados SQL**. Isso abre o Data Warehouse. 
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Pausado**.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar o Data Warehouse, clique em **Iniciar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Observe se o **Status** indica **Continuando**.

    ![Continuando](media/pause-and-resume-compute-portal/resuming.png)

7. Quando o Data Warehouse está novamente online, o status indica **Online** e o botão de opção é **Pausar**.
8. Os recursos de computação para o Data Warehouse agora estão online e você pode usar o serviço. Encargos de computação foram retomados.

    ![Computação on-line](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você está sendo cobrado por unidades de Data Warehouse e pelos dados armazenados em seu Data Warehouse. Esses recursos de computação e armazenamento são cobrados separadamente. 

- Se você quiser manter os dados no armazenamento, pause a computação.
- Se desejar remover encargos futuros, será possível excluir o data warehouse. 

Siga estas etapas para limpar os recursos conforme desejado.

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique no seu Data Warehouse.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Para pausar a computação, clique no botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Iniciar**.  Para retomar a computação, clique **Iniciar**.

2. Para remover o data warehouse para você não ser cobrado pela computação ou pelo armazenamento, clique em **Excluir**.

3. Para remover o SQL Server criado, clique em **meunovoservidor-20171113.database.windows.net** e, em seguida, clique em **Excluir**.  Tenha cuidado com essa exclusão, uma vez que a exclusão do servidor também exclui todos os bancos de dados atribuídos ao servidor.

4. Para remover o grupo de recursos, clique em **meuGrupoDeRecursos** e, em seguida, clique em **Excluir grupo de recursos**.


## <a name="next-steps"></a>Próximas etapas

Você agora pausou e retomou a computação para o Data Warehouse. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
> [Carregar dados em um SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
