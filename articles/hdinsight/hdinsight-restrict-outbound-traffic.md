---
title: Configurar restrição de tráfego de rede de saída - Azure HDInsight
description: Saiba como configurar a restrição de tráfego de rede de saída para clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370178"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configure o tráfego de rede de saída para clusters Azure HDInsight usando firewall

Este artigo fornece as etapas para você proteger o tráfego de saída do seu cluster HDInsight usando o Firewall Azure. As etapas abaixo supõem que você está configurando um Firewall Azure para um cluster existente. Se você estiver implantando um novo cluster e atrás de um firewall, crie primeiro o cluster e a sub-rede HDInsight e siga os passos deste guia.

## <a name="background"></a>Segundo plano

Os clusters Azure HDInsight normalmente são implantados em sua própria rede virtual. O cluster tem dependências de serviços fora dessa rede virtual que requerem acesso à rede para funcionar corretamente.

Existem várias dependências que requerem tráfego de entrada. O tráfego de gerenciamento de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados [aqui.](hdinsight-management-ip-addresses.md) Você também pode criar regras do Grupo de Segurança de Rede (NSG) com essas informações para proteger o tráfego de entrada para os clusters.

As dependências de tráfego de saída do HDInsight são quase inteiramente definidas com FQDNs, que não têm endereços IP estáticos atrás deles. A falta de endereços estáticos significa que os NSGs (Network Security Groups, grupos de segurança de rede) não podem ser usados para bloquear o tráfego de saída de um cluster. Os endereços mudam com frequência o suficiente para que não se possa configurar regras com base na resolução atual do nome e usá-lo para configurar as regras do NSG.

