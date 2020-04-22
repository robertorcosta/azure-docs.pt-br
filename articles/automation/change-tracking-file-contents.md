---
title: Exibir alterações de conteúdo do arquivo com a Automação do Azure
description: Use o recurso de alteração de conteúdo do arquivo do Alterar rastreamento para exibir o conteúdo de um arquivo que foi alterado.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682990"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Exibir o conteúdo de um arquivo que está sendo controlado com o Controle de Alterações

O controle de conteúdo do arquivo permite que você exiba o conteúdo de um arquivo antes e após uma alteração que está sendo controlada com o Controle de Alterações. Para fazer isso, ele salva o conteúdo do arquivo em uma conta de armazenamento após cada alteração.

## <a name="requirements"></a>Requisitos

* Uma conta de armazenamento Standard usando o modelo de implantação do Resource Manager é necessária para armazenar o conteúdo do arquivo. As contas de armazenamento do modelo de implantação Premium e Clássico não devem ser usadas. Para obter mais informações sobre contas de armazenamento, consulte [Contas de armazenamento sobre o Azure](../storage/common/storage-create-storage-account.md)

* A conta de armazenamento usada só pode ter 1 conta de automação conectada.

* [O rastreamento de](automation-change-tracking.md) alterações está ativado em sua conta de Automação.

## <a name="enable-file-content-tracking"></a>Habilitar o controle de conteúdo do arquivo

1. No portal do Azure, abra sua Conta de Automação e, em seguida, selecione **Controle de Alterações**.
2. No menu superior, selecione **Editar Configurações**.
3. Selecione **Conteúdo do Arquivo** e clique em **Link**. Isso abre o painel **Adicionar local de conteúdo para o Controle de Alterações**.

   ![enable](./media/change-tracking-file-contents/enable.png)

4. Selecione a assinatura e a conta de armazenamento a serem usadas para armazenar o conteúdo do arquivo. Se você deseja habilitar o controle de conteúdo de arquivo para todos os arquivos controlados existentes, selecione **Habilitado** para **Carregar conteúdo do arquivo para todas as configurações**. Você poderá alterar isso para cada caminho de arquivo mais tarde.

   ![definir a conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

5. Após a habilitação, a conta de armazenamento e os URIs de SAS serão mostrados. Os URIs de SAS expiram depois de 365 dias e podem ser recriados clicando no botão **Regenerar**.

   ![listar chaves de conta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Adicionar um arquivo

As etapas a seguir mostram como habilitar o Controle de Alterações para um arquivo:

1. Na página Editar configurações de rastreamento de **alterações,** selecione os **arquivos do Windows** ou a guia Arquivos **Linux** e clique em **Adicionar**

1. Preencha as informações de caminho do arquivo e selecione Verdadeiro em **Carregar conteúdo do arquivo para todas as configurações**. Essa configuração habilita o controle de conteúdo do arquivo somente para esse caminho de arquivo.

   ![Adicionar um arquivo do linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Exibindo o conteúdo de um arquivo controlado

1. Quando uma alteração é detectada para o arquivo ou para um arquivo no caminho, ela é mostrada no portal. Selecione a alteração de arquivo na lista de alterações. O painel Detalhes da alteração é exibido.

   ![listar alterações](./media/change-tracking-file-contents/change-list.png)

1. No painel De detalhes alterar, você verá o padrão antes e depois das informações do arquivo. Selecione **Exibir alterações de conteúdo do arquivo** para ver o conteúdo do arquivo.

   ![detalhes da alteração](./media/change-tracking-file-contents/change-details.png)

1. A nova página mostra o conteúdo do arquivo em uma exibição lado a lado. Você também pode selecionar **Embutido** para ver uma exibição embutida das alterações.

   ![exibir alterações de arquivo](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Próximas etapas

Visite o tutorial sobre Controle de Alterações para saber mais sobre o uso da solução:

> [!div class="nextstepaction"]
> [Solucionar problemas de alterações em seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Use [pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para visualizar dados de rastreamento de alterações detalhados.

