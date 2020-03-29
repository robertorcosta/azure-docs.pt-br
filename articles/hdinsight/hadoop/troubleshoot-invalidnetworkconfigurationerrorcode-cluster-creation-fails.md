---
title: InvalidNetworkConfigurationErrorCode erro - Azure HDInsight
description: Várias razões para criações de cluster com invalidNetworkConfigurationErrorCode no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720377"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>A criação de cluster falha com a configuração de rede inválidaCódigo de erro no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

Se você vir `InvalidNetworkConfigurationErrorCode` o código de erro com a descrição "A configuração da Rede Virtual não é compatível com o Requisito HDInsight", geralmente indica um problema com a [configuração](../hdinsight-plan-virtual-network-deployment.md) de rede virtual para o seu cluster. Com base no resto da descrição do erro, siga as seções abaixo para resolver seu problema.

## <a name="hostname-resolution-failed"></a>"Falha na resolução hostname"

### <a name="issue"></a>Problema

A descrição do erro contém "Falha na resolução hostname".

### <a name="cause"></a>Causa

Este erro aponta para um problema com a configuração personalizada de DNS. Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para os resolvedores recursivos do Azure para resolver nomes de host dentro dessa rede virtual (consulte [Resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para obter detalhes). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16. Este IP só é acessível a partir das VMs do Azure. Portanto, não funcionará se você estiver usando um servidor OnPrem DNS, ou seu servidor DNS for um VM Do Zure, que não faz parte da rede virtual do cluster.

### <a name="resolution"></a>Resolução

1. Ssh no VM que faz parte do cluster, e executar o comando `hostname -f`. Isso retornará o nome de domínio totalmente `<host_fqdn>` qualificado do host (referido como nas instruções abaixo).

1. Em seguida, `nslookup <host_fqdn>` execute o `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`comando (por exemplo, ). Se este comando resolver o nome em um endereço IP, significa que seu servidor DNS está funcionando corretamente. Neste caso, levante um caso de suporte com o HDInsight, e investigaremos seu problema. No seu caso de suporte, inclua as etapas de solução de problemas executadas. Isso nos ajudará a resolver o problema mais rapidamente.

1. Se o comando acima não retornar um `nslookup <host_fqdn> 168.63.129.16` endereço IP, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`então seja executado (por exemplo, ). Se este comando for capaz de resolver o IP, significa que o servidor DNS não está encaminhando a consulta para o DNS do Azure, ou não é uma VM que faz parte da mesma rede virtual que o cluster.

1. Se você não tem uma VM Azure que possa atuar como um servidor DNS personalizado na rede virtual do cluster, então você precisa adicionar isso primeiro. Crie uma VM na rede virtual, que será configurada como renador de DNS.

1. Depois de ter uma VM implantada em sua rede virtual, configure as regras de encaminhamento de DNS nesta VM. Encaminhe todas as solicitações de resolução de nomes iDNS para 168.63.129.16 e o restante para o servidor DNS. [Aqui](../hdinsight-plan-virtual-network-deployment.md) está um exemplo desta configuração para um servidor DNS personalizado.

1. Adicione o endereço IP desta VM como primeira entrada DNS para a configuração de DNS de rede virtual.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Falha ao conectar-se à conta de armazenamento do Azure"

### <a name="issue"></a>Problema

A descrição do erro contém "Falha ao conectar-se à Conta de Armazenamento Do Zure" ou "Falha ao conectar-se ao Azure SQL".

### <a name="cause"></a>Causa

O Azure Storage e o SQL não possuem endereços IP fixos, por isso precisamos permitir conexões de saída para todos os IPs para permitir o acesso a esses serviços. As etapas exatas de resolução dependem se você criou um Grupo de Segurança de Rede (NSG) ou UDR (User-Defined Rules, regras definidas pelo usuário). Consulte a seção sobre [controle do tráfego de rede com o HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) para obter detalhes sobre essas configurações.

### <a name="resolution"></a>Resolução

* Se o cluster usar um [NSG (Network Security Group, grupo de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Acesse o portal do Azure e identifique o NSG associado à sub-rede onde o cluster está sendo implantado. Na seção **Regras de segurança de saída,** permita o acesso de saída à internet sem limitação (observe que um número de **prioridade** menor aqui significa maior prioridade). Além disso, na seção **sub-redes,** confirme se este NSG é aplicado à sub-rede de cluster.

* Se o cluster usar um [UDR (User-Defined Routes, rotas definidas pelo usuário)](../../virtual-network/virtual-networks-udr-overview.md).

    Vá para o portal Azure e identifique a tabela de rotas associada à sub-rede onde o cluster está sendo implantado. Depois de encontrar a tabela de rotas para a sub-rede, inspecione a seção **de rotas** nela.

    Se houver rotas definidas, certifique-se de que existem rotas para endereços IP para a região onde o cluster foi implantado e o **NextHopType** para cada rota é **a Internet**. Deve haver uma rota definida para cada endereço IP necessário documentado no artigo acima mencionado.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A configuração da rede virtual não é compatível com o requisito do HDInsight"

### <a name="issue"></a>Problema

As descrições de erro contêm mensagens semelhantes às seguintes:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Causa

Provavelmente um problema com a configuração personalizada do DNS.

### <a name="resolution"></a>Resolução

Valide que 168.63.129.16 está na cadeia de DNS personalizada. Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos do Azure a fim de resolver nomes de host dentro da rede virtual. Para obter mais informações, consulte [Resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16.

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Execute o comando a seguir:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Você deverá ver algo assim:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Com base no resultado - escolha uma das seguintes etapas a seguir:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 não está nesta lista

**Opção 1**  
Adicione 168.63.129.16 como o primeiro DNS personalizado para a rede virtual usando as etapas descritas no [Plano uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Essas etapas são aplicáveis apenas se o servidor DNS personalizado for executado no Linux.

**Opção 2**  
Implante um VM de servidor DNS para a rede virtual. Isso envolve as seguintes etapas:

* Crie uma VM na rede virtual, que será configurada como renador de DNS (pode ser um Linux ou Windows VM).
* Configure as regras de encaminhamento de DNS nesta VM (encaminhe todas as solicitações de resolução de nomes iDNS para 168.63.129.16 e o restante para o servidor DNS).
* Adicione o endereço IP desta VM como primeira entrada DNS para configuração de DNS de rede virtual.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 está na lista

Neste caso, crie um caso de suporte com o HDInsight, e investigaremos seu problema. Inclua o resultado dos comandos abaixo no seu caso de suporte. Isso nos ajudará a investigar e resolver o problema mais rapidamente.

A partir de uma sessão ssh no nó da cabeça, edite e execute o seguinte:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
