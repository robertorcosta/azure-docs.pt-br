---
title: Não é possível fazer logon no cluster HDInsight do Azure
description: Solucionar problemas por que não é possível fazer logon no cluster Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894063"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Cenário: não é possível fazer logon no cluster HDInsight do Azure

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível fazer logon no cluster HDInsight do Azure.

## <a name="cause"></a>Causa

Os motivos podem variar. Tenha em mente que, ao fazer logon nos painéis do cluster ou do aplicativo, use o "logon do cluster" ou as credenciais HTTP. Ao conectar-se remotamente, use suas credenciais do Secure Shell (SSH) ou a área de trabalho remota.

## <a name="resolution"></a>Resolução

Para resolver problemas comuns, tente uma ou mais das etapas a seguir.

* Tente abrir o painel do cluster em uma nova guia do navegador no modo de privacidade.

* Se não for possível lembrar suas credenciais SSH, você poderá [redefinir as credenciais na interface do usuário do amAmbari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
