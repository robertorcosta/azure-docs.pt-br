---
title: Criação de cluster falha com erro DomainNotFound no Azure HDInsight
description: Solucionando etapas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776229"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Cenário: Criação de cluster falha com erro DomainNotFound no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A criação do cluster HDI Secure `DomainNotFound` (Enterprise Security Package) falha com a mensagem de erro.

## <a name="cause"></a>Causa

Configurações incorretas de DNS.

## <a name="resolution"></a>Resolução

Quando os clusters unidos de domínio são implantados, o HDI cria um nome de usuário e senha internos no AAD DS (para cada cluster) e junta todos os nós de cluster a este domínio. A adesão ao domínio é realizada usando ferramentas samba. Verifique se os seguintes pré-requisitos foram atendidos:

* O nome de domínio deve ser resolvido através do DNS.
* O endereço IP dos controladores de domínio deve ser definido nas configurações de DNS para a rede virtual onde o cluster está sendo implantado.
* Se a rede virtual estiver acompanhada da rede virtual do AAD DS, então ela tem que ser feita manualmente.
* Se você estiver usando os reencaminhadores DNS, o nome de domínio deve ser resolvido corretamente dentro da rede virtual.
* As políticas de segurança (NSGs) não devem bloquear a adesão ao domínio.

### <a name="additional-debugging-steps"></a>Etapas adicionais de depuração

* Implantar uma VM windows na mesma sub-rede, o domínio junte-se à máquina usando um nome de usuário e senha (isso pode ser feito através da ui do painel de controle), ou

* Implantar um VM ubuntu na mesma sub-rede e domínio junte-se à máquina
  * SSH na máquina
  * sudo su
  * Execute o script com nome de usuário e senha
  * O script fará ping, criará os arquivos de configuração necessários e, em seguida, domínio. Se for bem sucedido, as configurações do DNS são boas.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
