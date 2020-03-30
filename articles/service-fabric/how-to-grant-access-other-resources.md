---
title: Conceda acesso a um aplicativo a outros recursos do Azure
description: Este artigo explica como conceder o acesso do aplicativo Service Fabric habilitado para identidade gerenciada a outros recursos do Azure que suportam a autenticação baseada no Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614786"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concedendo o acesso gerenciado de identidade de um aplicativo de malha de serviço aos recursos do Azure (visualização)

Antes que o aplicativo possa usar sua identidade gerenciada para acessar outros recursos, as permissões devem ser concedidas a essa identidade no recurso protegido do Azure que está sendo acessado. A concessão de permissões é tipicamente uma ação de gerenciamento no "plano de controle" do serviço Azure que possui o recurso protegido roteado via Azure Resource Manager, que aplicará qualquer verificação de acesso baseada em função aplicável.

A seqüência exata das etapas dependerá, então, do tipo de recurso do Azure que está sendo acessado, bem como do idioma/cliente usado para conceder permissões. O restante do artigo assume uma identidade atribuída ao usuário atribuída ao aplicativo e inclui vários exemplos típicos para sua conveniência, mas não é de forma alguma uma referência exaustiva para este tópico; consulte a documentação dos respectivos serviços do Azure para obter instruções atualizadas sobre a concessão de permissões.  

## <a name="granting-access-to-azure-storage"></a>Concessão de acesso ao Armazenamento Azure
Você pode usar a identidade gerenciada do aplicativo Service Fabric (atribuída pelo usuário neste caso) para recuperar os dados de uma bolha de armazenamento do Azure. Conceda à identidade as permissões necessárias no portal Azure com as seguintes etapas:

1. Navegue até a conta de armazenamento
2. Clique no link do Controle de acesso (IAM) no painel à esquerda.
3. (opcional) Verifique o acesso existente: selecione a identidade gerenciada atribuída pelo sistema ou pelo usuário no controle 'Encontrar'; selecionar a identidade apropriada da lista de resultados subsequente
4. Clique em + Adicione a atribuição de função no topo da página para adicionar uma nova atribuição de função para a identidade do aplicativo.
Em Função, no menu suspenso, selecione Leitor de Dados de Blob de Armazenamento.
5. Na próxima queda, em Atribuição `User assigned managed identity`de acesso, escolha .
6. Depois, verifique se a assinatura correta está listada no menu suspenso Assinatura e defina Grupo de Recursos como Todos os grupos de recursos.
7. Em Select, escolha o UAI correspondente ao aplicativo Service Fabric e clique em Salvar.

O suporte para identidades gerenciadas pelo Service Fabric atribuídas pelo sistema não inclui integração no portal Azure; se o aplicativo usar uma identidade atribuída ao sistema, você terá que encontrar primeiro o ID do cliente `Azure AD user, group, or service principal` da identidade do aplicativo e, em seguida, repetir as etapas acima, mas selecionando a opção no controle Encontrar.

## <a name="granting-access-to-azure-key-vault"></a>Concessão de acesso ao Azure Key Vault
Da mesma forma, com o acesso ao armazenamento, você pode aproveitar a identidade gerenciada de um aplicativo de malha de serviço para acessar um cofre de chaves do Azure. As etapas para a concessão de acesso no portal Azure são semelhantes às listadas acima, e não serão repetidas aqui. Consulte a imagem abaixo para obter diferenças.

![Política de acesso ao cofre de chaves](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

O exemplo a seguir ilustra a concessão de acesso a um cofre por meio de uma implantação de modelo; adicionar o trecho abaixo como outra entrada `resources` sob o elemento do modelo. A amostra demonstra a concessão de acesso para os tipos de identidade atribuídos pelo usuário e pelo sistema, respectivamente - escolha o aplicável.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
E para identidades gerenciadas atribuídas pelo sistema:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Para obter mais [detalhes,](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)consulte Vaults - Update Access Policy .

## <a name="next-steps"></a>Próximas etapas
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)