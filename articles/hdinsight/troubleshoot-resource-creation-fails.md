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
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287354"
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

1. Vá para a [portal do Azure](https://portal.azure.com)e selecione **ajuda + suporte**.

1. Selecione **Nova solicitação de suporte**.

1. Na guia **noções básicas** da página **nova solicitação de suporte** , forneça as seguintes informações:

   * **Tipo de problema:** Selecione **limites de serviço e assinatura (cotas)**.
   * **Assinatura:** Selecione a assinatura que você deseja modificar.
   * **Tipo de cota:** Selecione **HDInsight**.

Para obter mais informações, consulte [Criar um tíquete de suporte para aumentar o núcleo](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]