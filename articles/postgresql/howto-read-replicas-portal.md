---
title: Gerenciar réplicas de leitura-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como gerenciar réplicas de leitura banco de dados do Azure para PostgreSQL-servidor único do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768957"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL-servidor único do portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no Banco de Dados do Azure para PostgreSQL no portal do Azure. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).


## <a name="prerequisites"></a>Pré-requisitos
Um [servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) que será o servidor mestre.

## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de uso geral ou otimizada para memória. O servidor mestre está preparado para replicação definindo o parâmetro Azure. replication_support. Quando o parâmetro de replicação é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. No portal do Azure, essas duas etapas são encapsuladas por um único botão, **habilitar o suporte de replicação**.

1. No portal do Azure, selecione o servidor existente do Banco de Dados do Azure para PostgreSQL a ser usado como mestre.

2. Na barra lateral do servidor, em **configurações**, selecione **replicação**.

> [!NOTE] 
> Se você vir **desabilitar suporte de replicação** esmaecido, as configurações de replicação já estão definidas no servidor por padrão. Você pode ignorar as etapas a seguir e ir para criar uma réplica de leitura. 

3. Selecione **habilitar suporte à replicação**. 

   ![Habilitar suporte à replicação](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Confirme que você deseja habilitar o suporte à replicação. Esta operação reiniciará o servidor mestre. 

   ![Confirmar habilitar suporte à replicação](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Você receberá duas notificações de portal do Azure quando a operação for concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para a reinicialização do servidor que segue imediatamente.

   ![Notificações de êxito-habilitar](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Atualize a página de portal do Azure para atualizar a barra de ferramentas de replicação. Agora você pode criar réplicas de leitura para este servidor.

   ![Barra de ferramentas atualizada](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Habilitar o suporte de replicação é uma operação única por servidor mestre. Um botão para **desabilitar o suporte à replicação** é fornecido para sua conveniência. Não recomendamos desabilitar o suporte à replicação, a menos que você tenha certeza de que nunca criará uma réplica nesse servidor mestre. Não é possível desabilitar o suporte à replicação enquanto o servidor mestre tiver réplicas existentes.


## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estas etapas:

1. Selecione um servidor do Banco de Dados do Azure para PostgreSQL existente a ser usado como servidor mestre. 

2. Na barra lateral do servidor, em **configurações**, selecione **replicação**.

3. Selecione **para adicionar réplica**.

   ![Adicionar uma réplica](./media/howto-read-replicas-portal/add-replica.png)

4. Insira um nome para a réplica de leitura. 

    ![Nome da réplica](./media/howto-read-replicas-portal/name-replica.png)

5. Selecione um local para a réplica. O local padrão é o mesmo que o do servidor mestre.

    ![Selecione um local](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

6. Selecione **OK** para confirmar a criação da réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

Depois que a réplica de leitura é criada, ela pode ser exibida na janela **Replicação**:

![Exibir a nova réplica na janela Replicação](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura.

> [!IMPORTANT]
> Depois de interromper a replicação para um servidor mestre e uma réplica de leitura, isso não poderá ser desfeito. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

Para interromper a replicação entre um servidor mestre e uma réplica de leitura do portal do Azure, siga estas etapas:

1. No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2. No menu de servidor, em **CONFIGURAÇÕES**, selecione **Replicação**.

3. Selecione o servidor de réplica para o qual interromper a replicação.

   ![Selecionar a réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **Parar replicação**.

   ![Selecionar interromper a replicação](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecione **OK** para interromper a replicação.

   ![Confirme para interromper a replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Excluir um servidor mestre
Para excluir um servidor mestre, você deve usar as mesmas etapas para excluir um servidor do Banco de Dados do Azure para PostgreSQL. 

> [!IMPORTANT]
> Ao excluir um servidor mestre, a replicação para todas as réplicas de leitura será interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

Para excluir um servidor do portal do Azure, siga estas etapas:

1. No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2. Abra a página **Visão geral** para o servidor. Selecione **Excluir**.

   ![Na página Visão geral do servidor, selecione para excluir o servidor mestre](./media/howto-read-replicas-portal/delete-server.png)
 
3. Insira o nome do servidor mestre para excluir. Selecione **Excluir** para confirmar a exclusão do servidor mestre.

   ![Confirme para excluir o servidor mestre](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Excluir uma réplica
Você pode excluir uma réplica de leitura semelhante a como exclui um servidor mestre.

- No portal do Azure, abra a página **Visão geral** para a réplica de leitura. Selecione **Excluir**.

   ![Na página de Visão geral de réplica, selecione para excluir a réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Você também pode excluir a réplica de leitura usando a janela **Replicação** seguindo estas etapas:

1. No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2. No menu de servidor, em **CONFIGURAÇÕES**, selecione **Replicação**.

3. Selecione a réplica de leitura a excluir.

   ![Selecione a réplica a excluir](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecione **Excluir réplica**.

   ![Selecionar excluir réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Insira o nome da réplica a excluir. Selecione **Excluir** para confirmar a exclusão da réplica.

   ![Confirme a exclusão da réplica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorar uma réplica
Duas métricas estão disponíveis para monitorar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Métrica de Retardo Máximo Entre Réplicas
A métrica **Retardo Máximo Entre Réplicas** mostra o retardo em bytes entre o servidor mestre e a réplica com o maior retardo. 

1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  Selecione **Métricas**. Na janela **Métricas**, selecione **Retardo Máximo entre Réplicas**.

    ![Monitorar o retardo máximo entre réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Para sua **Agregação**, selecione **Máx**.


### <a name="replica-lag-metric"></a>Métrica de retardo de réplica
A métrica **Retardo da Réplica** mostra o tempo decorrido desde a última transação reproduzida em uma réplica. Se não houver nenhuma transação ocorrendo no mestre, a métrica refletirá esse retardo.

1. No portal do Azure, selecione a réplica de leitura do Banco de Dados do Azure para PostgreSQL.

2. Selecione **Métricas**. Na janela **Métricas**, selecione **Retardo de Réplica**.

   ![Monitorar o retardo da réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Para sua **Agregação**, selecione **Máx**. 
 
## <a name="next-steps"></a>Próximos passos
* Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure e na API REST](howto-read-replicas-cli.md).