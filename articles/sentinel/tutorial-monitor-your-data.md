---
title: Visualize seus dados usando dashboards baseados em Workbooks do Monitor Do Azure no Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender como visualizar seus dados usando dashboards baseados em livros de trabalho no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585213"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Tutorial: Visualize e monitore seus dados



Depois [de conectar suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, você pode visualizar e monitorar os dados usando a adoção do Azure Sentinel do Azure Monitor Workbooks, que fornece versatilidade na criação de dashboards personalizados. Embora as Workbooks sejam exibidas de forma diferente no Azure Sentinel, pode ser útil para você ver como [criar relatórios interativos com a Azure Monitor Workbooks](../azure-monitor/app/usage-workbooks.md). O Azure Sentinel permite que você crie pastas de trabalho personalizadas em seus dados e também vem com modelos de pasta de trabalho internos para que você possa obter insights rapidamente em seus dados assim que você conectar uma fonte de dados.


Este tutorial ajuda você a visualizar seus dados no Azure Sentinel.
> [!div class="checklist"]
> * Usar pastas de trabalho internas
> * Criar pastas de trabalho

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter pelo menos permissões de contribuinte do leitor de pasta de trabalho ou da pasta de trabalho no grupo de recursos do espaço de trabalho do Azure Sentinel.

> [!NOTE]
> As anotações que você pode ver no Azure Sentinel são salvas dentro do grupo de recursos do espaço de trabalho do Azure Sentinel e são marcadas pelo espaço de trabalho em que foram criadas.

## <a name="use-built-in-workbooks"></a>Usar pastas de trabalho internas

1. Vá para **Workbooks** e selecione **Templates** para ver a lista completa de livros de trabalho incorporados do Azure Sentinel. Para ver quais são relevantes para os tipos de dados conectados, o campo **de tipos de dados necessários** em cada pasta de trabalho listará o tipo de dados ao lado de uma marca de verificação verde se você já transmitir dados relevantes para o Azure Sentinel.
  ![ir para as apra](./media/tutorial-monitor-data/access-workbooks.png)
1. Clique **em Exibir a agenda para** ver o modelo preenchido com seus dados.
  
1. Para editar a pasta de trabalho, **selecione Salvar**e selecione o local onde deseja salvar o arquivo json para o modelo. 

   > [!NOTE]
   > Isso cria um recurso do Azure com base no modelo relevante e salva o próprio arquivo Json do modelo e não os dados.


1. Selecione **Exibir a agenda de trabalho**. Em seguida, clique no botão **Editar** na parte superior. Agora você pode editar a carteira de trabalho e personalizá-la de acordo com suas necessidades. Para obter mais informações sobre como personalizar a pasta de trabalho, consulte como [criar relatórios interativos com a Pasta de Trabalho do Monitor Do Azure](../azure-monitor/app/usage-workbooks.md).
![ver livros de trabalho](./media/tutorial-monitor-data/workbook-graph.png)
1. Depois de fazer suas alterações, você pode salvar a carteira de trabalho. 

1. Você também pode clonar a carteira de trabalho: Selecione **Editar** e, em seguida, **Salvar como**, certificando-se de salvá-lo com outro nome, a mesma assinatura e grupo de recursos. Essas anotaçãos são exibidas na aba **Minhas contações.**


## <a name="create-new-workbook"></a>Criar nova carteira de trabalho

1. Vá para **Workbooks** e, em seguida, selecione **Adicionar carteira de trabalho** para criar uma nova carteira de trabalho do zero.
  ![ir para as apra](./media/tutorial-monitor-data/create-workbook.png)

1. Para editar a agenda, selecione **Editar**e, em seguida, adicione texto, consultas e parâmetros conforme necessário. Para obter mais informações sobre como personalizar a pasta de trabalho, consulte como [criar relatórios interativos com a Pasta de Trabalho do Monitor Do Azure](../azure-monitor/app/usage-workbooks.md). 

1. Ao criar uma consulta, definir a **origem de dados** é definida como **Logs,** o **tipo de recurso** é definido como **Log Analytics** e, em seguida, escolha o espaço de trabalho(s) relevante. 

1. Depois de criar sua pasta de trabalho, salve a pasta de trabalho certificando-se de salvá-la o grupo de assinatura e recursos do seu espaço de trabalho do Azure Sentinel.

1. Se você quiser deixar que outros em sua organização usem a carteira de trabalho, em **Salvar para** selecionar **relatórios compartilhados**. Se você quiser que esta carteira de trabalho esteja disponível apenas para você, selecione **Meus relatórios**.

1. Para alternar entre as carteiras de **Open** ![trabalho em](./media/tutorial-monitor-data/switch.png)seu espaço de trabalho, você pode selecionar as regras de trabalho open switch no painel superior de qualquer pasta de trabalho. Na janela que se abre para a direita, troque entre livros de trabalho.

   ![Trocar livros de trabalho](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Como excluir pastas de trabalho

Você pode excluir workbooks que foram criadas a partir de um modelo do Azure Sentinel. 

Para excluir uma carteira de trabalho personalizada, na página Da Carteira de Trabalho, selecione a carteira de trabalho salva que deseja excluir e **selecione Excluir**. Isso removerá a carteira de trabalho salva.

> [!NOTE]
> Isso remove o recurso, bem como quaisquer alterações feitas no modelo. O modelo original permanecerá disponível.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a visualizar seus dados no Azure Sentinel.

Para saber como automatizar suas respostas a ameaças, consulte [Configurar respostas automatizadas de ameaças no Azure Sentinel](tutorial-respond-threats-playbook.md).
