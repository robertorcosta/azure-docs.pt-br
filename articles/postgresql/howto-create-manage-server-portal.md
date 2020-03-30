---
title: Gerenciar banco de dados Azure para PostgreSQL - Portal Azure
description: Saiba como gerenciar um banco de dados Azure para servidor PostgreSQL a partir do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535685"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gerenciar um banco de dados Azure para servidor PostgreSQL usando o portal Azure
Este artigo mostra como gerenciar seu banco de dados Azure para servidores PostgreSQL. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e visualização de detalhes do servidor.

## <a name="sign-in"></a>Entrar
Faça login no [portal Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor
Visite o [quickstart](quickstart-create-server-database-portal.md) para aprender como criar e começar com um banco de dados Azure para servidor PostgreSQL.

## <a name="scale-compute-and-storage"></a>Dimensionar computação e armazenamento

Após a criação do servidor, você pode dimensionar entre os níveis De propósito geral e otimizado de memória à medida que suas necessidades mudam. Você também pode dimensionar a computação e a memória aumentando ou diminuindo os vCores. O armazenamento pode ser ampliado (no entanto, você não pode reduzir o armazenamento).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Dimensione entre os níveis de propósito geral e otimizado de memória

Você pode escalar de Propósito Geral para Memória Otimizada e vice-versa. A alteração de e para o nível Básico após a criação do servidor não é suportada. 

1. Selecione seu servidor no portal Azure. Selecione **o nível de preços,** localizado na seção **Configurações.**

2. Selecione **Propósito Geral** ou **Memória Otimizada,** dependendo do que você está dimensionando. 

    ![nível de preços de mudança](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > A alteração de níveis faz com que o servidor seja reiniciado.

4. Selecione **OK** para salvar alterações.


### <a name="scale-vcores-up-or-down"></a>Escala vCores para cima ou para baixo

1. Selecione seu servidor no portal Azure. Selecione **o nível de preços,** localizado na seção **Configurações.**

2. Altere a configuração **vCore**, movendo o controle deslizante para o valor desejado.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > O dimensionamento de vCores faz com que o servidor seja reiniciado.

3. Selecione **OK** para salvar alterações.


### <a name="scale-storage-up"></a>Dimensionar o armazenamento

1. Selecione seu servidor no portal Azure. Selecione **o nível de preços,** localizado na seção **Configurações.**

2. Altere a configuração **de armazenamento** movendo o controle deslizante para o valor desejado.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > O armazenamento não pode ser reduzido.

3. Selecione **OK** para salvar alterações.


## <a name="update-admin-password"></a>Atualizar a senha do admin
Você pode alterar a senha da função administrador a usar o portal Azure.

1. Selecione seu servidor no portal Azure. Na **janela Visão geral,** selecione **Redefinir senha**.

   ![visão geral](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Insira uma nova senha e confirme-a. A caixa de texto irá indissioná-lo sobre os requisitos de complexidade de senha.

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecione **OK** para salvar a nova senha.


## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir seu servidor se não precisar mais dele. 

1. Selecione seu servidor no portal Azure. Na **janela Visão geral,** **selecione Excluir**.

    ![excluir](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmar se este é o servidor que você deseja excluir.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Excluir um servidor é irreversível.

3. Selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [backups e restauração de servidores](howto-restore-server-portal.md)
- Saiba mais sobre [as opções de ajuste e monitoramento no Banco de Dados Azure para PostgreSQL](concepts-monitoring.md)
