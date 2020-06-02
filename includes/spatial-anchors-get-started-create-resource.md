---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83638980"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso Âncoras Espaciais

Vá para o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

No painel de navegação esquerdo do portal do Azure, clique em **Criar um recurso**.

Use a caixa de pesquisa para **Âncoras Espaciais**.

   ![Pesquisar Âncoras Espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **Âncoras Espaciais**. Na caixa de diálogo, selecione **Criar**.

No caixa de diálogo **Conta de Âncoras Espaciais**:

- Insira um nome de recurso exclusivos, usando caracteres alfanuméricos regulares.
- Selecione a assinatura que você deseja anexar o recurso.
- Crie um grupo de recursos, selecionando **Criar novo**. Denomine-o como **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecione um local (região) para criar o recurso.
- Selecione **Novo** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Depois que o recurso é criado, o portal do Azure mostra que a implantação foi concluída. Clique em **Ir para o recurso**.

![Implantação concluída](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Em seguida, você pode exibir as propriedades do recurso. Copiar o valor de **ID da conta** em um editor de texto, pois você precisará dele mais tarde.

   ![Propriedades de recurso](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Copie também o valor do **Domínio da Conta** do recurso em um editor de texto, pois você precisará dele mais tarde.

   ![Domínio da conta](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Em **Configurações**, selecione **Chave**. Cópia do valor de **Chave primária** em um editor de texto. Esse valor é o `Account Key`. Você precisará dela mais tarde.

   ![Chave de conta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
