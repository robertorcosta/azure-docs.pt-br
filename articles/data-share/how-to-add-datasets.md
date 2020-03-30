---
title: Adicionar conjuntos de dados a um compartilhamento de dados do Azure existente
description: Saiba como adicionar conjuntos de dados a um compartilhamento de dados existente no Azure Data Share e compartilhe com os mesmos destinatários.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490547"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Como adicionar conjuntos de dados a um compartilhamento de dados existente no Azure Data Share

Este artigo explica como adicionar conjuntos de dados a um compartilhamento de dados pré-existente usando o Azure Data Share. Isso permite compartilhar mais dados com os mesmos destinatários sem ter que criar um novo compartilhamento.

Para obter informações sobre como adicionar conjuntos de dados à medida que você cria um compartilhamento, consulte o tutorial [de dados compartilhar.](share-your-data.md)

## <a name="navigate-to-a-sent-data-share"></a>Navegue para um compartilhamento de dados enviado

No Azure Data Share, navegue até o seu compartilhamento enviado e selecione a guia **Conjuntos de dados.** Clique **em + Adicione conjuntos de dados** para adicionar mais conjuntos de dados.

![Adicionar conjuntos de dados](./media/how-to/how-to-add-datasets/add-datasets.png)

No painel à direita, selecione o tipo de conjunto de dados que deseja adicionar e clique em **Next**. Selecione o grupo de assinatura e recursos dos dados que você gostaria de adicionar. Usando as setas gotas, encontre e, em seguida, verifique a caixa ao lado dos dados para adicionar.

![Adicionar conjuntos de dados](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Depois de clicar em **Adicionar conjuntos de dados,** os conjuntos de dados serão adicionados ao seu compartilhamento. Nota: Um instantâneo deve ser acionado por seus consumidores para que eles vejam os novos conjuntos de dados. Se houver configurações de snapshot configuradas, os consumidores verão os novos conjuntos de dados assim que o próximo instantâneo programado for concluído. Sem as configurações de instantâneo configuradas, o consumidor deve acionar manualmente uma cópia completa ou incremental dos dados para receber as atualizações. Para obter mais informações sobre instantâneos, consulte [Snapshots](terminology.md).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [adicionar destinatários a um compartilhamento de dados existente.](how-to-add-recipients.md)