A solução para proteger endereços de saída é usar um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Azure Firewall pode restringir o tráfego HTTP e HTTPS de saída com base no FQDN das tags destino ou [FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configuração do Firewall Azure com HDInsight

Um resumo das etapas para bloquear o egresso do HDInsight existente com o Azure Firewall são:

1. Crie uma sub-rede.
1. Crie um firewall.
1. Adicionar regras de aplicativos ao firewall
1. Adicione regras de rede ao firewall.
1. Crie uma tabela de roteamento.

### <a name="create-new-subnet"></a>Criar nova sub-rede

Crie uma sub-rede chamada **AzureFirewallSubnet** na rede virtual onde seu cluster existe.

### <a name="create-a-new-firewall-for-your-cluster"></a>Crie um novo firewall para o seu cluster

Crie um firewall chamado **Test-FW01** usando as etapas em **Implantar o firewall** do [Tutorial: Implante e configure o Firewall Azure usando o portal Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configure o firewall com regras de aplicação

Crie uma coleção de regras de aplicativo que permita que o cluster envie e receba comunicações importantes.

1. Selecione o novo **firewall Test-FW01** no portal Azure.

1. Navegue até **as regras de configuração** > **Rules** > **A coleta de** > regras do aplicativo **+ Adicione a coleta de regras do aplicativo**.

    ![Título: Adicionar a coleção de regras do aplicativo](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Na tela **de coleta de regras do aplicativo Adicionar,** forneça as seguintes informações:

    **Seção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwAppRule|
    |Prioridade|200|
    |Ação|Allow|

    **Seção de tags FQDN**

    | Nome | Endereço de origem | Marca FQDN | Observações |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Necessário para serviços de HDI |

    **Seção 'Segment FQDNs'**

    | Nome | Endereços de origem | Protocolo:Porta | Alvo FQDNS | Observações |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Permite a atividade de login do Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Permite a atividade de login do Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Substitua pelo `storage_account_name` nome da sua conta de armazenamento real. Se o seu cluster for apoiado pelo WASB, adicione uma regra para WASB. Para usar SOMENTE conexões https, certifique-se de que [a "transferência segura necessária"](../storage/common/storage-require-secure-transfer.md) esteja ativada na conta de armazenamento. |

   ![Título: Digite os detalhes da coleção de regras do aplicativo](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selecione **Adicionar**.

### <a name="configure-the-firewall-with-network-rules"></a>Configure o firewall com regras de rede

Crie as regras de rede para configurar corretamente seu cluster HDInsight.

1. Continuando da etapa anterior, navegue até a **coleta** > de regras da rede **+ Adicione a coleta de regras de rede**.

1. Na tela **de coleta de regras da rede Adicionar,** forneça as seguintes informações:

    **Seção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwNetRule|
    |Prioridade|200|
    |Ação|Allow|

    **Seção Endereços IP**

    | Nome | Protocolo | Endereços de origem | Endereços de destino | Portas de destino | Observações |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Serviço de data/hora |
    | Rule_2 | Qualquer | * | DC_IP_Address_1 DC_IP_Address_2 | * | Se você estiver usando o Enterprise Security Package (ESP), adicione uma regra de rede na seção Endereços IP que permite a comunicação com o AAD-DS para clusters ESP. Você pode encontrar os endereços IP dos controladores de domínio na seção AAD-DS no portal |
    | Rule_3 | TCP | * | Endereço IP da sua conta de armazenamento do Data Lake | * | Se você estiver usando o Azure Data Lake Storage, então você pode adicionar uma regra de rede na seção Endereços IP para resolver um problema de SNI com ODLS Gen1 e Gen2. Essa opção irá direcionar o tráfego para firewall, o que pode resultar em custos mais altos para grandes cargas de dados, mas o tráfego será registrado e auditável em logs de firewall. Determine o endereço IP da sua conta data lake storage. Você pode usar um comando `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` powershell, como para resolver o FQDN em um endereço IP.|
    | Rule_4 | TCP | * | * | 12000 | (Opcional) Se você estiver usando o Log Analytics, crie uma regra de rede na seção Endereços IP para permitir a comunicação com o espaço de trabalho do Log Analytics. |

    **Seção Tags de serviço**

    | Nome | Protocolo | Endereços de Origem | Marcas de serviço | Portos de Destino | Observações |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Configure uma regra de rede na seção Tags de serviço para SQL que permitirá que você registre e audite o tráfego SQL, a menos que você configure pontos finais de serviço para sql server na sub-rede HDInsight, que contornará o firewall. |

   ![Título: Digite a coleção de regras do aplicativo](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selecione **Adicionar**.

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Crie uma tabela de rotas com as seguintes entradas:

* Todos os endereços IP dos serviços de [saúde e gerenciamento: Todas as regiões](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) com um próximo tipo de **internet**hop .

* Dois endereços IP para a região onde o cluster é criado a partir de serviços de [saúde e gerenciamento: regiões específicas](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) com um próximo tipo de **internet**hop .

* Uma rota de dispositivo virtual para endereço IP 0.0.0.0/0 com o próximo salto sendo o endereço IP privado do Firewall Do Azure.

Por exemplo, para configurar a tabela de rota para um cluster criado na região dos EUA de "East US", use as seguintes etapas:

1. Selecione o seu firewall Azure **Test-FW01**. Copie o **endereço IP privado** listado na página Visão **Geral.** Para este exemplo, usaremos um **endereço amostral de 10.0.2.4**.

1. Em seguida, navegue para **todas as tabelas de** > rota de**rede de** > **serviços** e **crie tabela de rotas**.

1. A partir de sua nova rota, navegue até **Configurações** > **Rotas** > **+ Adicionar**. Adicione as seguintes rotas:

| Nome da rota | Prefixo de endereço | Tipo do próximo salto | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Solução de virtualização | 10.0.2.4 |

Completar a configuração da tabela de rotas:

1. Atribua a tabela de rota criada à sua sub-rede HDInsight selecionando **Sub-redes** em **Configurações**.

1. Selecione **+ Associado**.

1. Na tela **da sub-rede Associate,** selecione a rede virtual em que seu cluster foi criado e a **sub-rede** que você usou para o cluster HDInsight.

1. Selecione **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Tráfego de aplicativos com nó de borda ou personalizado

As etapas acima permitirão que o cluster opere sem problemas. Você ainda precisa configurar dependências para acomodar seus aplicativos personalizados em execução nos nós de borda, se aplicável.

As dependências dos aplicativos devem ser identificadas e adicionadas ao Firewall do Azure ou à tabela de rotas.

As rotas devem ser criadas para o tráfego de aplicativos para evitar problemas de roteamento assimétricos.

Se seus aplicativos tiverem outras dependências, eles precisam ser adicionados ao seu Firewall Do Azure. Crie regras de aplicativo para permitir o tráfego HTTP/HTTPS e regras de Rede para todo o resto.

## <a name="logging-and-scale"></a>Registro e escala

O Azure Firewall pode enviar logs para alguns sistemas de armazenamento diferentes. Para obter instruções sobre como configurar o registro para o firewall, siga as etapas do [Tutorial: Monitore os logs e métricas do Firewall do Azure](../firewall/tutorial-diagnostics.md).

Depois de concluir a configuração de registro, se estiver registrando dados no Log Analytics, poderá visualizar o tráfego bloqueado com uma consulta, como a seguinte:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrar o Firewall do Azure com os logs do Azure Monitor é útil quando um aplicativo funciona pela primeira vez quando você não está ciente de todas as dependências do aplicativo. Saiba mais sobre os logs do Azure Monitor em [Analisar dados de log no Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Para saber sobre os limites de escala do Firewall Azure e solicitar aumentos, consulte [este](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento ou consulte as [PERGUNTAS FREQUENTES](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Acesso ao cluster

Depois de ter o firewall configurado com sucesso, você pode usar o ponto final interno (`https://CLUSTERNAME-int.azurehdinsight.net`) para acessar o Ambari de dentro da rede virtual.

Para usar o ponto`https://CLUSTERNAME.azurehdinsight.net`final público ( )`CLUSTERNAME-ssh.azurehdinsight.net`ou ssh endpoint (), certifique-se de ter as rotas certas na tabela de rotas e regras do NSG para evitar o problema de roteamento assimétrico explicado [aqui](../firewall/integrate-lb.md). Especificamente neste caso, você precisa permitir que o endereço IP do cliente nas regras do NSG de entrada `internet`e também adicioná-lo à tabela de rota definida pelo usuário com o próximo conjunto de saltos como . Se isso não estiver configurado corretamente, você verá um erro de tempo.

## <a name="configure-another-network-virtual-appliance"></a>Configure outro aparelho virtual de rede

> [!Important]
> As informações a seguir **só** são necessárias se você desejar configurar um dispositivo virtual de rede (NVA) diferente do Firewall Azure.

As instruções anteriores ajudam a configurar o Firewall Azure para restringir o tráfego de saída do seu cluster HDInsight. O Azure Firewall é configurado automaticamente para permitir o tráfego para muitos dos cenários importantes comuns. Se você quiser usar outro aparelho virtual de rede, você precisará configurar manualmente uma série de recursos adicionais. Tenha em mente o seguinte ao configurar seu aparelho virtual de rede:

* Os serviços compatíveis com o Ponto de Extremidade de Serviço devem ser configurados com pontos de extremidade de serviço.
* As dependências de endereço IP são para tráfego não-HTTP/S (tráfego TCP e UDP).
* Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo NVA.
* Os pontos finais do Wildcard HTTP/HTTPS são dependências que podem variar de acordo com uma série de qualificações.
* Atribua a tabela de rota que você cria à sua sub-rede HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Dependências capazes de ponto final de serviço

| **Ponto de extremidade** |
|---|
| SQL do Azure |
| Armazenamento do Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| **Ponto de extremidade** | **Detalhes** |
|---|---|
| \*:123 | Verificação do relógio do NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| IPs publicados [aqui](hdinsight-management-ip-addresses.md) | Estes são o serviço HDInsight |
| IPs privados AAD-DS para clusters ESP |
| \*:16800 para ativação do Windows KMS |
| \*12000 para Análise de Log |

#### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS do FQDN

> [!Important]
> A lista abaixo só dá alguns dos FQDNs mais importantes. Você pode obter a lista completa de FQDNs para configurar seu NVA [neste arquivo](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Ponto de extremidade**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Próximas etapas

* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
