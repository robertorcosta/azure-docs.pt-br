---
title: Transferir uma assinatura do Azure para um diretório diferente do Azure AD (versão prévia)
description: Saiba como transferir uma assinatura do Azure e recursos relacionados conhecidos para um diretório diferente do Azure Active Directory (Azure AD).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: rolyon
ms.openlocfilehash: 9873bd8f94c80caccd75033e2a8a4bc2cffcde03
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227025"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory-preview"></a>Transferir uma assinatura do Azure para um diretório diferente do Azure AD (versão prévia)

> [!IMPORTANT]
> Seguir estas etapas para transferir uma assinatura para um diretório diferente do Azure AD está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As organizações podem ter várias assinaturas do Azure. Cada assinatura é associada a um diretório específico do Azure Active Directory (Azure AD). Para facilitar o gerenciamento, talvez você queira transferir uma assinatura para um diretório diferente do Azure AD. Quando você transfere uma assinatura para um diretório diferente do Azure AD, alguns recursos não são transferidos para o diretório de destino. Por exemplo, todas as atribuições de função e funções personalizadas no controle de acesso baseado em função do Azure (RBAC do Azure) são excluídas **permanentemente** do diretório de origem e não são transferidas para o diretório de destino.

Este artigo descreve as etapas básicas que você pode seguir para transferir uma assinatura para um diretório diferente do Azure AD e recriar alguns dos recursos após a transferência.

> [!NOTE]
> Para assinaturas do CSP (provedores de serviços de nuvem) do Azure, não há suporte para a alteração do diretório do Azure AD para a assinatura.

## <a name="overview"></a>Visão geral

A transferência de uma assinatura do Azure para um diretório diferente do Azure AD é um processo complexo que deve ser cuidadosamente planejado e executado. Muitos serviços do Azure exigem entidades de segurança (identidades) para operar normalmente ou até mesmo gerenciar outros recursos do Azure. Este artigo tenta abranger a maioria dos serviços do Azure que dependem muito das entidades de segurança, mas não é abrangente.

> [!IMPORTANT]
> Em alguns cenários, a transferência de uma assinatura pode exigir tempo de inatividade para concluir o processo. Um planejamento cuidadoso é necessário para avaliar se o tempo de inatividade será necessário para sua transferência.

O diagrama a seguir mostra as etapas básicas que você deve seguir ao transferir uma assinatura para um diretório diferente.

1. Preparar para a transferência

1. Transferir a assinatura do Azure para um diretório diferente

