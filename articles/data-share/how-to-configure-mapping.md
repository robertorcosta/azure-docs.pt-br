---
title: Configure um mapeamento de conjunto de dados no Azure Data Share
description: Saiba como configurar um mapeamento de conjunto de dados para um compartilhamento recebido usando o Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964236"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de conjunto de dados para um compartilhamento recebido no Azure Data Share

Este artigo explica como configurar um mapeamento de conjunto de dados para um Compartilhamento Recebido usando o Azure Data Share. Você vai querer fazer isso se aceitar um convite de compartilhamento de dados, mas optou por "Aceitar e configurar mais tarde", ou se os dados forem compartilhados no local. Você pode querer configurar um mapeamento do conjunto de dados se precisar alterar o destino dos dados que estão sendo compartilhados com você ou se quiser receber dados em um SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navegue para um compartilhamento de dados recebido

No serviço Azure Data Share, navegue até o compartilhamento recebido e selecione a guia **Detalhes.** 

![Mapeamento de conjuntos de dados](./media/dataset-mapping.png "Mapeamento de conjuntos de dados") 

Marque a caixa ao lado do conjunto de dados para o quais você gostaria de atribuir um destino. Selecione **'Desmapear'** para desmapear o mapeamento existente. Selecione **+ Mapa para segmentar** para escolher uma nova loja de destino. 

![Mapa para alvo](./media/dataset-map-target.png "Mapa para alvo") 

## <a name="select-a-new-target-store"></a>Selecione uma nova loja-alvo

Selecione um tipo de dados de destino que você gostaria que os dados aterrissassem. Para compartilhamento baseado em instantâneo, todos os dados que já existem em quaisquer contas de armazenamento previamente mapeadas não serão automaticamente transferidos para o novo armazenamento-alvo. Para compartilhamento no local, selecione um armazenamento de dados no Local especificado. A localização é o data center do Azure onde o armazenamento de dados de origem do provedor de dados está localizado.

![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecione um formato de arquivo (apenas fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL, você poderá escolher em qual formato ele é recebido. 

![Escolher formato](./media/sql-file-formats.png "Formatos de arquivo SQL")

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).



