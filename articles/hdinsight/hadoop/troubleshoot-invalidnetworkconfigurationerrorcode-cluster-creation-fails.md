---
title: Erro InvalidNetworkConfigurationErrorCode-Azure HDInsight
description: Vários motivos para as criações de cluster com falha com o InvalidNetworkConfigurationErrorCode no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720377"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Falha na criação do cluster com InvalidNetworkConfigurationErrorCode no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

Se você vir o código de erro `InvalidNetworkConfigurationErrorCode` com a descrição "a configuração de rede virtual não é compatível com o requisito do HDInsight", isso geralmente indica um problema com a [configuração de rede virtual](../hdinsight-plan-virtual-network-deployment.md) para o cluster. Com base no restante da descrição do erro, siga as seções abaixo para resolver o problema.

## <a name="hostname-resolution-failed"></a>"Falha na resolução do nome do host"

### <a name="issue"></a>Problema

A descrição do erro contém "falha na resolução do nome do host".

### <a name="cause"></a>Causa

Esse erro aponta para um problema com a configuração de DNS personalizada. Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para resolvedores recursivos do Azure para resolver nomes de host dentro dessa rede virtual (consulte [resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para obter detalhes). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16. Esse IP só pode ser acessado por meio das VMs do Azure. Portanto, ele não funcionará se você estiver usando um servidor DNS local ou o servidor DNS for uma VM do Azure, que não faz parte da rede virtual do cluster.

### <a name="resolution"></a>Resolução

1. Ssh na VM que faz parte do cluster e execute o comando `hostname -f`. Isso retornará o nome de domínio totalmente qualificado do host (conhecido como `<host_fqdn>` nas instruções abaixo).

1. Em seguida, execute o comando `nslookup <host_fqdn>` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Se esse comando resolver o nome para um endereço IP, significa que o servidor DNS está funcionando corretamente. Nesse caso, gere um caso de suporte com o HDInsight e investigaremos seu problema. Em seu caso de suporte, inclua as etapas de solução de problemas que você executou. Isso nos ajudará a resolver o problema mais rapidamente.

1. Se o comando acima não retornar um endereço IP, execute `nslookup <host_fqdn> 168.63.129.16` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Se esse comando for capaz de resolver o IP, significa que o servidor DNS não está encaminhando a consulta para o DNS do Azure ou não é uma VM que faça parte da mesma rede virtual que o cluster.

1. Se você não tiver uma VM do Azure que possa atuar como um servidor DNS personalizado na rede virtual do cluster, será necessário adicionar isso primeiro. Crie uma VM na rede virtual, que será configurada como encaminhador DNS.

1. Depois de ter uma VM implantada em sua rede virtual, configure as regras de encaminhamento de DNS nessa VM. Encaminhe todas as solicitações de resolução de nomes de iDNS para 168.63.129.16 e o restante para o servidor DNS. [Aqui](../hdinsight-plan-virtual-network-deployment.md) está um exemplo dessa configuração para um servidor DNS personalizado.

1. Adicione o endereço IP dessa VM como a primeira entrada DNS para a configuração de DNS da rede virtual.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Falha ao conectar-se à conta de armazenamento do Azure"

### <a name="issue"></a>Problema

A descrição do erro contém "falha ao se conectar à conta de armazenamento do Azure" ou "falha ao conectar-se ao SQL do Azure".

### <a name="cause"></a>Causa

O armazenamento do Azure e o SQL não têm endereços IP fixos, portanto, precisamos permitir conexões de saída para todos os IPs para permitir o acesso a esses serviços. As etapas exatas de resolução dependem se você configurou um NSG (grupo de segurança de rede) ou UDR (regras definidas pelo usuário). Consulte a seção sobre como [controlar o tráfego de rede com o HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) para obter detalhes sobre essas configurações.

### <a name="resolution"></a>Resolução

* Se o cluster usar um [NSG (grupo de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Vá para a portal do Azure e identifique o NSG associado à sub-rede em que o cluster está sendo implantado. Na seção **regras de segurança de saída** , permita o acesso de saída à Internet sem limitação (Observe que um número de **prioridade** menor aqui significa prioridade mais alta). Além disso, na seção **sub-redes** , confirme se esse NSG é aplicado à sub-rede do cluster.

* Se o cluster usar uma [UDR (rotas definidas pelo usuário)](../../virtual-network/virtual-networks-udr-overview.md).

    Vá para a portal do Azure e identifique a tabela de rotas associada à sub-rede em que o cluster está sendo implantado. Depois de encontrar a tabela de rotas para a sub-rede, inspecione a seção **rotas** nela.

    Se houver rotas definidas, verifique se há rotas para endereços IP para a região em que o cluster foi implantado e o **NextHopType** para cada rota é **Internet**. Deve haver uma rota definida para cada endereço IP necessário documentado no artigo mencionado anteriormente.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A configuração de rede virtual não é compatível com o requisito do HDInsight"

### <a name="issue"></a>Problema

As descrições de erro contêm mensagens semelhantes da seguinte maneira:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Causa

Provavelmente um problema com a configuração de DNS personalizada.

### <a name="resolution"></a>Resolução

Valide se 168.63.129.16 está na cadeia DNS personalizada. Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos do Azure a fim de resolver nomes de host dentro da rede virtual. Para obter mais informações, consulte [resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

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

    Com base no resultado-escolha uma das seguintes etapas a seguir:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 não está nesta lista

**Opção 1**  
Adicione 168.63.129.16 como o primeiro DNS personalizado para a rede virtual usando as etapas descritas em [planejar uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Essas etapas serão aplicáveis somente se o servidor DNS personalizado for executado no Linux.

**Opção 2**  
Implante uma VM do servidor DNS para a rede virtual. Isso envolve as seguintes etapas:

* Crie uma VM na rede virtual, que será configurada como encaminhador DNS (pode ser uma VM Linux ou Windows).
* Configure as regras de encaminhamento de DNS nessa VM (encaminhe todas as solicitações de resolução de nomes de iDNS para 168.63.129.16 e o restante para o servidor DNS).
* Adicione o endereço IP dessa VM como primeira entrada DNS para a configuração de DNS da rede virtual.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 está na lista

Nesse caso, crie um caso de suporte com o HDInsight e nós investigaremos seu problema. Inclua o resultado dos comandos abaixo em seu caso de suporte. Isso nos ajudará a investigar e resolver o problema mais rapidamente.

Em uma sessão SSH no nó principal, edite e, em seguida, execute o seguinte:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