1. Recriar recursos no diretório de destino, como atribuições de função, funções personalizadas e identidades gerenciadas

    ![Diagrama de transferência de assinatura](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Decidindo se uma assinatura deve ser transferida para um diretório diferente

A seguir, algumas razões pelas quais você pode querer transferir uma assinatura:

- Devido a uma fusão ou aquisição da empresa, você deseja gerenciar uma assinatura adquirida em seu diretório primário do Azure AD.
- Alguém em sua organização criou uma assinatura e você deseja consolidar o gerenciamento para um determinado diretório do Azure AD.
- Você tem aplicativos que dependem de uma ID de assinatura ou URL específica e não é fácil modificar a configuração ou o código do aplicativo.
- Uma parte de sua empresa foi dividida em uma empresa separada e você precisa mover alguns dos seus recursos para um diretório diferente do Azure AD.
- Você deseja gerenciar alguns de seus recursos em um diretório diferente do Azure AD para fins de isolamento de segurança.

A transferência de uma assinatura requer tempo de inatividade para concluir o processo. Dependendo do seu cenário, pode ser melhor recriar os recursos e copiar os dados para o diretório de destino e a assinatura.

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Entender o impacto da transferência de uma assinatura

Vários recursos do Azure têm uma dependência em uma assinatura ou em um diretório. Dependendo da sua situação, a tabela a seguir lista o impacto conhecido da transferência de uma assinatura. Ao executar as etapas neste artigo, você pode recriar alguns dos recursos que existiam antes da transferência da assinatura.

> [!IMPORTANT]
> Esta seção lista os serviços ou recursos conhecidos do Azure que dependem de sua assinatura. Como os tipos de recursos no Azure estão em constante evolução, pode haver dependências adicionais não listadas aqui que podem causar uma alteração significativa em seu ambiente. 

| Serviço ou recurso | Afetados | Recuperado | Você é afetado? | O que você pode fazer |
| --------- | --------- | --------- | --------- | --------- |
| Atribuições de função | Sim | Sim | [Listar atribuições de função](#save-all-role-assignments) | Todas as atribuições de função são excluídas permanentemente. Você deve mapear usuários, grupos e entidades de serviço para objetos correspondentes no diretório de destino. Você deve recriar as atribuições de função. |
| Funções personalizadas | Sim | Sim | [Listar funções personalizadas](#save-custom-roles) | Todas as funções personalizadas são excluídas permanentemente. Você deve recriar as funções personalizadas e quaisquer atribuições de função. |
| Identidades gerenciadas atribuídas pelo sistema | Sim | Sim | [Listar identidades gerenciadas](#list-role-assignments-for-managed-identities) | Você deve desabilitar e reabilitar as identidades gerenciadas. Você deve recriar as atribuições de função. |
| Identidades gerenciadas atribuídas pelo usuário | Sim | Sim | [Listar identidades gerenciadas](#list-role-assignments-for-managed-identities) | Você deve excluir, recriar e anexar as identidades gerenciadas ao recurso apropriado. Você deve recriar as atribuições de função. |
| Cofre de Chave do Azure | Sim | Sim | [Listar políticas de acesso Key Vault](#list-key-vaults) | Você deve atualizar a ID de locatário associada aos cofres de chaves. Você deve remover e adicionar novas políticas de acesso. |
| Bancos de dados SQL do Azure com a integração de autenticação do Azure AD habilitada | Sim | Não | [Verificar bancos de dados SQL do Azure com autenticação do Azure AD](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Armazenamento e Azure Data Lake Storage Gen2 do Azure | Sim | Sim |  | Você deve recriar quaisquer ACLs. |
| Azure Data Lake Storage Gen1 | Sim | Sim |  | Você deve recriar quaisquer ACLs. |
| Arquivos do Azure | Sim | Sim |  | Você deve recriar quaisquer ACLs. |
| Sincronização de Arquivos do Azure | Sim | Sim |  |  |
| Azure Managed Disks | Sim | N/D |  |  |
| Serviços de contêiner do Azure para kubernetes | Sim | Sim |  |  |
| Azure Active Directory Domain Services | Sim | Não |  |  |
| Registros de aplicativo | Sim | Sim |  |  |

> [!WARNING]
> Se você estiver usando a criptografia em repouso para um recurso, como uma conta de armazenamento ou um banco de dados SQL, que tenha uma dependência em um cofre de chaves que **não** esteja na mesma assinatura que está sendo transferida, isso poderá levar a um cenário irrecuperável. Se você tiver essa situação, deverá executar etapas para usar um cofre de chaves diferente ou desabilitar temporariamente as chaves gerenciadas pelo cliente para evitar esse cenário irrecuperável.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, será necessário:

- [Bash em Azure cloud Shell](/azure/cloud-shell/overview) ou [CLI do Azure](https://docs.microsoft.com/cli/azure)
- Administrador da conta da assinatura que você deseja transferir no diretório de origem
- Função de [proprietário](built-in-roles.md#owner) no diretório de destino

## <a name="step-1-prepare-for-the-transfer"></a>Etapa 1: preparar para a transferência

### <a name="sign-in-to-source-directory"></a>Entrar no diretório de origem

1. Entre no Azure como administrador.

1. Obtenha uma lista de suas assinaturas com o comando [AZ Account List](/cli/azure/account#az-account-list) .

    ```azurecli
    az account list --output table
    ```

1. Use o [conjunto de contas AZ](https://docs.microsoft.com/cli/azure/account#az-account-set) para definir a assinatura ativa que você deseja transferir.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>Instalar a extensão Resource-Graph

 A extensão de grafo de recursos permite que você use o comando [AZ Graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) para consultar recursos gerenciados pelo Azure Resource Manager. Você usará esse comando em etapas posteriores.

1. Use a [lista de extensões AZ](https://docs.microsoft.com/cli/azure/extension#az-extension-list) para ver se você tem a extensão *Resource-Graph* instalada.

    ```azurecli
    az extension list
    ```

1. Caso contrário, instale a extensão *Resource-Graph* .

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Salvar todas as atribuições de função

1. Use a [lista de atribuição de função AZ](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-list) para listar todas as atribuições de função (incluindo atribuições de função herdadas).

    Para facilitar a revisão da lista, você pode exportar a saída como JSON, TSV ou uma tabela. Para obter mais informações, consulte [listar atribuições de função usando o RBAC e CLI do Azure do Azure](role-assignments-list-cli.md).

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Salve a lista de atribuições de função.

    Quando você transfere uma assinatura, todas as atribuições de função são excluídas **permanentemente** , portanto, é importante salvar uma cópia.

1. Examine a lista de atribuições de função. Pode haver atribuições de função que você não precisará no diretório de destino.

### <a name="save-custom-roles"></a>Salvar funções personalizadas

1. Use a [lista de definição de função AZ](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-list) para listar suas funções personalizadas. Para obter mais informações, consulte [criar ou atualizar funções personalizadas do Azure usando CLI do Azure](custom-roles-cli.md).

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Salve cada função personalizada que será necessária no diretório de destino como um arquivo JSON separado.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Faça cópias dos arquivos de função personalizados.

1. Modifique cada cópia para usar o formato a seguir.

    Você usará esses arquivos posteriormente para recriar as funções personalizadas no diretório de destino.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Determinar mapeamentos de entidade de serviço, usuário e grupo

1. Com base na sua lista de atribuições de função, determine os usuários, grupos e entidades de serviço que serão mapeados no diretório de destino.

    Você pode identificar o tipo de entidade de segurança examinando a `principalType` propriedade em cada atribuição de função.

1. Se necessário, no diretório de destino, crie quaisquer usuários, grupos ou entidades de serviço necessários.

### <a name="list-role-assignments-for-managed-identities"></a>Listar atribuições de função para identidades gerenciadas

Identidades gerenciadas não são atualizadas quando uma assinatura é transferida para outro diretório. Como resultado, qualquer identidade gerenciada atribuída pelo sistema ou atribuída pelo usuário existente será interrompida. Após a transferência, você pode reabilitar todas as identidades gerenciadas atribuídas pelo sistema. Para identidades gerenciadas atribuídas pelo usuário, você precisará recriá-las e anexá-las no diretório de destino.

1. Examine a [lista de serviços do Azure que dão suporte a identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) para observar onde você pode estar usando identidades gerenciadas.

1. Use [AZ ad SP List](/cli/azure/identity?view=azure-cli-latest#az-identity-list) para listar suas identidades gerenciadas atribuídas pelo sistema e pelo usuário.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Na lista de identidades gerenciadas, determine quais são atribuídas pelo sistema e que são atribuídas pelo usuário. Você pode usar os critérios a seguir para determinar o tipo.

    | Critérios | Tipo de identidade gerenciada |
    | --- | --- |
    | `alternativeNames` Propriedade inclui `isExplicit=False` | Atribuído pelo sistema |
    | `alternativeNames` a propriedade não inclui `isExplicit` | Atribuído pelo sistema |
    | `alternativeNames` Propriedade inclui `isExplicit=True` | Atribuído pelo usuário |

    Você também pode usar a [lista de identidades AZ](https://docs.microsoft.com/cli/azure/identity#az-identity-list) para apenas listar identidades gerenciadas atribuídas pelo usuário. Para obter mais informações, consulte [criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md).

    ```azurecli
    az identity list
    ```

1. Obtenha uma lista dos `objectId` valores para suas identidades gerenciadas.

1. Pesquise sua lista de atribuições de função para ver se há atribuições de função para suas identidades gerenciadas.

### <a name="list-key-vaults"></a>Listar cofres de chave

Quando você cria um cofre de chaves, ele é automaticamente vinculado à ID de locatário de Azure Active Directory padrão para a assinatura na qual ele é criado. Todas as entradas de política de acesso também são vinculadas a essa ID de locatário. Para obter mais informações, consulte [movendo um Azure Key Vault para outra assinatura](../key-vault/general/move-subscription.md).

> [!WARNING]
> Se você estiver usando a criptografia em repouso para um recurso, como uma conta de armazenamento ou um banco de dados SQL, que tenha uma dependência em um cofre de chaves que **não** esteja na mesma assinatura que está sendo transferida, isso poderá levar a um cenário irrecuperável. Se você tiver essa situação, deverá executar etapas para usar um cofre de chaves diferente ou desabilitar temporariamente as chaves gerenciadas pelo cliente para evitar esse cenário irrecuperável.

- Se você tiver um cofre de chaves, use [AZ keyvault show](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-show) para listar as políticas de acesso. Para obter mais informações, consulte [fornecer Key Vault autenticação com uma política de controle de acesso](../key-vault/key-vault-group-permissions-for-apps.md).

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Listar bancos de dados SQL do Azure com autenticação do Azure AD

- Use [AZ SQL Server ad-admin List](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) e [AZ Graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) Extension para ver se você está usando bancos de dados SQL do Azure com a integração de autenticação do Azure ad habilitada. Para obter mais informações, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o SQL](../azure-sql/database/authentication-aad-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Listar ACLs

1. Se você estiver usando Azure Data Lake Storage Gen1, liste as ACLs que são aplicadas a qualquer arquivo usando o portal do Azure ou o PowerShell.

1. Se você estiver usando Azure Data Lake Storage Gen2, liste as ACLs que são aplicadas a qualquer arquivo usando o portal do Azure ou o PowerShell.

1. Se você estiver usando arquivos do Azure, liste as ACLs que são aplicadas a qualquer arquivo.

### <a name="list-other-known-resources"></a>Listar outros recursos conhecidos

1. Use a [conta AZ show](https://docs.microsoft.com/cli/azure/account#az-account-show) para obter sua ID de assinatura.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Use a extensão [AZ Graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) para listar outros recursos do Azure com dependências de diretório do Azure ad conhecidas.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Etapa 2: transferir a assinatura

Nesta etapa, você transfere a assinatura do diretório de origem para o diretório de destino. As etapas serão diferentes dependendo se você deseja também transferir a propriedade de cobrança.

> [!WARNING]
> Quando você transfere a assinatura, todas as atribuições de função no diretório de origem são excluídas **permanentemente** e não podem ser restauradas. Você não pode voltar depois de transferir a assinatura. Certifique-se de concluir as etapas anteriores antes de executar esta etapa.

1. Determine se você também deseja transferir a propriedade de cobrança para outra conta.

1. Transfira a assinatura para um diretório diferente.

    - Se você quiser manter a propriedade de cobrança atual, siga as etapas em [associar ou adicionar uma assinatura do Azure ao seu locatário Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    - Se você também quiser transferir a propriedade de cobrança, siga as etapas em [transferir a propriedade de cobrança de uma assinatura do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md). Para transferir a assinatura para um diretório diferente, você deve marcar a caixa de seleção **assinatura do locatário do Azure ad** .

1. Depois de concluir a transferência da assinatura, volte para este artigo para recriar os recursos no diretório de destino.

## <a name="step-3-re-create-resources"></a>Etapa 3: recriar recursos

### <a name="sign-in-to-target-directory"></a>Entrar no diretório de destino

1. No diretório de destino, entre como o usuário que aceitou a solicitação de transferência.

    Somente o usuário na nova conta que aceitou a solicitação de transferência terá acesso para gerenciar os recursos.

1. Obtenha uma lista de suas assinaturas com o comando [AZ Account List](https://docs.microsoft.com/cli/azure/account#az-account-list) .

    ```azurecli
    az account list --output table
    ```

1. Use o [conjunto de contas AZ](https://docs.microsoft.com/cli/azure/account#az-account-set) para definir a assinatura ativa que você deseja usar.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Criar funções personalizadas
        
- Use [AZ role Definition Create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create) para criar cada função personalizada a partir dos arquivos que você criou anteriormente. Para obter mais informações, consulte [criar ou atualizar funções personalizadas do Azure usando CLI do Azure](custom-roles-cli.md).

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>Criar atribuições de função

- Use [AZ role Assignment Create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) para criar as atribuições de função para usuários, grupos e entidades de serviço. Para obter mais informações, consulte [Adicionar ou remover atribuições de função usando o Azure RBAC e CLI do Azure](role-assignments-cli.md).

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Atualizar identidades gerenciadas atribuídas pelo sistema

1. Desabilitar e reabilitar identidades gerenciadas atribuídas pelo sistema.

    | Serviço do Azure | Mais informações | 
    | --- | --- |
    | Máquinas virtuais | [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | conjuntos de escala de máquina virtual | [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Outros serviços | [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Use [AZ role Assignment Create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) para criar as atribuições de função para identidades gerenciadas atribuídas pelo sistema. Para obter mais informações, consulte [atribuir um acesso de identidade gerenciada a um recurso usando CLI do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Atualizar identidades gerenciadas atribuídas pelo usuário

1. Exclua, crie novamente e anexe identidades gerenciadas atribuídas pelo usuário.

    | Serviço do Azure | Mais informações | 
    | --- | --- |
    | Máquinas virtuais | [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | conjuntos de escala de máquina virtual | [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Outros serviços | [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Criar, listar ou excluir uma identidade gerenciada atribuída ao usuário usando a CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Use [AZ role Assignment Create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) para criar as atribuições de função para identidades gerenciadas atribuídas pelo usuário. Para obter mais informações, consulte [atribuir um acesso de identidade gerenciada a um recurso usando CLI do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Atualizar cofres de chaves

Esta seção descreve as etapas básicas para atualizar seus cofres de chaves. Para obter mais informações, consulte [movendo um Azure Key Vault para outra assinatura](../key-vault/general/move-subscription.md).

1. Atualize a ID de locatário associada a todos os cofres de chaves existentes na assinatura para o diretório de destino.

1. Remover todas as entradas de política de acesso existentes.

1. Adicione novas entradas de política de acesso associadas ao diretório de destino.

### <a name="update-acls"></a>Atualizar ACLs

1. Se você estiver usando Azure Data Lake Storage Gen1, atribua as ACLs apropriadas. Para obter mais informações, consulte [protegendo os dados armazenados no Azure Data Lake armazenamento Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Se você estiver usando Azure Data Lake Storage Gen2, atribua as ACLs apropriadas. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

1. Se você estiver usando arquivos do Azure, atribua as ACLs apropriadas.

### <a name="review-other-security-methods"></a>Examinar outros métodos de segurança

Embora as atribuições de função sejam removidas durante a transferência, os usuários na conta do proprietário original podem continuar a ter acesso à assinatura por meio de outros métodos de segurança, incluindo:

- Chaves de acesso para serviços como Armazenamento.
- [Certificados de gerenciamento](../cloud-services/cloud-services-certs-create.md) que concedem ao administrador do usuário acesso aos recursos de assinatura.
- Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.

Se sua intenção é remover o acesso de usuários no diretório de origem para que eles não tenham acesso no diretório de destino, você deve considerar a rotação de qualquer credencial. Até que as credenciais sejam atualizadas, os usuários continuarão a ter acesso após a transferência.

1. Gire as chaves de acesso da conta de armazenamento. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md).

1. Se você estiver usando chaves de acesso para outros serviços, como o banco de dados SQL do Azure ou mensagens do barramento de serviço do Azure, gire as chaves de acesso.

1. Para recursos que usam segredos, abra as configurações do recurso e atualize o segredo.

1. Para recursos que usam certificados, atualize o certificado.

## <a name="next-steps"></a>Próximas etapas

- [Transferir a propriedade de cobrança de uma assinatura do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Transferir assinaturas do Azure entre assinantes e CSPs](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
