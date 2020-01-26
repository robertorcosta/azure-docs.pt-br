---
title: Recuperar um painel excluído no portal do Azure | Microsoft Docs
description: Se você excluir um painel publicado no portal do Azure, poderá recuperar o painel.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760191"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Recuperar um painel excluído no portal do Azure

Se você excluir um painel _publicado_ no portal do Azure, poderá recuperar esse painel dentro de 14 dias da exclusão. Se o painel não for publicado, você não poderá recuperá-lo e deverá reconstruí-lo. Para obter mais informações sobre como publicar um painel, consulte [publicar painel](azure-portal-dashboard-share-access.md#publish-dashboard). Siga estas etapas para recuperar um painel publicado:

1. No menu portal do Azure, selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos em que você publicou o painel (por padrão, eles são nomeados como **painéis**).

1. Em **log de atividades**, expanda a operação **excluir painel** . Selecione a guia **histórico de alterações** e, em seguida, selecione **\<\>de recursos excluídos** .

    ![Captura de tela da guia histórico de alterações](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecione e copie o conteúdo do painel esquerdo e, em seguida, salve em um arquivo de texto com uma extensão de arquivo _. JSON_ . O portal usa o arquivo JSON para recriar o painel.

    ![Captura de tela da diferença do histórico de alterações](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. No menu portal do Azure, selecione **painéis**e, em seguida, selecione **carregar**.

    ![Captura de tela do upload do painel](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecione o arquivo JSON que você salvou. O portal recria o painel com o mesmo nome e elementos do painel excluído.

1. Selecione **compartilhar** para publicar o painel e restabelecer o controle de acesso apropriado.

    ![Captura de tela do compartilhamento de painel](media/recover-shared-deleted-dashboard/dashboard-share.png)
