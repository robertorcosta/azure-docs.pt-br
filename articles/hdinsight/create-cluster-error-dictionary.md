---
title: Azure HDInsight Criar um cluster - dicionário de erros
description: Saiba como solucionar problemas que ocorrem ao criar clusters Azure HDInsight
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618894"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: erros de criação de cluster

Este artigo descreve resoluções para erros que você pode encontrar ao criar clusters.

> [!NOTE]
> Os três primeiros erros descritos neste artigo são erros de validação. Eles podem ocorrer quando um produto Azure HDInsight usa a classe **CsmDocument_2_0.**

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: ImplantaçãoDocumento 'CsmDocument_2_0' falhou na validação

### <a name="error"></a>Erro

"O local de ação de\<script\>não pode ser acessado URI: SCRIPT ACTION URL "

#### <a name="error-message"></a>Mensagem de erro

"O servidor remoto retornou um erro: (404) Não encontrado."

### <a name="cause"></a>Causa

O serviço HDInsight não pode acessar a URL de ação de script que você forneceu como parte da solicitação criar cluster. O serviço recebe a mensagem de erro anterior quando tenta acessar a ação do script.

### <a name="resolution"></a>Resolução

- Para obter uma URL HTTP ou HTTPS, verifique a URL tentando ir até ela a partir de uma janela de navegador anônima.
- Para uma URL WASB, certifique-se de que o script existe na conta de armazenamento que você dá na solicitação. Certifique-se também de que a chave de armazenamento desta conta de armazenamento esteja correta.
- Para uma URL ADLS, certifique-se de que o script existe na conta de armazenamento.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: ImplantaçãoDocumento 'CsmDocument_2_0' falhou na validação

### <a name="error"></a>Erro

"O local de ação de \<\>script não pode ser acessado URI: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Mensagem de erro

"O uri \<de\> script dado SCRIPT_URI está no ADLS, mas este cluster não tem o principal de armazenamento do lago de dados"

### <a name="cause"></a>Causa

O serviço HDInsight não pode acessar a URL de ação de script que você forneceu como parte da solicitação criar cluster. O serviço recebe a mensagem de erro anterior quando tenta acessar a ação do script.

### <a name="resolution"></a>Resolução

Adicione a conta correspondente do Azure Data Lake Storage Gen 1 ao cluster. Adicione também o principal de serviço que acessa a conta Data Lake Storage Gen 1 ao cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: ImplantaçãoDocumento 'CsmDocument_2_0' falhou na validação

### <a name="error"></a>Erro

"Tamanho VM\<\>' CUSTOMER_SPECIFIED_VM_SIZE ' desde que na solicitação seja\<inválido ou não suportado para o papel ' ROLE\>'. Os valores \<\>válidos são: VALID_VM_SIZE_FOR_ROLE ."

### <a name="cause"></a>Causa

O tamanho da máquina virtual que você especificou não é permitido para a função. Esse erro pode ocorrer porque o valor do tamanho da VM não funciona como esperado ou não é adequado para a função do computador.

### <a name="resolution"></a>Resolução

A mensagem de erro lista os valores válidos para o tamanho da VM. Selecione um desses valores e tente novamente a solicitação Criar cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Código de erro: InvalidVirtualNetworkId  

### <a name="error"></a>Erro

"O VirtualNetworkId não é válido. VirtualNetworkId\<'\>USER_VIRTUALNETWORKID '*'

### <a name="cause"></a>Causa

O valor **VirtualNetworkId** que você especificou durante a criação de clusters não está no formato correto.

### <a name="resolution"></a>Resolução

Certifique-se de que os valores **de VirtualNetworkId** e sub-rede estão no formato correto. Para obter o valor **VirtualNetworkId:**

1. Vá para o portal do Azure.
1. Selecione sua rede virtual.
1. Selecione o item do menu **Propriedades.** O valor da propriedade **ResourceID** é o valor **VirtualNetworkId.**

Aqui está um exemplo de um ID de rede virtual:

"/assinaturas/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Código de erro: PersonalizaçãoFalhaErrorCode

### <a name="error"></a>Erro

