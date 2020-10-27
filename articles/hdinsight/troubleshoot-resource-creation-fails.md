---
title: Solucionar falhas de criação de recursos no Azure HDInsight
description: Os erros comuns de problema de capacidade e técnicas de mitigação são fornecidos neste artigo.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: a47781bcdf60d863d6dcf77d70bc0ee496a51388
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538792"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Solucionar falhas de criação de recursos no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com o Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erro: a implantação excederia a cota de ' 800 '

O Azure tem um limite de cota de 800 implantações por grupo de recursos. Cotas diferentes são aplicadas por grupo de recursos, assinatura, conta ou outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Tentar implantar uma máquina virtual com mais núcleos do que o permitido resulta na mensagem de erro informando que a cota foi excedida.

Para resolver esse problema, exclua as implantações que não são mais necessárias usando o portal do Azure, a CLI ou o PowerShell.

Para obter mais informações, consulte [Solucionar erros de cotas de recursos](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erro: o nó máximo excedeu os núcleos disponíveis nesta região

Sua assinatura pode estar configurada para limitar o número de núcleos de uma região. A tentativa de implantar um recurso com mais núcleos do que o permitido resulta na mensagem de erro informando que a cota foi excedida.

Para solicitar um aumento de cota, siga essas etapas:

1. Vá para a [portal do Azure](https://portal.azure.com)e selecione **ajuda + suporte** .

1. Selecione **Nova solicitação de suporte** .

1. Na guia **noções básicas** da página **nova solicitação de suporte** , forneça as seguintes informações:

   * **Tipo de problema:** Selecione **limites de serviço e assinatura (cotas)** .
   * **Assinatura:** Selecione a assinatura que você deseja modificar.
   * **Tipo de cota:** Selecione **HDInsight** .

Para obter mais informações, consulte [Criar um tíquete de suporte para aumentar o núcleo](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte** . Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).