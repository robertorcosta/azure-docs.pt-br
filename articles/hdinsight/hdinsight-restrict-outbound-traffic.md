---
title: Configurar a restrição de tráfego de rede de saída – Azure HDInsight
description: Saiba como configurar a restrição de tráfego de rede de saída para clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: f87c3665f558b3185e95b0ad0aa18a883439a221
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006510"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configurar o tráfego de rede de saída para clusters do Azure HDInsight usando o firewall

Este artigo fornece as etapas para proteger o tráfego de saída do seu cluster do HDInsight usando o Firewall do Azure. As etapas a seguir pressupõem que você está configurando um Firewall do Azure para um cluster existente. Se você estiver implantando um novo cluster atrás de um firewall, primeiro crie seu cluster e sub-rede do HDInsight. Depois, execute as etapas deste guia.

## <a name="background"></a>Segundo plano

Os clusters do HDInsight normalmente são implantados em uma rede virtual. O cluster tem dependências em serviços fora dessa rede virtual.

Há várias dependências que exigem tráfego de entrada. O tráfego de gerenciamento de entrada não pode ser enviado por meio de um dispositivo de firewall. Os endereços de origem para esse tráfego são conhecidos e publicados [aqui](hdinsight-management-ip-addresses.md). Você também pode criar regras de NSG (Grupo de Segurança de Rede) com essas informações para proteger o tráfego de entrada para os clusters.

As dependências de tráfego de saída do HDInsight são quase totalmente definidas com FQDNs. E elas não têm endereços IP estáticos por trás. A falta de endereços estáticos significa que os NSGs (Grupos de Segurança de Rede) não podem ser usados para bloquear o tráfego de saída de um cluster. Os endereços mudam com frequência suficiente para que não seja possível configurar regras com base na resolução de nomes e uso.

