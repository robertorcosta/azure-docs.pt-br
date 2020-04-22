---
title: Use o ID Broker (visualização) para gerenciamento de credenciais- Azure HDInsight
description: Saiba mais sobre o HDInsight ID Broker para simplificar a autenticação para clusters Apache Hadoop unidos por domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1e7eaf49fb8b62259b8c619c89edffd629dfde7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685504"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Use id broker (visualização) para gerenciamento de credenciais

Este artigo descreve como configurar e usar o recurso ID Broker no Azure HDInsight. Você pode usar esse recurso para fazer login no Apache Ambari através da Autenticação Multifatorial do Azure e obter os bilhetes Kerberos necessários sem precisar de hashes de senha no Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Visão geral

O ID Broker simplifica configurações complexas de autenticação nos seguintes cenários:

* Sua organização conta com a federação para autenticar usuários para acessar recursos na nuvem. Anteriormente, para usar os clusters do HDInsight Enterprise Security Package (ESP), você tinha que habilitar a sincronização de hash de senha do ambiente local para o Azure Active Directory. Essa exigência pode ser difícil ou indesejável para algumas organizações.

* Você está construindo soluções que usam tecnologias que dependem de diferentes mecanismos de autenticação. Por exemplo, Apache Hadoop e Apache Ranger dependem de Kerberos, enquanto o Azure Data Lake Storage conta com OAuth.

O ID Broker fornece uma infra-estrutura de autenticação unificada e remove a exigência de sincronização de hashes de senha para o Azure AD DS. O ID Broker consiste em componentes em execução em um Windows Server VM (ID Broker node), juntamente com os nós de gateway do cluster. 

O diagrama a seguir mostra o fluxo de autenticação para todos os usuários, incluindo usuários federados, depois que o ID Broker estiver ativado:

![Fluxo de autenticação com ID Broker](./media/identity-broker/identity-broker-architecture.png)

O ID Broker permite que você faça login em clusters ESP usando autenticação multifatorial, sem fornecer senhas. Se você já fez login em outros serviços do Azure, como o portal Azure, você pode fazer login no seu cluster HDInsight com uma única experiência de login (SSO).

## <a name="enable-hdinsight-id-broker"></a>Habilitar o HDInsight ID Broker

Para criar um cluster ESP com o ID Broker ativado, tome as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Siga as etapas básicas de criação de um cluster ESP. Para obter mais informações, consulte [Criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selecione **Habilitar hdinsight ID Broker**.

O recurso ID Broker adicionará uma VM extra ao cluster. Este VM é o nó ID Broker e inclui componentes do servidor para suportar a autenticação. O nó ID Broker é domínio unido ao domínio Azure AD DS.

![Opção para ativar o ID Broker](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure
Se você adicionar uma `idbrokernode` nova função chamada com os seguintes atributos ao perfil de computação do seu modelo, o cluster será criado com o nó de corretor de ID ativado:

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

O plug-in HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) é atualizado para suportar o OAuth. Você pode usar este plug-in para se conectar ao cluster e enviar trabalhos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso ssh sem um hash de senha no Azure AD DS

Depois que o ID Broker estiver ativado, você ainda precisará de um hash de senha armazenado no Azure AD DS para cenários SSH com contas de domínio. Para SSH para uma VM com domínio `kinit` ou para executar o comando, você precisa fornecer uma senha. 

A autenticação SSH requer que o hash esteja disponível no Azure AD DS. Se você quiser usar o SSH apenas para cenários administrativos, você pode criar uma conta somente em nuvem e usá-la para SSH para o cluster. Outros usuários ainda podem usar ferramentas Ambari ou HDInsight (como o plug-in da IntelliJ) sem ter o hash de senha disponível no Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Clientes que usam o OAuth para se conectar ao gateway HDInsight com a configuração do ID Broker

Na configuração da corretora de ID, aplicativos personalizados e clientes que se conectam ao gateway podem ser atualizados para adquirir o token OAuth necessário primeiro. Você pode seguir as etapas deste [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) para adquirir o token com as seguintes informações:

*   OAuth recurso uri:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Permissão: (nome: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Próximas etapas

* [Configure um cluster HDInsight com o Enterprise Security Package usando os serviços de domínio do Azure Active Directory](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar usuários do Azure Active Directory para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)
