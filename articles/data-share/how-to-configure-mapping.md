---
title: Configurar um mapeamento de conjunto de dados no Azure data share
description: Saiba como configurar um mapeamento de DataSet para um compartilhamento recebido usando o compartilhamento de dados do Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 82de05e7169c5803dd999521f61a33b9dd17b567
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511948"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de DataSet para um compartilhamento recebido no compartilhamento de dados do Azure

Este artigo explica como configurar um mapeamento de DataSet para um compartilhamento recebido usando o compartilhamento de dados do Azure. Você desejará fazer isso se tiver aceitado um convite de compartilhamento de dados, mas tiver optado por "aceitar e configurar mais tarde", ou se os dados forem compartilhados in-loco. Talvez você queira configurar um mapeamento de conjunto de dados se precisar alterar o destino para que sejam compartilhados com você ou se desejar receber dados em um SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navegar até um compartilhamento de dados recebido

No serviço de compartilhamento de dados do Azure, navegue até o compartilhamento recebido e selecione a guia **detalhes** . 

![Mapeamento de DataSet](./media/dataset-mapping.png "Mapeamento de DataSet") 

Marque a caixa ao lado do conjunto de um para o qual você gostaria de atribuir um destino. Selecione **desmapear** para desmapear o mapeamento existente. Selecione **+ mapear para destino** para escolher um novo repositório de destino. 

![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

## <a name="select-a-new-target-store"></a>Selecione um novo repositório de destino

Selecione um tipo de dados de destino no qual você deseja que os dados entrem. Para o compartilhamento baseado em instantâneo, todos os dados que já existem em todas as contas de armazenamento mapeadas anteriormente não serão automaticamente movidos para o novo repositório de destino. Para compartilhamento in-loco, selecione um repositório de dados no local especificado. O local é o data center do Azure no qual o armazenamento de dados de origem do provedor de dados está localizado.

![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecionar um formato de arquivo (somente fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL, você poderá escolher em qual formato ele será recebido. 

![Escolher formato](./media/sql-file-formats.png "Formatos de arquivo SQL")

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).