Proteja os endereços de saída com um firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Firewall do Azure restringe o tráfego de saída com base no FQDN de destino ou nas [marcas de FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Como configurar o Firewall do Azure com o HDInsight

Um resumo das etapas para bloquear a saída do HDInsight existente com o Firewall do Azure:

1. Crie uma sub-rede.
1. Crie um firewall.
1. Adicione regras de aplicativo ao firewall
1. Adicione regras de rede ao firewall.
1. Crie uma tabela de roteamento.

### <a name="create-new-subnet"></a>Criar uma sub-rede

Crie uma sub-rede chamada **AzureFirewallSubnet** na rede virtual em que o cluster existe.

### <a name="create-a-new-firewall-for-your-cluster"></a>Criar um firewall para o cluster

Crie um firewall chamado **Test-FW01** usando as etapas em **Implantar o firewall** em [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configurar o firewall com regras de aplicativo

Crie uma coleção de regras de aplicativo que permita que o cluster envie e receba comunicações importantes.

1. Selecione o novo firewall **Test-FW01** do portal do Azure.

1. Navegue até **Configurações** > **Regras** > **Coleção de regras de aplicativos** >  **+ Adicionar coleção de regras de aplicativos**.

    ![Título: Adicionar coleção de regras de aplicativos](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Na tela **Adicionar coleção de regras de aplicativos**, forneça as seguintes informações:

    **Seção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwAppRule|
    |Prioridade|200|
    |Ação|Allow|

    **Seção de marcas de FQDN**

    | Nome | Endereço de origem | Marca FQDN | Observações |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Exigido para serviços HDIs |

    **Seção de FQDNs de destino**

    | Nome | Endereços de origem | Protocolo: porta | FQDNs de destino | Observações |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Permite a atividade de logon do Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Permite a atividade de logon do Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Substitua `storage_account_name` pelo nome da conta de armazenamento real. Se o seu cluster tiver o suporte de WASB, adicione uma regra para WASB. Para usar APENAS conexões HTTPS, verifique se ["transferência segura necessária"](../storage/common/storage-require-secure-transfer.md) está habilitada na conta de armazenamento. |

   ![Título: Entrar nos detalhes da coleção de regras de aplicativo](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selecione **Adicionar**.

### <a name="configure-the-firewall-with-network-rules"></a>Configurar o firewall com regras de rede

Crie as regras de rede para configurar corretamente o cluster do HDInsight.

1. Continuando da etapa anterior, navegue até **Coleção de regras de rede** >  **+ Adicionar coleção de regras de rede**.

1. Na tela **Adicionar coleção de regras de rede**, forneça as seguintes informações:

    **Seção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwNetRule|
    |Prioridade|200|
    |Ação|Allow|

    **Seção de endereços IP**

    | Nome | Protocolo | Endereços de origem | Endereços de destino | Portas de destino | Observações |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Serviço de tempo |
    | Rule_2 | Qualquer | * | DC_IP_Address_1, DC_IP_Address_2 | * | Se você estiver usando o EPS (Enterprise Security Package), adicione uma regra de rede na seção Endereços IP que permite a comunicação com o AAD-DS para clusters ESP. Você pode encontrar os endereços IP dos controladores de domínio na seção AAD-DS no portal |
    | Rule_3 | TCP | * | Endereço IP da sua conta do Data Lake Storage | * | Se você estiver usando o Azure Data Lake Storage, poderá adicionar uma regra de rede na seção Endereços IP para resolver um problema de SNI com ADLS Gen1 e Gen2. Esta opção roteará o tráfego para o firewall. Isso pode resultar em custos mais altos para cargas de dados grandes, mas o tráfego será registrado e auditável nos logs de firewall. Determine o endereço IP para sua conta do Data Lake Storage. Você pode usar um comando do PowerShell como `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` para resolver o FQDN para um endereço IP.|
    | Rule_4 | TCP | * | * | 12000 | (Opcional) Se você estiver usando o Log Analytics, crie uma regra de rede na seção Endereços IP para habilitar a comunicação com seu workspace do Log Analytics. |

    **Seção de marcas de serviço**

    | Nome | Protocolo | Endereços de Origem | Marcas de serviço | Portas de destino | Observações |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Configure uma regra de rede na seção Marcas de Serviço para SQL que permitirá que você registre e audite o tráfego do SQL. A menos que você tenha configurado pontos de extremidade de serviço para SQL Server na sub-rede do HDInsight, que ignorará o firewall. |
    | Rule_8 | TCP | * | Azure Monitor | * | (Opcional) Os clientes que planejam usar o recurso de dimensionamento automático devem adicionar essa regra. |
    
   ![Título: Inserir coleção de regras de aplicativos](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selecione **Adicionar**.

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Crie uma tabela de rotas com as seguintes entradas:

* Todos os endereços IP de [Serviços de integridade e gerenciamento: todas as regiões](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) com um tipo do próximo salto de **Internet**.

* Dois endereços IP para a região em que o cluster é criado de [Serviços de integridade e gerenciamento: regiões específicas](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) com um tipo do próximo salto de **Internet**.

* Uma rota da Solução de Virtualização para o endereço IP 0.0.0.0/0 com o próximo salto sendo seu endereço IP privado do Firewall do Azure.

Por exemplo, para configurar a tabela de rotas para um cluster criado na região dos EUA "Leste dos EUA", use as seguintes etapas:

1. Selecione o firewall **Test-FW01** do Azure. Copie o **endereço IP privado** listado na página **Visão geral**. Para este exemplo, usaremos um **endereço de exemplo de 10.0.2.4**.

1. Em seguida, navegue até **Todos os serviços** > **Rede** > **Tabelas de rede** e **Criar Tabela de Rotas**.

1. Em sua nova rota, navegue até **Configurações** > **Rotas** >  **+ Adicionar**. Adicione as seguintes rotas:

| Nome da rota | Prefixo de endereço | Tipo do próximo salto | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Solução de virtualização | 10.0.2.4 |

Conclua a configuração da tabela de rotas:

1. Atribua a tabela de rotas que você criou à sub-rede do HDInsight selecionando **Sub-redes** em **Configurações**.

1. Selecione **+ Associar**.

1. Na tela **Associar sub-rede**, selecione a rede virtual na qual o cluster foi criado. E a **Sub-rede** usada para o cluster do HDInsight.

1. Selecione **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Nó de borda ou tráfego de aplicativo personalizado

As etapas acima permitirão que o cluster opere sem problemas. Você ainda precisa configurar dependências para acomodar seus aplicativos personalizados em execução nos nós de borda, se aplicável.

As dependências de aplicativo precisam ser identificadas e adicionadas ao Firewall do Azure ou à tabela de rotas.

As rotas precisam ser criadas para o tráfego de aplicativo a fim de evitar problemas de roteamento assimétrico.

Se os seus aplicativos tiverem outras dependências, eles precisarão ser adicionados ao Firewall do Azure. Crie regras de aplicativo para permitir o tráfego HTTP/HTTPS e regras de Rede para todo o resto.

## <a name="logging-and-scale"></a>Registrar em log e escalar

O Firewall do Azure pode enviar logs para alguns sistemas de armazenamento diferentes. Para obter instruções sobre como configurar o registro em log para o firewall, siga as etapas em [Tutorial: monitorar os logs e as métricas do Firewall do Azure](../firewall/tutorial-diagnostics.md).

Depois de concluir a configuração do registro em log, se você estiver usando Log Analytics, poderá exibir o tráfego bloqueado com uma consulta, como:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

A integração do Firewall do Azure aos logs do Azure Monitor é útil ao colocar um aplicativo para ser executado pela primeira vez. Especialmente quando você não está ciente de todas as dependências do aplicativo. Saiba mais sobre os logs do Azure Monitor em [Analisar dados de log no Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Para saber mais sobre os limites de escala do Firewall do Azure e solicitar aumentos, confira [este](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento ou consulte as [Perguntas frequentes](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Acessar o cluster

Depois de configurar o firewall com êxito, você poderá usar o ponto de extremidade interno (`https://CLUSTERNAME-int.azurehdinsight.net`) para acessar o Ambari de dentro da rede virtual.

Para usar o ponto de extremidade público (`https://CLUSTERNAME.azurehdinsight.net`) ou o ponto de extremidade SSH (`CLUSTERNAME-ssh.azurehdinsight.net`), verifique se você tem as rotas certas na tabela de rotas e as regras de NSG para evitar o problema de roteamento assimétrico explicado [aqui](../firewall/integrate-lb.md). Especificamente nesse caso, você precisa permitir o endereço IP do cliente nas regras de NSG de entrada e também adicioná-lo à tabela de rotas definida pelo usuário com o próximo salto definido como `internet`. Se o roteamento não estiver configurado corretamente, você verá um erro de tempo limite.

## <a name="next-steps"></a>Próximas etapas

* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurar solução de virtualização de rede](./network-virtual-appliance.md)
