---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489919"
---
>[!IMPORTANT]
>Você pode usar os recursos que criou como pré-requisitos em outros tutoriais e artigos de instruções do Serviço do Azure Machine Learning.

### <a name="delete-everything"></a>Excluir tudo

Se você não pretende usar os recursos criados, exclua todo o grupo de recursos para não gerar encargos:

1. No portal do Azure, selecione **Grupos de recursos** no lado esquerdo da janela.
 
   ![Excluir um grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. No lado direito da janela, selecione o botão de reticências ( **...** ).

1. Selecione **Excluir grupo de recursos**.

A exclusão de um grupo de recursos também exclui todos os recursos criados no designer.  

### <a name="delete-only-the-compute-target"></a>Excluir apenas o destino de computação

O destino de computação que você criou aqui é *dimensionado automaticamente* para zero nós quando não estiver sendo usado. Isso serve para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra o workspace.

    ![Excluir o destino de computação](./media/aml-ui-cleanup/delete-compute-target.png)

1. Na seção **Computação** do seu workspace, selecione o recurso.

1. Selecione **Excluir**.

### <a name="delete-individual-assets"></a>Excluir recursos individuais

No designer em que você criou seu experimento, exclua ativos individuais selecionando-os e, em seguida, selecionando o botão **Excluir**.

![Excluir ativos](./media/aml-ui-cleanup/delete-asset.png)

Os conjuntos de dados podem ter o registro cancelado do seu workspace. Para isso, selecione cada conjunto de dados e, em seguida, selecione **Cancelar registro**.

![Cancelar registro do conjunto de registros](./media/aml-ui-cleanup/unregister-dataset.png)


