---
title: Falha na criação do cluster com erro DomainNotFound no Azure HDInsight
description: Etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76776229"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Cenário: falha na criação do cluster com o erro DomainNotFound no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A criação de cluster HDI Secure (Enterprise Security Package) `DomainNotFound` falha com a mensagem de erro.

## <a name="cause"></a>Causa

Configurações de DNS incorretas.

## <a name="resolution"></a>Resolução

Quando os clusters ingressados no domínio são implantados, o HDI cria um nome de usuário interno e uma senha no AAD DS (para cada cluster) e une todos os nós de cluster a esse domínio. O ingresso no domínio é realizado usando as ferramentas do samba. Verifique se os seguintes pré-requisitos foram atendidos:

* O nome de domínio deve ser resolvido por meio do DNS.
* O endereço IP dos controladores de domínio deve ser definido nas configurações de DNS para a rede virtual em que o cluster está sendo implantado.
* Se a rede virtual estiver emparelhada com a rede virtual do AAD DS, ela precisará ser feita manualmente.
* Se você estiver usando encaminhadores DNS, o nome de domínio deverá ser resolvido corretamente na rede virtual.
* As políticas de segurança (NSGs) não devem bloquear o ingresso no domínio.

### <a name="additional-debugging-steps"></a>Etapas de depuração adicionais

* Implantar uma VM do Windows na mesma sub-rede, ingressar no domínio usando um nome de usuário e uma senha (isso pode ser feito por meio da interface do usuário do painel de controle) ou

* Implantar uma VM do Ubuntu na mesma sub-rede e domínio ingressar no computador
  * SSH no computador
  * sudo su
  * Executar o script com nome de usuário e senha
  * O script executará o ping, criará os arquivos de configuração necessários e, em seguida, o domínio. Se tiver sucesso, as configurações de DNS serão boas.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
