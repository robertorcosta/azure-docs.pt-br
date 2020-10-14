---
title: Azure Key Vault mover um cofre para uma assinatura diferente | Microsoft Docs
description: Orientação sobre como mover um cofre de chaves para uma assinatura diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 2a06fd55d73c37caaa35797131d2b31817bf90f0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042398"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Movendo um Azure Key Vault para outra assinatura

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

> [!IMPORTANT]
> **Mover um cofre de chaves para outra assinatura causará uma alteração significativa no seu ambiente.**
> Verifique se você entendeu o impacto dessa alteração e siga as orientações neste artigo cuidadosamente antes de decidir mover o cofre de chaves para uma nova assinatura.
> Se você estiver usando o MSI (identidades de serviço gerenciado), leia as instruções de pós-atualização no final do documento. 

Quando você cria um cofre de chaves, ele é automaticamente vinculado à ID de locatário de Azure Active Directory padrão para a assinatura na qual ele é criado. Todas as entradas de política de acesso também são vinculadas a essa ID de locatário. Se você mover sua assinatura do Azure do locatário A para o locatário B, seus cofres de chaves existentes ficarão inacessíveis pelas entidades de serviço (usuários e aplicativos) no locatário B. Para corrigir esse problema, você precisa:

* Alterar a ID do locatário associada a todos os cofres de chaves existentes na assinatura para o locatário B.
* Remover todas as entradas de política de acesso existentes.
* Adicionar novas entradas de política de acesso associadas ao locatário B.

## <a name="limitations"></a>Limitações

> [!IMPORTANT]
> Os **cofres de chaves usados para a criptografia de disco não podem ser movidos** Se você estiver usando o Key Vault com criptografia de disco para uma VM, o cofre de chaves não poderá ser movido para um grupo de recursos diferente ou uma assinatura enquanto a criptografia de disco estiver habilitada. Você deve desabilitar a criptografia de disco antes de mover o cofre de chaves para um novo grupo de recursos ou assinatura. 

Algumas entidades de serviço (usuários e aplicativos) estão associadas a um locatário específico. Se você mover o cofre de chaves para uma assinatura em outro locatário, haverá uma chance de que você não consiga restaurar o acesso a uma entidade de serviço específica. Verifique se todas as entidades de serviço essenciais existem no locatário em que você está movendo o cofre de chaves.

## <a name="design-considerations"></a>Considerações sobre o design

Sua organização pode ter implementado Azure Policy com imposição ou exclusões no nível da assinatura. Pode haver um conjunto diferente de atribuições de política na assinatura em que o cofre de chaves existe atualmente e a assinatura em que você está movendo o cofre de chaves. Um conflito nos requisitos da política tem o potencial de quebrar seus aplicativos.

### <a name="example"></a>Exemplo

Você tem um aplicativo conectado ao key Vault que cria certificados que são válidos por dois anos. A assinatura em que você está tentando mover o cofre de chaves tem uma atribuição de política que bloqueia a criação de certificados válidos por mais de um ano. Depois de mover o cofre de chaves para a nova assinatura, a operação para criar um certificado válido por dois anos será bloqueada por uma atribuição de política do Azure.

### <a name="solution"></a>Solução

Certifique-se de ir para a página Azure Policy na portal do Azure e examine as atribuições de política para sua assinatura atual, bem como a assinatura para a qual você está migrando e verifique se não há nenhuma correspondência.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso de nível de colaborador ou superior à assinatura atual em que o cofre de chaves existe.
* Acesso de nível de colaborador ou superior à assinatura em que você deseja mover o cofre de chaves.
* Um grupo de recursos na nova assinatura.

## <a name="procedure"></a>Procedimento

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>Movendo Key Vault para uma nova assinatura dentro do mesmo locatário

1. Faça logon no Portal do Azure
2. Navegue até o Key Vault
3. Clique na guia "visão geral"
4. Selecione o botão "mover"
5. Selecione "mover para outra assinatura" nas opções de lista suspensa
6. Selecione o grupo de recursos para o qual você deseja mover o cofre de chaves
7. Reconhecer o aviso sobre a movimentação de recursos
8. Selecione "OK"

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>Etapas adicionais se você moveu o cofre de chaves para uma assinatura em um novo locatário

Se você moveu o cofre de chaves para uma assinatura em um novo locatário, precisa atualizar manualmente a ID do locatário e remover políticas de acesso antigas. Aqui estão os tutoriais para essas etapas no PowerShell e CLI do Azure. Se você estiver usando o PowerShell, talvez seja necessário executar o comando Clear-AzContext documentado abaixo para permitir que você veja os recursos fora do escopo selecionado atual. 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Agora que o cofre está associado à ID de locatário correta e as antigas entradas da política de acesso foram removidas, defina novas entradas da política de acesso com o cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) do Azure PowerShell ou o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) da CLI do Azure.

Se você estiver usando uma identidade gerenciada para recursos do Azure, será necessário atualizá-la para o novo locatário Azure Active Directory também. Para obter mais informações sobre identidades gerenciadas, [visão geral da identidade gerenciada](/azure/active-directory/managed-identities-azure-resources/overview).

Se você estiver usando a identidade gerenciada, também precisará atualizar a identidade porque a identidade antiga não estará mais no locatário de Azure Active Directory correto. Consulte os documentos a seguir para ajudar a resolver esse problema. 

* [Atualizando MSI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)
* [Transferir assinatura para o novo diretório](https://docs.microsoft.com/azure/role-based-access-control/transfer-subscription)
