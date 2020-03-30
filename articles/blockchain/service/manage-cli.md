---
title: Gerencie o Serviço blockchain do Azure usando o Azure CLI
description: Como gerenciar o Azure Blockchain Service com o Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455590"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gerencie o Serviço blockchain do Azure usando o Azure CLI

Além do portal Azure, você pode usar o Azure CLI para gerenciar membros de blockchain e nós de transação para o seu Azure Blockchain Service.

Certifique-se de que você instalou a MAIS recente [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e fez login em uma conta do Azure com `az login`.

Nos exemplos a seguir, `<parameter names>` substitua o exemplo por seus próprios valores.

## <a name="create-blockchain-member"></a>Criar membro blockchain

Exemplo cria um membro blockchain no Azure Blockchain Service que executa o protocolo de razão quórum em um novo consórcio.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **name** | Um nome exclusivo que identifica o membro do blockchain do serviço Azure Blockchain. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **Localização** | Região do Azure em que o membro do blockchain é criado. Por exemplo, `eastus`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. |
| **senha** | A senha da conta de membro. A senha da conta de membro é usada para autenticação no ponto de extremidade público do membro do blockchain usando a autenticação básica. A senha deve atender a três dos quatro requisitos seguintes: comprimento deve ser entre 12 & 72 caracteres, 1 caractere minúsculo, 1 caractere maiúsculo, 1 número\`e 1 caractere especial que não é sinal de número (#), por cento(%), vírgula(,), estrela(*), citação de volta( , citação dupla("), citação única('), traço(-) e semicoluna(;)|
| **Protocolo** | A pré-visualização pública suporta quórum. |
| **Consórcio** | Nome do consórcio a ser ingressado ou criado. |
| **consortiumManagementAccountPassword** | A senha de gerenciamento do consórcio. A senha é usada para se juntar a um consórcio. |
| **Rulename** | Nome da regra para listar em branco um intervalo de endereços IP. Parâmetro opcional para regras de firewall.|
| **startIpAddress** | Início da faixa de endereço IP para whitelisting. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim da faixa de endereço IP para whitelisting. Parâmetro opcional para regras de firewall. |
| **skuName** | Tipo de camada. Use S0 para Standard e B0 para Básico. |

## <a name="change-blockchain-member-password"></a>Alterar a senha do membro blockchain

Exemplo altera a senha de um membro blockchain.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **name** | Nome que identifica seu membro do Azure Blockchain Service. |
| **senha** | A senha da conta de membro. A senha deve atender a três dos quatro requisitos a seguir: comprimento deve ser entre 12 & 72 caracteres, 1 caractere minúsculo, 1 caractere maiúsculo, 1\`número e 1 caractere especial que não é sinal de número(#), por cento(%), vírgula(,), estrela(*), citação de volta( ), citação dupla("), citação simples('), traço(-) e ponto e vírgula(;)). |

## <a name="create-transaction-node"></a>Criar nó de transação

Crie um nó de transação dentro de um membro blockchain existente. Adicionando nós de transação, você pode aumentar o isolamento de segurança e distribuir carga. Por exemplo, você pode ter um ponto final de nó de transação para diferentes aplicativos clientes.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **name** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |
| **Localização** | Região do Azure em que o membro do blockchain é criado. Por exemplo, `eastus`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. |
| **senha** | A senha do nó de transação. A senha deve atender a três dos quatro requisitos a seguir: comprimento deve ser entre 12 & 72 caracteres, 1 caractere minúsculo, 1 caractere maiúsculo, 1\`número e 1 caractere especial que não é sinal de número(#), por cento(%), vírgula(,), estrela(*), citação de volta( ), citação dupla("), citação simples('), traço(-) e ponto e vírgula(;)). |
| **Rulename** | Nome da regra para listar em branco um intervalo de endereços IP. Parâmetro opcional para regras de firewall. |
| **startIpAddress** | Início da faixa de endereço IP para whitelisting. Parâmetro opcional para regras de firewall. |
| **endIpAddress** | Fim da faixa de endereço IP para whitelisting. Parâmetro opcional para regras de firewall.|

## <a name="change-transaction-node-password"></a>Alterar a senha do nó de transação

Exemplo altera uma senha de nó de transação.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Azure Blockchain Service. |
| **name** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |
| **senha** | A senha do nó de transação. A senha deve atender a três dos quatro requisitos a seguir: comprimento deve ser entre 12 & 72 caracteres, 1 caractere minúsculo, 1 caractere maiúsculo, 1\`número e 1 caractere especial que não é sinal de número(#), por cento(%), vírgula(,), estrela(*), citação de volta( ), citação dupla("), citação simples('), traço(-) e ponto e vírgula(;)). |

## <a name="change-consortium-management-account-password"></a>Alterar a senha da conta de gerenciamento do consórcio

A conta de gestão do consórcio é usada para a gestão de membros do consórcio. Cada membro é identificado exclusivamente por uma conta de gerenciamento de consórcio e você pode alterar a senha desta conta com o seguinte comando.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **name** | Nome que identifica seu membro do Azure Blockchain Service. |
| **consortiumManagementAccountPassword** | A senha da conta de gerenciamento do consórcio. A senha deve atender a três dos quatro requisitos a seguir: comprimento deve ser entre 12 & 72 caracteres, 1 caractere minúsculo, 1 caractere maiúsculo, 1\`número e 1 caractere especial que não é sinal de número(#), por cento(%), vírgula(,), estrela(*), citação de volta( ), citação dupla("), citação simples('), traço(-) e ponto e vírgula(;)). |
  
## <a name="update-firewall-rules"></a>Atualizar regras de firewall

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Azure Blockchain Service. |
| **name** | Nome do membro blockchain do Azure Blockchain Service. |
| **Rulename** | Nome da regra para listar em branco um intervalo de endereços IP. Parâmetro opcional para regras de firewall.|
| **startIpAddress** | Início da faixa de endereço IP para whitelisting. Parâmetro opcional para regras de firewall.|
| **endIpAddress** | Fim da faixa de endereço IP para whitelisting. Parâmetro opcional para regras de firewall.|

## <a name="list-api-keys"></a>Listar chaves de API

As chaves de API podem ser usadas para acesso ao nó semelhante ao nome de usuário e senha. Existem duas teclas de API para suportar a rotação de chaves. Use o comando a seguir para listar suas chaves de API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Azure Blockchain Service. |
| **name** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |

## <a name="regenerate-api-keys"></a>Regenerar chaves API

Use o seguinte comando para regenerar suas teclas de API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Azure Blockchain Service. |
| **name** | Nome do membro blockchain do Azure Blockchain Service que também inclui o novo nome do nó de transação. |
| **Keyname** | Substitua \<\> a chaveValor por chave1 ou chave2. |

## <a name="delete-a-transaction-node"></a>Exclua um nó de transação

Exemplo exclui um nó de transação de membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Azure Blockchain Service. |
| **name** | Nome do membro blockchain do Azure Blockchain Service que também inclui o nome do nó de transação a ser excluído. |

## <a name="delete-a-blockchain-member"></a>Exclua um membro blockchain

Exemplo exclui um membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parâmetro | Descrição |
|---------|-------------|
| **grupo de recursos** | Nome do grupo de recursos onde existem recursos do Azure Blockchain Service. |
| **name** | Nome do membro blockchain do Azure Blockchain Service a ser excluído. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso para usuário Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **Papel** | Nome do papel azure AD. |
| **assignee** | ID do usuário Azure AD. Por exemplo, `user@contoso.com` |
| **scope** | Escopo da atribuição de papéis. Pode ser um membro blockchain ou um nó de transação. |

**Exemplo:**

Conceder acesso ao nó para o usuário Azure AD para **membro**blockchain :

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso ao nó para o usuário azure AD ao **nó de transação**blockchain :

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Conceder acesso ao grupo Ad do Azure ou função de aplicativo

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parâmetro | Descrição |
|---------|-------------|
| **Papel** | Nome do papel azure AD. |
| **atribuído-objeto-id** | ID do grupo Azure AD ou ID de aplicativo. |
| **scope** | Escopo da atribuição de papéis. Pode ser um membro blockchain ou um nó de transação. |

**Exemplo:**

Conceder acesso ao nó para **a função de aplicação**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover acesso ao nó Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **Papel** | Nome do papel azure AD. |
| **assignee** | ID do usuário Azure AD. Por exemplo, `user@contoso.com` |
| **scope** | Escopo da atribuição de papéis. Pode ser um membro blockchain ou um nó de transação. |

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar os álos de transação do Azure Blockchain Service com o portal Azure](configure-transaction-nodes.md).
