---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 66f5c72fcabb62e21f0110cb981b7271244c0648
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799630"
---
>[!IMPORTANT]
>Você pode usar os recursos que criou como pré-requisitos em outros tutoriais e artigos de instruções do Serviço do Azure Machine Learning.

### <a name="delete-everything"></a>Excluir tudo

Se você não pretende usar os recursos criados, exclua todo o grupo de recursos para não gerar encargos:

1. No portal do Azure, selecione **Grupos de recursos** no lado esquerdo da janela.
 
   ![Excluir um grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. Selecione **Excluir grupo de recursos**.

A exclusão de um grupo de recursos também exclui todos os recursos criados no designer.  

### <a name="delete-individual-assets"></a>Excluir recursos individuais

No designer em que você criou seu experimento, exclua ativos individuais selecionando-os e, em seguida, selecionando o botão **Excluir**.

O destino de computação que você criou aqui é *dimensionado automaticamente* para zero nós quando não estiver sendo usado. Isso serve para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

![Excluir ativos](./media/aml-ui-cleanup/delete-asset.png)

Os conjuntos de dados podem ter o registro cancelado do seu workspace. Para isso, selecione cada conjunto de dados e, em seguida, selecione **Cancelar registro**.

![Cancelar registro do conjunto de registros](./media/aml-ui-cleanup/unregister-dataset.png)

Para excluir um conjunto de dados, navegue até a conta de armazenamento usando o portal do Azure ou o Gerenciador de Armazenamento e exclua manualmente esses ativos.


