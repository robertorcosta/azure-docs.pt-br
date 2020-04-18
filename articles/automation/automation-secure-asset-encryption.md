---
title: Criptografar ativos seguros na Automação do Azure
description: O Azure Automation protege ativos seguros usando vários níveis de criptografia. Por padrão, a criptografia é feita usando chaves gerenciadas pela Microsoft. Os clientes podem configurar suas contas de automação para usar chaves gerenciadas pelo cliente para criptografia. Este artigo descreve os detalhes de ambos os modos de criptografia e como você pode alternar entre os dois.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604809"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Criptografar ativos seguros na Automação do Azure

Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos estão protegidos no Azure Automation usando vários níveis de criptografia. Com base na chave de alto nível usada para a criptografia, existem dois modelos para criptografia:
-    Usando chaves gerenciadas pela Microsoft
-    Usando chaves gerenciadas pelo cliente

## <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta Azure Automation usa chaves gerenciadas pela Microsoft.

Cada ativo seguro é criptografado e armazenado no Azure Automation usando uma chave única (chave de criptografia de dados) que é gerada para cada conta de automação. Essas chaves em si são criptografadas e armazenadas no Azure Automation usando mais uma chave única que é gerada para cada conta chamada AEK (Account Encryption Key). Essas chaves de criptografia de conta criptografadas e armazenadas no Azure Automation usando chaves gerenciadas pela Microsoft. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Chaves gerenciadas pelo cliente com Key Vault (visualização)

Você pode gerenciar a criptografia de ativos seguros para sua conta de Automação com suas próprias chaves. Quando você especifica uma chave gerenciada pelo cliente no nível da conta Automação, essa chave é usada para proteger e controlar o acesso à chave de criptografia da conta para a conta automação. Isso, por sua vez, é usado para criptografar e descriptografar todos os ativos seguros. As chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus ativos seguros.

Use o Azure Key Vault para armazenar chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves.  Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Habilite as chaves gerenciadas pelo cliente para uma conta de automação

Quando você habilita a criptografia com chaves gerenciadas pelo cliente para uma conta de automação, a Azure Automation envolve a chave de criptografia da conta com a chave gerenciada pelo cliente no cofre de chaves associado. A ativação de chaves gerenciadas pelo cliente não afeta o desempenho, e a conta é criptografada com a nova chave imediatamente, sem qualquer atraso.

Uma nova conta de Automação é sempre criptografada usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que a conta é criada. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões-chave à identidade gerenciada associada à conta Automação. A identidade gerenciada só está disponível depois que a conta de armazenamento for criada.

Quando você modifica a chave usada para a criptografia de ativos seguros do Azure Automation, ativando ou desativando chaves gerenciadas pelo cliente, atualizando a versão-chave ou especificando uma chave diferente, a criptografia da chave de criptografia da conta muda, mas os ativos seguros em sua conta Azure Automation não precisam ser recriptografados.

As três seções a seguir descrevem a mecânica de habilitar chaves gerenciadas pelo cliente para uma conta de Automação. 

> [!NOTE] 
> Para habilitar as chaves gerenciadas pelo cliente, você precisa fazer chamadas aPi Azure Automation REST usando a versão api 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Pré-requisitos para usar chaves gerenciadas pelo cliente no Azure Automation

Antes de habilitar as chaves gerenciadas pelo cliente para uma conta de Automação, você deve garantir que os seguintes pré-requisitos sejam atendidos:

 - A chave manged pelo cliente é armazenada em um Cofre chave Azure. 
 - Habilite as propriedades **Soft Delete** e **Não Expurguem** no cofre da chave. Esses recursos são necessários para permitir a recuperação de chaves em caso de exclusão acidental.
 - Apenas as chaves RSA são suportadas com criptografia Azure Automation. Para obter mais informações sobre chaves, consulte [sobre as chaves, segredos e certificados do Azure Key Vault](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- A conta de Automação e o cofre-chave podem estar em assinaturas diferentes, mas precisam estar no mesmo inquilino do Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Atribuir uma identidade à conta automação

Para usar chaves gerenciadas pelo cliente com uma conta de Automação, sua conta de Automação precisa autenticar contra o cofre chave armazenando chaves gerenciadas pelo cliente. O Azure Automation usa identidades gerenciadas atribuídas ao sistema para autenticar a conta com o Azure Key Vault. Para obter mais informações sobre identidades gerenciadas, consulte [Quais são as identidades gerenciadas para os recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Configure uma identidade gerenciada atribuída ao sistema para a conta Automação usando a seguinte chamada de API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo da solicitação:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

A identidade atribuída ao sistema para a conta Automação é retornada em uma resposta semelhante à seguinte:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso do Key Vault

Uma vez que uma identidade gerenciada é atribuída à conta Automação, você configura o acesso ao cofre chave armazenando chaves gerenciadas pelo cliente. A Azure Automation requer **obter**, **recuperar**, **wrapKey**, **DesembrulharChave** nas chaves gerenciadas pelo cliente.

Essa política de acesso pode ser definida usando a seguinte chamada da API REST:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corpo da solicitação:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Os campos **tenantId** e **objectId** devem ser fornecidos com valores de **identidade.tenantId** e **identity.principalId,** respectivamente, a partir da resposta da identidade gerenciada para a conta de Automação.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Alterar a configuração da conta de automação para usar a chave gerenciada pelo cliente

Finalmente, você pode mudar sua conta de automação de chaves gerenciadas pela Microsoft para chaves gerenciadas pelo cliente, usando a seguinte chamada de API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo da solicitação:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Resposta de exemplo

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Gerenciar o ciclo de vida das chaves gerenciadas pelo cliente

### <a name="rotate-customer-managed-keys"></a>Gire as chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar a conta Automação para usar a nova chave URI.

Girar a chave não desencadeia a recriptografia de ativos seguros na conta Automação. Não é necessário mais nenhuma ação.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia efetivamente o acesso a todos os ativos seguros na conta de Automação, já que a chave de criptografia é inacessível pela Azure Automation.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Azure Key Vault?](../key-vault/general/overview.md)
- [Ativos de certificado na Automação do Azure](shared-resources/certificates.md)
- [Ativos de credenciais na Automação do Azure](shared-resources/credentials.md)
- [Ativos variáveis na Automação do Azure](shared-resources/variables.md)
