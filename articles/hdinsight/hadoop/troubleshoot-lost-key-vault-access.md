---
title: Clusters Azure HDInsight com criptografia de disco perdem acesso ao Key Vault
description: Solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461524"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Cenário: Clusters Azure HDInsight com criptografia de disco perdem acesso ao Key Vault

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O alerta `The HDInsight cluster is unable to access the key for BYOK encryption at rest`do Resource Health Center (RHC) é mostrado para clusters Bring Your Own Key (BYOK) onde os nós de cluster perderam o acesso aos clientes Key Vault (KV). Alertas semelhantes também podem ser vistos na UI Apache Ambari.

## <a name="cause"></a>Causa

O alerta garante que o KV esteja acessível a partir dos nós de cluster, garantindo assim a conexão de rede, a saúde do KV e a política de acesso para o usuário atribuído identidade gerenciada. Este alerta é apenas um aviso de desligamento iminente do corretor nas reinicializações subseqüentes do nó, o cluster continua funcionando até que os nódulos reiniciem.

Navegue até a UI Apache Ambari para encontrar mais informações sobre o alerta do **Disk Encryption Key Vault Status**. Este alerta terá detalhes sobre o motivo da falha na verificação.

## <a name="resolution"></a>Resolução

### <a name="kvaad-outage"></a>Paralisação kv/AAD

Veja [a disponibilidade e a redundância do Azure Key Vault e](../../key-vault/general/disaster-recovery-guidance.md) a página de status do Azure para obter mais detalheshttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>Exclusão acidental kv

* Restaurar a chave excluída no KV para recuperar automaticamente. Para obter mais informações, consulte [Recuperar chave excluída](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Entre em contato com a equipe kv para se recuperar de exclusões acidentais.

### <a name="kv-access-policy-changed"></a>A política de acesso kv alterada

Restaurar as políticas de acesso para o usuário atribuído identidade gerenciada que é atribuída ao cluster HDI para acessar o KV.

### <a name="key-permitted-operations"></a>Principais operações permitidas

Para cada tecla em KV, você pode escolher o conjunto de operações permitidas. Certifique-se de que você tenha operações de envoltório e desembrulhar ativadas para a tecla BYOK

### <a name="expired-key"></a>Chave expirada

Se o vencimento tiver passado e a tecla não for girada, restaure a chave do HSM de backup ou entre em contato com a equipe da KV para limpar a data de validade.

### <a name="kv-firewall-blocking-access"></a>Firewall KV bloqueando o acesso

Corrija as configurações de firewall KV para permitir que os nós de cluster BYOK acessem o KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Regras do NSG sobre o acesso de bloqueio de rede virtual

Verifique as regras do NSG associadas à rede virtual anexada ao cluster.

## <a name="mitigation-and-prevention-steps"></a>Medidas de mitigação e prevenção

### <a name="kv-accidental-deletion"></a>Exclusão acidental kv

* Configure o cofre de chaves com [o conjunto de bloqueio de recursos](../../azure-resource-manager/management/lock-resources.md).
* Fazer backup das teclas do módulo de segurança de hardware.

### <a name="key-deletion"></a>Exclusão da chave

O cluster deve ser excluído antes da exclusão da tecla.

### <a name="kv-access-policy-changed"></a>A política de acesso kv alterada

Audite e teste regularmente as políticas de acesso.

### <a name="expired-key"></a>Chave expirada

* Afaste as chaves do seu HSM.
* Use uma chave sem qualquer conjunto de expiração.
* Se o vencimento precisar ser definido, gire as teclas antes da data de validade.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
