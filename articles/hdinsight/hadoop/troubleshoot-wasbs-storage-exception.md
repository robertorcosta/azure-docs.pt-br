---
title: A conta que está sendo acessada não suporta erro http no Azure HDInsight
description: Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165547"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>A conta que está sendo acessada não suporta erro http no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A seguinte mensagem de erro é recebida:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Causa

Existem várias razões pelas quais a mensagem de erro é recebida:

* A conta de armazenamento tem [transferência segura](../../storage/common/storage-require-secure-transfer.md) ativada e o [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) incorreto está sendo usado.

* Um cluster foi criado com uma conta de armazenamento que tinha transferência segura *desativada*. Posteriormente, a transferência segura foi habilitada na conta de armazenamento.

## <a name="resolution"></a>Resolução

Se a transferência segura estiver ativada para o Azure Storage `wasbs://` `abfss://`ou data lake storage Gen2, o URI seria ou, respectivamente.  Confira também [Transferência segura](../../storage/common/storage-require-secure-transfer.md).

Para novos clusters, use uma conta de armazenamento que já tenha a configuração de transferência segura desejada. Não altere a configuração de transferência segura para uma conta de armazenamento que esteja em uso por um cluster existente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
