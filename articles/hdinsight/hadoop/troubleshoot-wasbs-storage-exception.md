---
title: A conta que está sendo acessada não dá suporte ao erro de http no Azure HDInsight
description: Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165547"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>A conta que está sendo acessada não dá suporte ao erro de http no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A seguinte mensagem de erro é recebida:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Causa

Há várias razões pelas quais a mensagem de erro é recebida:

* A conta de armazenamento tem a [transferência segura](../../storage/common/storage-require-secure-transfer.md) habilitada e o [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) incorreto está sendo usado.

* Um cluster foi criado com uma conta de armazenamento que tinha a transferência segura *desabilitada*. Depois disso, a transferência segura foi habilitada na conta de armazenamento.

## <a name="resolution"></a>Resolução

Se a transferência segura estiver habilitada para o armazenamento do Azure ou Data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://`, respectivamente.  Confira também [Transferência segura](../../storage/common/storage-require-secure-transfer.md).

Para novos clusters, use uma conta de armazenamento que já tenha a configuração de transferência segura desejada. Não altere a configuração de transferência segura para uma conta de armazenamento que esteja em uso por um cluster existente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
