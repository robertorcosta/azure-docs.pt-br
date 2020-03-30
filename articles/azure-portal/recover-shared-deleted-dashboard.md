---
title: Recuperar um painel excluído no portal Azure | Microsoft Docs
description: Se você excluir um painel publicado no portal Azure, você pode recuperar o painel de controle.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133284"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Recupere um painel excluído no portal Azure

Se você estiver na nuvem pública do Azure e excluir um painel _publicado_ no portal Azure, você pode recuperar esse painel dentro de 14 dias após a exclusão. Se você está em uma nuvem do governo Azure ou o painel não é publicado, você não pode recuperá-lo, e você deve reconstruí-lo. Para obter mais informações sobre a publicação de um painel, consulte [Publicar painel](azure-portal-dashboard-share-access.md#publish-dashboard). Siga estas etapas para recuperar um painel publicado:

1. No menu portal do Azure, selecione **Grupos de recursos**e selecione o grupo de recursos onde você publicou o painel (por padrão, ele é chamado **dashboards**).

1. Em **Registro de atividades,** expanda a operação **'Excluir painel'.** Selecione a guia **Alterar histórico** e, em seguida, ** \<selecione recurso\>excluído**.

    ![Captura de tela da guia histórico de alterações](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecione e copie o conteúdo do painel esquerdo e salve um arquivo de texto com uma extensão de arquivo _.json._ O portal usa o arquivo JSON para recriar o painel.

    ![Captura de tela do diff história de mudança](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. No menu do portal Azure, selecione **Dashboards**e selecione **Upload**.

    ![Captura de tela do upload do painel](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecione o arquivo JSON que você salvou. O portal recria o painel com o mesmo nome e elementos do painel excluído.

1. Selecione **Compartilhar** para publicar o painel e restabelecer o controle de acesso apropriado.

    ![Captura de tela do compartilhamento de dashboard](media/recover-shared-deleted-dashboard/dashboard-share.png)
