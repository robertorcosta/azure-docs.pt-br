---
title: Visualização dos dados usando Pastas de Trabalho do Azure Monitor no Azure Sentinel | Microsoft Docs
description: Use este tutorial para saber como visualizar seus dados usando pastas de trabalho no Azure Sentinel.
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
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 8d8f1343d92f66dc464ab7064949bbabb813268e
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798542"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Tutorial: Visualização e monitoramento dos dados



Depois [conectar as fontes de dados](quickstart-onboard.md) ao Azure Sentinel, será possível visualizar e monitorar os dados usando as Pastas de Trabalho do Azure Monitor com o Azure Sentinel, que oferece versatilidade na criação de painéis personalizados. Embora as Pastas de Trabalho sejam exibidas de diferentes maneiras no Azure Sentinel, pode ser útil ver como [criar relatórios interativos com as Pastas de Trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md). O Azure Sentinel permite que você crie pastas de trabalho personalizadas em seus dados e também vem com modelos de pasta de trabalho internos para que você possa obter insights rapidamente em seus dados assim que você conectar uma fonte de dados.


Este tutorial ajuda a visualizar os dados no Azure Sentinel.
> [!div class="checklist"]
> * Uso de pastas de trabalho internas
> * Criar pastas de trabalho

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter pelo menos as permissões de colaborador de Pasta de Trabalho ou de leitor de Pasta de Trabalho no grupo de recursos do workspace do Azure Sentinel.

> [!NOTE]
> As pastas de trabalho exibidas no Azure Sentinel são salvas no grupo de recursos do workspace do Azure Sentinel e são marcadas pelo workspace no qual foram criadas.

## <a name="use-built-in-workbooks"></a>Uso de pastas de trabalho internas

1. Acesse **Pastas de Trabalho** e, em seguida, selecione **Modelos** para ver a lista completa de pastas de trabalho internas do Azure Sentinel. Para ver quais são relevantes para os tipos de dados que você conectou, o campo **Tipos de dados necessários** em cada pasta de trabalho listará o tipo de dados ao lado de uma marca de seleção verde, se você já transmite dados relevantes para o Azure Sentinel.
  ![acessar pastas de trabalho](./media/tutorial-monitor-data/access-workbooks.png)
1. Clique em **Exibir pasta de trabalho** para ver o modelo preenchido com os seus dados.
  
1. Para editar a pasta de trabalho, selecione **Salvar** e, em seguida, selecione o local onde você deseja salvar o arquivo JSON para o modelo. 

   > [!NOTE]
   > Isso cria um recurso do Azure com base no modelo relevante e salva o próprio arquivo JSON de modelo, não os dados.


1. Selecione **Exibir pasta de trabalho**. Em seguida, clique no botão **Editar** na parte superior. Agora você pode editar a pasta de trabalho e personalizá-la de acordo com as suas necessidades. Para obter mais informações sobre como personalizar a pasta de trabalho, veja como [Criar relatórios interativos com as pastas de trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md).
![exibir pastas de trabalho](./media/tutorial-monitor-data/workbook-graph.png)
1. Salve a pasta de trabalho depois de fazer as alterações. 

1. Também é possível clonar a pasta de trabalho: Selecione **Editar** e, em seguida, **Salvar como**, salve-a com outro nome, na mesma assinatura e grupo de recursos. Essas pastas de trabalho são exibidas na guia **Minhas pastas de trabalho**.


## <a name="create-new-workbook"></a>Criação de uma nova pasta de trabalho

1. Acesse **Pastas de Trabalho** e. em seguida, selecione **Adicionar pasta de trabalho** para criar uma nova pasta de trabalho do zero.
  ![acessar pastas de trabalho](./media/tutorial-monitor-data/create-workbook.png)

1. Para editar a pasta de trabalho, selecione **Editar**e, em seguida, adicione texto, consultas e parâmetros conforme necessário. Para obter mais informações sobre como personalizar a pasta de trabalho, veja como [Criar relatórios interativos com as pastas de trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md). 

1. Quando criar uma consulta, a **Fonte de dados** está definida como **Logs**, o **Tipo de recurso** está definido como **Log Analytics** e, em seguida, escolha o(s) workspace(s) relevante(s). 

1. Depois de criar sua pasta de trabalho, salve-a na assinatura e no grupo de recursos do workspace do Azure Sentinel.

1. Se quiser permitir que outras pessoas em sua organização usem a pasta de trabalho, em **Salvar para** selecione **Relatórios compartilhados**. Se quiser que essa pasta de trabalho esteja disponível apenas para você, selecione **Meus relatórios**.

1. Para alternar entre pastas de trabalho no workspace, selecione **Abrir** ![Alternar pastas de trabalho](./media/tutorial-monitor-data/switch.png) no painel superior de qualquer pasta de trabalho. Na janela que abre à direita, alterne entre as pastas de trabalho.

   ![Alternar pastas de trabalho](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Como excluir pastas de trabalho

É possível excluir pastas de trabalho que foram criadas a partir de um modelo do Azure Sentinel. 

Para excluir uma pasta de trabalho personalizada, na página Pastas de Trabalho, selecione a pasta de trabalho salva que deseja excluir e selecione **Excluir**. Isso remove a pasta de trabalho salva.

> [!NOTE]
> Isso remove o recurso e as alterações feitas no modelo. O modelo original permanece disponível.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a visualizar os dados no Azure Sentinel.

Para saber como automatizar respostas a ameaças, veja [Configuração de respostas a ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).
