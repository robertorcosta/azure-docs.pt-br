---
title: Usar o agente de ID (versão prévia) para o gerenciamento de credenciais-Azure HDInsight
description: Saiba mais sobre o agente de ID do HDInsight para simplificar a autenticação para clusters de Apache Hadoop ingressados no domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 12d98406b21ed9a3ea27f9aa4abc0db6f536468d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251908"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Agente de ID do Azure HDInsight (versão prévia)

Este artigo descreve como configurar e usar o recurso HIB (agente de ID do HDInsight) no Azure HDInsight. Você pode usar esse recurso para obter a autenticação OAuth moderna para o Apache Ambari enquanto tem imposição de MFA (autenticação multifator) sem precisar de hashes de senha herdados no Azure Active Directory Domain Services (AAD-DS).

## <a name="overview"></a>Visão geral

O HIB simplifica as configurações de autenticação complexas nos seguintes cenários:

* Sua organização depende da Federação para autenticar usuários para acessar recursos de nuvem. Anteriormente, para usar clusters do Enterprise Security Package do HDInsight (ESP), era necessário habilitar a sincronização de hash de senha do seu ambiente local para Azure Active Directory (Azure AD). Esse requisito pode ser difícil ou indesejável para algumas organizações.

* Sua organização gostaria de impor a MFA para acesso baseado em Web/HTTP ao Apache Ambari e outros recursos de cluster.

O HIB fornece a infraestrutura de autenticação que permite a transição de protocolo do OAuth (moderno) para o Kerberos (Herdado) sem a necessidade de sincronizar hashes de senha para o AAD-DS. Essa infraestrutura consiste em componentes em execução em uma VM do Windows Server (nó do agente de ID), juntamente com nós de gateway de cluster.

O diagrama a seguir mostra o fluxo de autenticação moderno baseado em OAuth para todos os usuários, incluindo usuários federados, depois que o agente de ID está habilitado:

![Fluxo de autenticação com o agente de ID](./media/identity-broker/identity-broker-architecture.png)

Neste diagrama, o cliente (ou seja, navegador ou aplicativos) precisa adquirir o token OAuth primeiro e, em seguida, apresentar o token ao gateway em uma solicitação HTTP. Se você já tiver entrado em outros serviços do Azure, como o portal do Azure, você pode entrar em seu cluster HDInsight com uma experiência de logon único (SSO).

Ainda pode haver muitos aplicativos herdados que dão suporte apenas à autenticação básica (ou seja, nome de usuário/senha). Para esses cenários, você ainda pode usar a autenticação básica HTTP para se conectar aos gateways de cluster. Nessa configuração, você deve garantir a conectividade de rede dos nós de gateway para o ponto de extremidade de Federação (ponto de extremidade do ADFS) para garantir uma linha de visão direta dos nós do gateway.

Use a tabela a seguir para determinar a melhor opção de autenticação com base em suas necessidades de organização:

|Opções de autenticação |Configuração do HDInsight | Fatores a serem considerados |
|---|---|---|
| OAuth totalmente | ESP + HIB | 1. opção mais segura (MFA é suportada) 2.    Não é necessário passar a sincronização de hash. 3.  Nenhum acesso SSH/kinit/keytab para contas locais, que não têm hash de senha no AAD-DS. 4.   Contas somente em nuvem ainda podem SSH/kinit/keytab. 5. Acesso baseado na Web a Ambari por meio do OAuth 6.  Requer a atualização de aplicativos herdados (JDBC/ODBC, etc.) para dar suporte ao OAuth.|
| OAuth + autenticação básica | ESP + HIB | 1. acesso baseado na Web a Ambari por meio do OAuth 2. Os aplicativos herdados continuam a usar a autenticação básica. 3. A MFA deve ser desabilitada para acesso básico à autenticação. 4. Não é necessário passar a sincronização de hash. 5. Nenhum acesso SSH/kinit/keytab para contas locais, que não têm hash de senha no AAD-DS. 6. Contas somente em nuvem ainda podem SSH/kinit. |
| Autenticação totalmente básica | ESP | 1. mais semelhante às configurações locais. 2. A sincronização de hash de senha para AAD-DS é necessária. 3. As contas locais podem SSH/kinit ou usar keytab. 4. A MFA deverá ser desabilitada se o armazenamento de backup estiver ADLS Gen2 |


