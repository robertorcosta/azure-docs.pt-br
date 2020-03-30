---
title: Gerenciar workspaces
titleSuffix: ML Studio (classic) - Azure
description: Gerencie o acesso aos espaços de trabalho do Azure Machine Learning Studio (clássico) e implante e gerencie serviços web de API de aprendizado de máquina
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217961"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Gerencie um espaço de trabalho azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Para obter informações sobre como gerenciar serviços Web no portal de serviços Web do Machine Learning, veja [Gerenciar um serviço Web usando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md).
> 
> 

Você pode gerenciar espaços de trabalho do Machine Learning Studio (clássico) no portal Azure.



## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Para gerenciar um espaço de trabalho studio (clássico) no portal Azure:

1. Entre no [Portal do Azure](https://portal.azure.com/) usando uma conta de administrador de assinatura do Azure.
2. Na caixa de pesquisa no topo da página, digite "machine learning Studio (clássico) espaços de trabalho" e, em seguida, selecione **Espaços de trabalho Machine Learning Studio (clássicos).**
3. Clique no workspace que você deseja gerenciar.

Além das informações de gerenciamento de recursos padrão e das opções disponíveis, você pode:

- Exibir **Propriedades** - essa página exibe as informações do workspace e dos recursos e você pode alterar o assinatura e o grupo de recursos aos quais esse workspace está conectado.
- **Ressincronizar as Chaves de Armazenamento** - o workspace mantém chaves para a conta de armazenamento. Se a conta de armazenamento alterar as chaves, clique em **Ressincronizar chaves** para sincronizar as chaves com o workspace.

Para gerenciar os serviços web associados a este espaço de trabalho (clássico) do Studio, use o portal Serviços Web de Aprendizagem de Máquina. Consulte [Gerenciar um serviço Web usando o portal de Serviços Web do Azure Machine Learning](manage-new-webservice.md) para obter informações completas.

> [!NOTE]
> Para implantar ou gerenciar Novos serviços Web, você deverá ser atribuído a uma função de colaborador ou de administrador na assinatura na qual o serviço Web é implantado. Se você convidar outro usuário para um espaço de trabalho do Machine Learning Studio (clássico), você deve atribuí-lo a uma função de contribuinte ou administrador na assinatura antes que eles possam implantar ou gerenciar serviços web. 
> 
>Para obter mais informações sobre como definir permissões de acesso, confira [Gerenciar o acesso usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [implantar o Machine Learning com modelos do Azure Resource Manager](deploy-with-resource-manager-template.md). 