"A implantação do cluster falhou devido a um erro na ação de script personalizado. Ações fracassadas: \<SCRIPT_NAME\>, Por favor, vá para Ambari UI para depurar ainda mais a falha."

### <a name="cause"></a>Causa

O script personalizado fornecido durante a solicitação Criar cluster é executado depois que o cluster é implantado com sucesso. Este código de erro indica que um erro \<surgiu\>durante a execução do script personalizado chamado SCRIPT_NAME .

### <a name="resolution"></a>Resolução

Como o script é seu script personalizado, recomendamos que você solucionem o problema e reexecute o script se necessário. Para solucionar a falha do script, examine os logs na pasta /var/lib/ambari-agent/*. Ou abra a página **Operações** na UI Ambari e selecione **a** run_customscriptaction operação para visualizar os detalhes do erro.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Código de erro: InvalidDocumentErrorCode

### <a name="error"></a>Erro

"A \<\> versão \<do esquema META_STORE_TYPE\> Metastore\> METASTORE_MAJOR_VERSION no banco \<\>de dados \<DATABASE_NAME é incompatível com a versão de cluster CLUSTER_VERSION"

### <a name="cause"></a>Causa

O metastore personalizado é incompatível com a versão de cluster HDInsight selecionada. Atualmente, os clusters HDInsight 4.0 suportam apenas a versão 3.0 do Metastore e posterior, enquanto os clusters HDInsight 3.6 não suportam a versão 3.0 do Metastore e posterior.

### <a name="resolution"></a>Resolução

Use apenas versões metastore que sua versão de cluster HDInsight suporta. Se você não especificar um metastore personalizado, o HDInsight criará internamente um metastore e, em seguida, o excluirá após a exclusão do cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: FalhaAo conectarComoClusterErrorCode 

### <a name="error"></a>Erro

"Não é possível conectar-se ao ponto final do gerenciamento de cluster para executar a operação de dimensionamento. Verifique se as regras de segurança da rede não estão bloqueando o acesso externo ao cluster e se a ido de ida de ida e de gerenciamento de cluster (Ambari) pode ser acessada com sucesso."

### <a name="cause"></a>Causa

Uma regra de firewall no seu grupo de segurança de rede (NSG) está bloqueando a comunicação de cluster com serviços críticos de saúde e gerenciamento do Azure.

### <a name="resolution"></a>Resolução

Se você planeja usar grupos de segurança de rede para controlar o tráfego de rede, tome as seguintes ações antes de instalar o HDInsight:

- Identifique a região do Azure que você pretende usar para o HDInsight.
- Identifique os endereços IP necessários para o HDInsight. Para obter mais informações, consulte [Endereços IP de gerenciamento HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Crie ou modifique os grupos de segurança da rede para a sub-rede em que você planeja instalar o HDInsight.
  - Para grupos de segurança de rede, permita tráfego de entrada na porta 443 a partir dos endereços IP. Essa configuração garante que os serviços de gerenciamento hdInsight possam chegar ao cluster de fora da rede virtual.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Código de erro: StoragePersBlockedForMsi

### <a name="error"></a>Erro

"A Identidade Gerenciada não tem permissões na conta de armazenamento. Verifique se a função 'Storage Blob Data Owner' é atribuída à Identidade Gerenciada para a conta de armazenamento. Armazenamento: /assinaturas/ \<ID\> de assinatura\< /resourceGroups/ Resource Group\> Name /providers/Microsoft.Storage/storage/ \<Storage Account Name\>, Identidade gerenciada: /assinaturas/ \<ID\> de assinatura /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Causa

Você não forneceu as permissões necessárias para gerenciar a identidade. A identidade gerenciada atribuída pelo usuário não tem a função de Contribuinte de Armazenamento Blob na conta de armazenamento Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Resolução

1. Abra o portal do Azure.
1. Vá até sua conta de armazenamento.
1. Procure sob **controle de acesso (IAM)**.
1. Certifique-se de que o usuário tenha a função de contribuinte de dados blob de armazenamento ou a função de proprietário de dados do Blob de armazenamento atribuído a eles.

Para obter mais informações, consulte [Configurar permissões para a identidade gerenciada na conta Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Código de erro: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Erro

"As regras de segurança no Grupo\<de Segurança\>de Rede /assinaturas/\> SubscriptionID /resourceGroups/<Resource\<Group nome\> padrão/providers/Microsoft.Network/network/networkSecurityGroups/ Network Security Group Name\<configurado com sub-rede /assinaturas/ SubscriptionID\>/resourceGroups/\<Nome\> do Grupo de Recursos RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> não permite Para obter mais informações, visite [Plan a virtual network for Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)ou entre em contato com o suporte."

### <a name="cause"></a>Causa

Se os grupos de segurança de rede ou as UDRs (UDRs) controlarem o tráfego de entrada no seu cluster HDInsight, certifique-se de que seu cluster possa se comunicar com serviços críticos de saúde e gerenciamento do Azure.

### <a name="resolution"></a>Resolução

Se você planeja usar grupos de segurança de rede para controlar o tráfego de rede, tome as seguintes ações antes de instalar o HDInsight:

- Identifique a região do Azure que você planeja usar para o HDInsight e crie uma lista segura dos endereços IP para sua região. Para obter mais informações, consulte [Serviços de Saúde e Gestão: Regiões específicas](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifique os endereços IP que o HDInsight requer. Para obter mais informações, consulte [endereços IP de gerenciamento HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Crie ou modifique os grupos de segurança da rede para a sub-rede em que você planeja instalar o HDInsight. Para grupos de segurança de rede, permita tráfego de entrada na porta 443 a partir dos endereços IP. Essa configuração garante que os serviços de gerenciamento hdInsight possam chegar ao cluster de fora da rede virtual.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Código de erro: a configuração do cluster falhou em instalar componentes em um ou mais hosts

### <a name="error"></a>Erro

"A configuração do cluster falhou em instalar componentes em um ou mais hosts. Por favor, tente novamente o seu pedido.

### <a name="cause"></a>Causa 

Normalmente, esse erro é gerado quando há um problema transitório ou uma paralisação do Azure.

### <a name="resolution"></a>Resolução

Verifique a página [de status do Azure](https://status.azure.com) para verificar quaisquer paralisações do Azure que possam afetar a implantação do cluster. Se não houver paralisações, tente novamente a implantação do cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: falhaaoconecte-secomclusterErrorcode

### <a name="error"></a>Erro

Não é possível se conectar ao ponto final do gerenciamento de clusters. Tente novamente mais tarde.

### <a name="cause"></a>Causa

O HDInsight Service não pode se conectar ao seu cluster ao tentar criar o cluster

### <a name="resolution"></a>Resolução

Se você estiver usando NSGs (Custom network security group) (grupo de segurança de rede VNet) personalizados e rotas definidas pelo usuário (UDRs), certifique-se de que seu cluster possa se comunicar com os serviços de gerenciamento do HDInsight. Para obter informações adicionais, consulte [endereços IP de gerenciamento HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Código de erro: Implantações falharam devido<Resource URI>à violação da política: 'Recurso' foi desautorizado pela política. Identificadores de diretiva: '{"policyAssignment":{"name":"<Policy Name> ","id":"/providers/Microsoft.Management/managementGroups/providers/Microsoft.Authorization/policyAssignments/<Management Group Name> <Policy Name>"},"policyDefinition":<Policy Definition>

### <a name="cause"></a>Causa

As políticas do Azure baseadas em assinatura podem negar a criação de endereços IP públicos. A criação do cluster HDInsight exige dois IPs públicos.

As seguintes políticas geralmente impactam a criação de clusters:

* Políticas que impedem a criação de endereços IP ou balanceadores de carga dentro da assinatura.
* Política que impede a criação de contas de armazenamento.
* Política que impede a exclusão de recursos de rede, como endereços IP ou balanceadores de carga.

### <a name="resolution"></a>Resolução

Exclua ou desative a diretiva Azure baseada em assinatura ao criar o HDInsight Cluster.

---

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a solução de problemas na criação de clusters, [consulte'Solucionar falhas de criação de cluster solucionar problemas com o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