## <a name="enable-hdinsight-id-broker"></a>Habilitar agente de ID do HDInsight

Para criar um cluster ESP com o agente de ID habilitado, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Siga as etapas de criação básicas para um cluster ESP. Para obter mais informações, consulte [criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selecione **habilitar agente de ID do HDInsight**.

O recurso do agente de ID adicionará uma VM extra ao cluster. Essa VM é o nó do agente de ID e inclui componentes de servidor para dar suporte à autenticação. O nó do agente de ID é ingressado no domínio do Azure AD DS domínio.

![Opção para habilitar o agente de ID](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Usar modelos do Azure Resource Manager
Se você adicionar uma nova função chamada `idbrokernode` com os seguintes atributos ao perfil de computação do modelo, o cluster será criado com o nó do agente de ID habilitado:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Integração de ferramentas

As ferramentas HDIsngith são atualizadas para dar suporte nativo ao OAuth. É altamente recomendável usar essas ferramentas para acesso moderno baseado em OAuth aos clusters. O [plug-in](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) do HDInsight IntelliJ pode ser usado para aplicativos baseados em Java, como escalabilidade. As [Ferramentas do Spark & Hive para vs Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) podem ser usadas de trabalhos do PySpark e do hive. Eles dão suporte a trabalhos em lotes e interativos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem um hash de senha no Azure AD DS

|Opções de SSH |Fatores a serem considerados |
|---|---|
| Conta VM local (por exemplo, sshuser) | 1. você forneceu essa conta no momento da criação do cluster. 2.  Não há nenhum authication Kerberos para esta conta |
| Conta somente em nuvem (por exemplo, alice@contoso.onmicrosoft.com ) | 1. o hash de senha está disponível no AAD-DS 2. A autenticação Kerberos é possível por meio do Kerberos SSH |
| Conta local (por exemplo, alice@contoso.com ) | 1. a autenticação Kerberos do SSH só será possível se o hash de senha estiver disponível no AAD-DS, caso contrário, esse usuário não poderá realizar o SSH no cluster |

Para realizar o SSH em uma VM ingressada no domínio ou para executar o `kinit` comando, você precisa fornecer uma senha. A autenticação Kerberos do SSH requer que o hash esteja disponível no AAD-DS. Se você quiser usar o SSH somente para cenários administrativos, poderá criar uma conta somente em nuvem e usá-la para SSH para o cluster. Outros usuários locais ainda podem usar as ferramentas Ambari ou HDInsight ou HTTP Basic auth sem ter o hash de senha disponível no AAD-DS.

Se sua organização não estiver sincronizando hashes de senha para o AAD-DS, como prática recomendada, crie um único usuário de nuvem no Azure AD e atribua-o como administrador de cluster ao criar o cluster e use-o para fins de administração que inclua obter acesso à raiz para as VMs via SSH.

Para solucionar problemas de autenticação, confira este [guia](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Clientes que usam o OAuth para se conectar ao gateway do HDInsight com HIB

Na configuração do HIB, os aplicativos personalizados e clientes que se conectam ao gateway podem ser atualizados para adquirir o token OAuth necessário primeiro. Você pode seguir as etapas neste [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) para adquirir o token com as seguintes informações:

*   URI do recurso OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Permissão: (nome: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Depois de adquirir o token OAuth, você pode usá-lo no cabeçalho Authorization da solicitação HTTP para o gateway de cluster (por exemplo, https:// <clustername> -int.azurehdinsight.net). Por exemplo, um exemplo de comando de ondulação para a API do Apache Livy pode ser assim:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Próximas etapas

* [Configurar um cluster HDInsight com Enterprise Security Package usando Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar usuários do Azure Active Directory para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)
