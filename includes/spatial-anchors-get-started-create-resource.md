---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b83951cd743afc7e249e3ee37182007bbe40cb5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971245"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso Âncoras Espaciais

Vá para o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

No painel esquerdo, clique em **Criar um recurso**.

Use a caixa de pesquisa para **Âncoras Espaciais**.

![Uma captura de tela mostrando os resultados de uma pesquisa de Âncoras Espaciais.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **Âncoras Espaciais** e clique em **Criar**.

No painel **Conta de Âncoras Espaciais**, faça o seguinte:

* Insira um nome de recurso exclusivo usando caracteres alfanuméricos normais.  
* Selecione a assinatura que você deseja anexar o recurso.  
* Crie um grupo de recursos, selecionando **Criar novo**. Nomeie-o **myResourceGroup** e clique em **OK**.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* Selecione um local (região) para criar o recurso.  
* Selecione **Novo** para começar a criar o recurso.

![Uma captura de tela do painel de Âncoras Espaciais para a criação de um recurso.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Depois que o recurso for criado, o portal do Azure mostrará que a implantação foi concluída. 
   
![Uma captura de tela mostrando que a implantação do recurso foi concluída.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Selecione **Ir para o recurso**. Agora você pode ver as propriedades do recurso. 
   
Copie o valor da **ID da Conta** do recurso em um editor de texto para usar posteriormente.

![Uma captura de tela do painel de propriedades do recurso.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Copie também o valor do **Domínio de Contas** do recurso em um editor de texto para usar posteriormente.

![Uma captura de tela mostrando o valor do domínio de contas do recurso.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Em **Configurações**, selecione **Chave**. Copie o valor da **Chave primária**, **Chave de Conta**, em um editor de texto para usar posteriormente.

![Uma captura de tela do painel Chaves da conta.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
