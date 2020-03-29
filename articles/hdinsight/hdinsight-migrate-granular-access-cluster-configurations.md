---
title: Configurações de cluster Azure HDInsight baseadas em função granular
description: Saiba mais sobre as alterações necessárias como parte da migração para acesso granular baseado em função para configurações de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310825"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para acesso baseado em função granular para configurações de cluster

Estamos introduzindo algumas mudanças importantes para apoiar o acesso mais fino baseado em papéis para obter informações confidenciais. Como parte dessas mudanças, algumas ações podem ser necessárias até 3 de setembro de **2019** se você estiver usando uma das [entidades/cenários afetados](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>O que está mudando?

Anteriormente, os segredos podiam ser obtidos através da API HDInsight por usuários de cluster que possuíam `*/read` as funções de Proprietário, Contribuinte ou [Leitor RBAC,](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)pois estavam disponíveis para qualquer pessoa com a permissão. Os segredos são definidos como valores que poderiam ser usados para obter acesso mais elevado do que o papel de um usuário deve permitir. Estes incluem valores como credenciais HTTP do gateway de cluster, chaves de conta de armazenamento e credenciais de banco de dados.

A partir de 3 de setembro de 2019, o acesso a esses segredos exigirá a permissão, o `Microsoft.HDInsight/clusters/configurations/action` que significa que eles não poderão mais ser acessados pelos usuários com a função Leitor. As funções que possuem essa permissão são Contribuinte, Proprietário e a nova função HDInsight Cluster Operator (mais sobre isso abaixo).

Também estamos introduzindo uma nova função [de Operador de Cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) que será capaz de recuperar segredos sem que sejam concedidas as permissões administrativas do Contribuinte ou proprietário. Resumidamente:

| Função                                  | Anteriormente                                                                                        | Indo para a frente       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | - Leia o acesso, incluindo segredos                                                                   | - Leia o acesso, **excluindo** segredos |           |   |   |
| Operador de cluster HDInsight<br>(Nova função) | N/D                                                                                              | - Acesso a leitura/gravação, incluindo segredos         |   |   |
| Colaborador                           | - Acesso a leitura/gravação, incluindo segredos<br>- Criar e gerenciar todos os tipos de recursos do Azure.     | Nenhuma alteração |
| Proprietário                                 | - Acesso de leitura/gravação, incluindo segredos<br>- Acesso total a todos os recursos<br>- Delegar acesso a outros | Nenhuma alteração |

Para obter informações sobre como adicionar a atribuição de função HDInsight Cluster Operator a um usuário para conceder-lhes acesso de leitura/gravação a segredos de cluster, consulte a seção abaixo, [adicione a atribuição de função HDInsight Cluster Operator a um usuário](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sou afetado por essas mudanças?

As seguintes entidades e cenários são afetados:

- [API](#api): Usuários `/configurations` `/configurations/{configurationName}` que usam os pontos finais ou os pontos finais.
- [Ferramentas Azure HDInsight para Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versão 1.1.1 ou abaixo.
- [Azure Toolkit para IntelliJ](#azure-toolkit-for-intellij) versão 3.20.0 ou abaixo.
- [Azure Data Lake e Stream Analytics Tools para Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) abaixo da versão 2.3.9000.1.
- [Azure Toolkit para eclipse](#azure-toolkit-for-eclipse) versão 3.15.0 ou abaixo.
- [SDK para .NET](#sdk-for-net)
    - [versões 1.x ou 2.x](#versions-1x-and-2x) `ConfigureHttpSettings`: `EnableHttp` `DisableHttp` Usuários que usam a `GetClusterConfigurations` `GetConnectivitySettings`classe ConfiguraçõesOperationsExtensions.
    - [versões 3.x e](#versions-3x-and-up) `Get`para `Update`cima : Usuários que usam os `EnableHttp`métodos da `DisableHttp` `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): `get` `update` Usuários que `ConfigurationsOperations` usam os métodos da classe.
- [SDK para Java](#sdk-for-java): `update` `get` Usuários que `ConfigurationsInner` usam os métodos da classe.
- [SDK para Go](#sdk-for-go): `Get` `Update` Usuários que `ConfigurationsClient` usam os métodos da estrutura.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) abaixo da versão 2.0.0.
Consulte as seções abaixo (ou use os links acima) para ver as etapas de migração para o seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou depreciadas:

- [**GET /configurações/{configuraçãoNome}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informações confidenciais removidas)
    - Anteriormente usado para obter tipos de configuração individuais (incluindo segredos).
    - A partir de 3 de setembro de 2019, esta chamada de API agora retornará tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, use a nova chamada POST/configurações. Para obter apenas configurações de gateway, use a nova chamada POST/getGatewaySettings.
- [**GET /configurações**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (depreciadas)
    - Anteriormente usado para obter todas as configurações (incluindo segredos)
    - A partir de 3 de setembro de 2019, esta chamada de API será preterida e não será mais suportada. Para obter todas as configurações daqui para frente, use a nova chamada POST/configurações. Para obter configurações com parâmetros sensíveis omitidos, use a chamada GET /configurations/{configurationName}.
- [**POST /configurações/{configuraçãoNome}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (depreciado)
    - Usado anteriormente para atualizar credenciais de gateway.
    - A partir de 3 de setembro de 2019, esta chamada de API será preterida e não será mais suportada. Use o novo POST /updateGatewaySettings em vez disso.

As seguintes APIs de substituição foram adicionadas:</span>

- [**POST /configurações**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Use esta API para obter todas as configurações, incluindo segredos.
- [**POST /getGatewayConfigurações**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Use esta API para obter configurações de gateway.
- [**POST /updateGatewayConfigurações**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Use esta API para atualizar as configurações do gateway (nome de usuário e/ou senha).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Ferramentas para Visual Studio Code

Se você estiver usando a versão 1.1.1 ou abaixo, atualize para a [versão mais recente do Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Kit de Ferramentas do Azure para IntelliJ

Se você estiver usando a versão 3.20.0 ou abaixo, atualize para a [versão mais recente do Azure Toolkit para plugin IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake e Stream Analytics Ferramentas para Visual Studio

Atualize para a versão 2.3.9000.1 ou posterior do [Azure Data Lake e Stream Analytics Tools para Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) para evitar interrupções.  Para obter ajuda com a atualização, consulte nossa documentação, [Atualize as ferramentas do Lago de Dados para o Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Kit de ferramentas do Azure para Eclipse

Se você estiver usando a versão 3.15.0 ou abaixo, atualize para a [versão mais recente do Azure Toolkit para eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>Versões 1.x e 2.x

Atualize para a [versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do HDInsight SDK para .NET. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations`**não retornará mais parâmetros sensíveis,** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, `ClusterOperationsExtensions.ListConfigurations` incluindo parâmetros sensíveis, use daqui para frente.  Observe que os usuários com a função 'Reader' não poderão usar este método. Isso permite um controle granular sobre o qual os usuários podem acessar informações confidenciais para um cluster.
    - Para recuperar apenas as `ClusterOperationsExtensions.GetGatewaySettings`credenciais do gateway HTTP, use .

- `ClusterOperationsExtensions.GetConnectivitySettings`é agora preterido e foi `ClusterOperationsExtensions.GetGatewaySettings`substituído por .

- `ClusterOperationsExtensions.ConfigureHttpSettings`é agora preterido e foi `ClusterOperationsExtensions.UpdateGatewaySettings`substituído por .

- `ConfigurationsOperationsExtensions.EnableHttp`e `DisableHttp` agora são preteridos. O HTTP está agora sempre ativado, de modo que esses métodos não são mais necessários.

#### <a name="versions-3x-and-up"></a>Versões 3.x e para cima

Atualize para a [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou posterior do HDInsight SDK para .NET. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**não retornará mais parâmetros sensíveis,** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) incluindo parâmetros sensíveis, use daqui para frente.Observe que os usuários com a função 'Reader' não poderão usar este método. Isso permite um controle granular sobre o qual os usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)credenciais do gateway HTTP, use . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)é agora preterido e foi [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)substituído por . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)e [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) agora são preteridos. O HTTP está agora sempre ativado, de modo que esses métodos não são mais necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualize para a [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou posterior do HDInsight SDK para Python. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**não retornará mais parâmetros sensíveis,** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) incluindo parâmetros sensíveis, use daqui para frente.Observe que os usuários com a função 'Reader' não poderão usar este método. Isso permite um controle granular sobre o qual os usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)credenciais do gateway HTTP, use .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)é agora preterido e foi [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)substituído por .

### <a name="sdk-for-java"></a>SDK para Java

Atualize para a [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) ou posterior do HDInsight SDK para Java. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**não retornará mais parâmetros sensíveis,** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)agora é preterido.

### <a name="sdk-for-go"></a>SDK para Ir

Atualize para a [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou posterior do HDInsight SDK for Go. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**não retornará mais parâmetros sensíveis,** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) incluindo parâmetros sensíveis, use daqui para frente.Observe que os usuários com a função 'Reader' não poderão usar este método. Isso permite um controle granular sobre o qual os usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)credenciais do gateway HTTP, use .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)é agora preterido e foi [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)substituído por .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Atualize para [a versão 2.0.0 do AZ PowerShell](https://www.powershellgallery.com/packages/Az) ou posterior para evitar interrupções.  Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações.
- `Grant-AzHDInsightHttpServicesAccess`agora é preterido e foi substituído `Set-AzHDInsightGatewayCredential` pelo novo cmdlet.
- `Get-AzHDInsightJobOutput`foi atualizado para suportar acesso granular baseado em função à chave de armazenamento.
    - Os usuários com as funções Operador do Cluster HDInsight, Colaborador ou Proprietário não serão afetados.
    - Os usuários com apenas a função `DefaultStorageAccountKey` Leitor precisarão especificar o parâmetro explicitamente.
- `Revoke-AzHDInsightHttpServicesAccess`agora é preterido. HTTP está agora sempre ativado, de modo que este cmdlet não é mais necessário.
 Veja o [az. Guia de migração hdInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para mais detalhes.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicione a atribuição de função HDInsight Cluster Operator a um usuário

Um usuário com a função [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pode atribuir a função [HDInsight Cluster Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) aos usuários que você deseja ter acesso a leitura/gravação a valores confidenciais de configuração do cluster HDInsight (como credenciais de gateway de cluster e chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

A maneira mais simples de adicionar essa `az role assignment create` atribuição de função é usando o comando no Azure CLI.

> [!NOTE]
> Este comando deve ser executado por um usuário com a função Proprietário, pois só eles podem conceder essas permissões. O `--assignee` é o nome do principal do serviço ou endereço de e-mail do usuário a quem você deseja atribuir a função hdinsight cluster operator. Se você receber um erro de permissões insuficiente, consulte o FAQ abaixo.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Função de concessão no nível de recurso (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Função de concessão no nível do grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Papel de concessão no nível de assinatura

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Você pode, alternativamente, usar o portal Azure para adicionar a atribuição de função HDInsight Cluster Operator a um usuário. Consulte a documentação, [Gerencie o acesso aos recursos do Azure usando o RBAC e o portal Azure - Adicione uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Perguntas frequentes

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Por que estou vendo uma resposta 403 (proibido) depois de atualizar minhas solicitações de API e/ou ferramenta?

As configurações de cluster estão agora por trás `Microsoft.HDInsight/clusters/configurations/*` do controle de acesso granular baseado em função e exigem a permissão para acessá-las. Para obter essa permissão, atribua a função HDInsight Cluster Operator, Contributor ou Owner ao usuário ou principal de serviço que tenta acessar configurações.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Por que vejo "Privilégios insuficientes para concluir a operação" ao executar o comando Azure CLI para atribuir a função hdinsight cluster operator a outro usuário ou diretor de serviço?

Além de ter a função Proprietário, o usuário ou o principal de serviço executando o comando precisa ter permissões AAD suficientes para procurar os IDs do objeto do cessionário. Esta mensagem indica permissões AAD insuficientes. Tente substituir `-–assignee` o `–assignee-object-id` argumento e forneça o ID do objeto do cessionário como parâmetro em vez do nome (ou o ID principal no caso de uma identidade gerenciada). Consulte a seção de parâmetros opcionais da [atribuição de função az criar documentação](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) para obter mais informações.

Se isso ainda não funcionar, entre em contato com o seu admin AAD para obter as permissões corretas.

### <a name="what-will-happen-if-i-take-no-action"></a>O que acontecerá se eu não agir?

A partir de 3 de `GET /configurations` setembro `POST /configurations/gateway` de 2019, `GET /configurations/{configurationName}` as chamadas não retornam mais nenhuma informação e a chamada não retornará mais parâmetros sensíveis, como chaves de conta de armazenamento ou a senha do cluster. O mesmo se aplica aos métodos SDK correspondentes e aos cmdlets PowerShell.

Se você estiver usando uma versão mais antiga de uma das ferramentas para visual studio, VSCode, IntelliJ ou Eclipse mencionado acima, eles não funcionarão mais até que você atualize.

Para obter informações mais detalhadas, consulte a seção correspondente deste documento para o seu cenário.
