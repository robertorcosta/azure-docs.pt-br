---
title: Clusters do Azure HDInsight com criptografia de disco perdem Key Vault acesso
description: Etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461524"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Cenário: clusters do Azure HDInsight com criptografia de disco perdem Key Vault acesso

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O alerta do Resource Health Center (RHC) `The HDInsight cluster is unable to access the key for BYOK encryption at rest`,, é mostrado para clusters Bring your own Key (BYOK) em que os nós do cluster perderam acesso aos clientes key Vault (kV). Alertas semelhantes também podem ser vistos na interface do usuário do Apache Ambari.

## <a name="cause"></a>Causa

O alerta garante que o KV seja acessível a partir dos nós de cluster, garantindo, assim, a conexão de rede, a integridade do KV e a política de acesso para a identidade gerenciada atribuída pelo usuário. Esse alerta é apenas um aviso de desligamento iminente do agente em reinicializações de nó subsequentes, o cluster continua a funcionar até que os nós sejam reinicializados.

Navegue até a interface do usuário do Apache Ambari para obter mais informações sobre o alerta de **criptografia de disco Key Vault status**. Esse alerta terá detalhes sobre o motivo da falha de verificação.

## <a name="resolution"></a>Resolução

### <a name="kvaad-outage"></a>Interrupção de KV/AAD

Veja [Azure Key Vault disponibilidade e redundância](../../key-vault/general/disaster-recovery-guidance.md) e a página de status do Azure para obter mais detalheshttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>Exclusão acidental do KV

* Restaure a chave excluída em KV para recuperar automaticamente. Para obter mais informações, consulte [recuperar chave excluída](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Entre em contato com a equipe do KV para se recuperar de exclusões acidentais.

### <a name="kv-access-policy-changed"></a>Política de acesso do KV alterada

Restaure as políticas de acesso para a identidade gerenciada atribuída pelo usuário atribuída ao cluster HDI para acessar o KV.

### <a name="key-permitted-operations"></a>Operações de chave permitidas

Para cada chave em KV, você pode escolher o conjunto de operações permitidas. Verifique se você tem operações de encapsulamento e desencapsulamento habilitadas para a chave BYOK

### <a name="expired-key"></a>Chave expirada

Se a expiração tiver passado e a chave não for girada, restaure a chave do backup HSM ou contate a equipe do KV para limpar a data de expiração.

### <a name="kv-firewall-blocking-access"></a>Firewall do KV bloqueando o acesso

Corrija as configurações do firewall do KV para permitir que os nós de cluster do BYOK acessem o KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Regras de NSG sobre acesso de bloqueio de rede virtual

Verifique as regras de NSG associadas à rede virtual anexada ao cluster.

## <a name="mitigation-and-prevention-steps"></a>Etapas de mitigação e prevenção

### <a name="kv-accidental-deletion"></a>Exclusão acidental do KV

* Configure Key Vault com o [conjunto de bloqueios de recurso](../../azure-resource-manager/management/lock-resources.md).
* Faça backup das chaves em seu módulo de segurança de hardware.

### <a name="key-deletion"></a>Exclusão de chave

O cluster deve ser excluído antes da exclusão da chave.

### <a name="kv-access-policy-changed"></a>Política de acesso do KV alterada

Auditar e testar regularmente as políticas de acesso.

### <a name="expired-key"></a>Chave expirada

* Faça backup das chaves para o HSM.
* Use uma chave sem nenhum conjunto de expiração.
* Se a expiração precisar ser definida, gire as chaves antes da data de validade.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
