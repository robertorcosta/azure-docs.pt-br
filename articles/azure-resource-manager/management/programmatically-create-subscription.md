---
title: Criar programaticamente assinaturas do Azure
description: Aprenda a criar assinaturas adicionais do Azure de forma programática.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460391"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Criar programáticamente assinaturas do Azure (visualização)

Os clientes do Azure com um [Contrato Empresarial (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) podem criar assinaturas de forma programática. Neste artigo, você aprenderá a criar assinaturas programaticamente usando o Azure Resource Manager.

Quando você cria uma assinatura do Azure de forma programática, essa assinatura é regida pelo contrato o qual você obteve serviços Azure da Microsoft ou de um revendedor autorizado. Para saber mais, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Crie assinaturas para uma conta de faturamento da EA

Use as informações nas seções a seguir para criar assinaturas eA.

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma função proprietário em uma conta de inscrição para criar uma assinatura. Há duas maneiras de conseguir o papel:

* O Administrador Corporativo de sua inscrição pode [torná-lo um Proprietário de Conta](https://ea.azure.com/helpdocs/addNewAccount) (faça login obrigatório) o que faz de você um Proprietário da Conta de Inscrição.

* Um proprietário existente da conta de inscrição pode [conceder acesso a você](grant-access-to-create-subscription.md). Da mesma forma, se você quiser usar um diretor de serviço para criar uma assinatura da EA, você deve [conceder a esse principal serviço a capacidade de criar assinaturas](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Localize as contas às quais você tem acesso

Depois que você é adicionado a uma conta de inscrição associada a um proprietário de conta, o Azure usa a relação conta-para-inscrição para determinar onde cobrar as taxas de assinatura. Todas as assinaturas criadas a conta são cobradas na inscrição da EA em que a conta está. Para criar assinaturas, você deve passar valores sobre a conta de registro e as entidades de usuário a fim de ser dono da assinatura.

Para executar os comandos a seguir, você deve estar conectado ao *diretório base* do proprietário da conta, que é o diretório em que as assinaturas são criadas por padrão.

### <a name="rest"></a>[Resto](#tab/rest)

Solicite listar todas as contas de matrícula a que você tenha acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

A resposta da API lista todas as contas de inscrição a que você tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie `name` a conta dessa. Por exemplo, se você quisesse criar SignUpEngineering@contoso.com assinaturas sob a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```conta de inscrição, você copiaria . Este identificador é o ID do objeto da conta de inscrição. Cole esse valor em algum lugar para que você `enrollmentAccountObjectId`possa usá-lo na próxima etapa como .

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Abra [o Azure Cloud Shell](https://shell.azure.com/) e selecione o PowerShell.

Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

O Azure responde com uma lista de contas de inscrição a que você tem acesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie `ObjectId` a conta dessa. Por exemplo, se você quisesse criar SignUpEngineering@contoso.com assinaturas sob a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```conta de inscrição, você copiaria . Cole este id de objeto em algum lugar para que `enrollmentAccountObjectId`você possa usá-lo no próximo passo como o .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Use o comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso.

```azurecli-interactive
az billing enrollment-account list
```

O Azure responde com uma lista de contas de inscrição a que você tem acesso:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie `name` a conta dessa. Por exemplo, se você quisesse criar SignUpEngineering@contoso.com assinaturas sob a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```conta de inscrição, você copiaria . Este identificador é o ID do objeto da conta de inscrição. Cole esse valor em algum lugar para que você `enrollmentAccountObjectId`possa usá-lo na próxima etapa como .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Crie assinaturas em uma conta de registro específico

O exemplo a seguir cria uma assinatura chamada *Dev Team Subscription* na conta de inscrição selecionada na etapa anterior. A oferta de assinatura é *MS-AZR-0017P* (Acordo Empresarial Microsoft regular). Ele também adiciona dois usuários como Proprietários RBAC para a assinatura.

### <a name="rest"></a>[Resto](#tab/rest)

Faça a solicitação a seguir, substituindo `<enrollmentAccountObjectId>` pela `name` copiada na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, [aprenda como obter IDs de objeto sustais](grant-access-to-create-subscription.md#userObjectId)de usuário .

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Não      | String | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sim      | String | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Não       | String | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |

Em resposta, você obtém um objeto `subscriptionOperation` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionOperation` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Primeiro, instale este `Install-Module Az.Subscription -AllowPrerelease`módulo de visualização executando . Para certificar-se de que `-AllowPrerelease` funciona, instale uma versão recente do PowerShellGet do [Módulo Get do PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscription](/powershell/module/az.subscription) `<enrollmentAccountObjectId>` abaixo, `ObjectId` substituindo-o```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```coletado na primeira etapa ( ). Se você quiser especificar proprietários, [aprenda como obter IDs de objeto sustais](grant-access-to-create-subscription.md#userObjectId)de usuário .

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Não      | String | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sim      | String | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sim       | String | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Esse valor é um GUID que você obteve de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Não       | String | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `OwnerSignInName`    | Não       | String | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `OwnerObjectId`.|
| `OwnerApplicationId` | Não       | String | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `OwnerObjectId`. Ao usar esse parâmetro, o principal de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Para ver a lista completa de todos os parâmetros, confira [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Primeiro, instale esta `az extension add --name subscription`extensão de visualização executando .

Execute o comando [az account](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) `<enrollmentAccountObjectId>` create `name` abaixo, substituindo pelo```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```que você copiou na primeira etapa (). Se você quiser especificar proprietários, [aprenda como obter IDs de objeto sustais](grant-access-to-create-subscription.md#userObjectId)de usuário .

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Não      | String | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sim      | String | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sim       | String | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Esse valor é um GUID que você obteve de `az billing enrollment-account list`. |
| `owner-object-id`      | Não       | String | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `owner-upn`    | Não       | String | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `owner-object-id`.|
| `owner-spn` | Não       | String | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `owner-object-id`. Ao usar esse parâmetro, o principal de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Para ver uma lista completa de todos os parâmetros, consulte [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de assinatura do Azure Enterprise

- Somente as assinaturas do Azure Enterprise podem ser criadas usando esta API.
- Há um limite de 500 assinaturas por conta de inscrição. Depois disso, mais assinaturas para a conta só podem ser criadas no portal Azure. Se você quiser criar mais assinaturas através da API, crie outra conta de inscrição.
- Os usuários que não são proprietários de contas, mas foram adicionados a uma conta de inscrição via RBAC, não podem criar assinaturas no portal Azure.
- Você não pode selecionar o locatário para a assinatura a ser criada. A assinatura é sempre criada no locatário inicial do Proprietário da Conta. Para mover a assinatura para um locatário diferente, consulte [alterar o locatário da assinatura](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Crie assinaturas para uma conta MCA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter um proprietário, contribuinte ou criador de assinatura do Azure em uma seção de fatura ou função de proprietário ou contribuinte em um perfil de faturamento ou uma conta de faturamento para criar assinaturas. Para obter mais informações, confira [Funções e tarefas da cobrança de assinatura](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

O exemplo mostrado abaixo usa APIs REST. Atualmente, não há suporte para o PowerShell e a CLI do Azure.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Encontre contas de faturamento às quais você tem acesso

Faça a solicitação abaixo para listar todas as contas de faturamento.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de faturamento às as que você tem acesso.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use `displayName` a propriedade para identificar a conta de faturamento para a qual você deseja criar assinaturas. Certifique-se de que o tipo de concordância da conta é *MicrosoftCustomerAgreement*. Copie `name` a conta.  Por exemplo, se você quiser criar `Contoso` uma assinatura para a `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`conta de faturamento, você copiará . Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Encontre seções de faturas para criar assinaturas

Os encargos para sua assinatura aparecem em uma seção da fatura de um perfil de faturamento. Use a API a seguir para obter a lista de seções de faturas e perfis de faturamento nos quais você tem permissão para criar assinaturas do Azure.

Faça a solicitação a seguir, substituindo `<billingAccountName>` pela `name` copiada na primeira etapa (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
A resposta da API lista todas as seções de faturas e seus perfis de faturamento nos quais você tem acesso à criação de assinaturas:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Use `invoiceSectionDisplayName` a propriedade para identificar a seção de fatura para a qual você deseja criar assinaturas. Copie `invoiceSectionId`o `billingProfileId` e um `skuId` dos da seção de faturas. Por exemplo, se você quiser criar `Microsoft Azure plan` `Development` uma assinatura de tipo `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` para `0001`a seção de fatura, você copiará , e . Cole esses valores em algum lugar para que você possa usá-los no próximo passo.

### <a name="create-a-subscription-for-an-invoice-section"></a>Crie uma assinatura para uma seção de fatura

O exemplo a seguir cria uma assinatura chamada *Assinatura Dev Team* do tipo *Microsoft Azure Plan* para a seção de *faturas de desenvolvimento.* A assinatura será cobrada no perfil de faturamento *da Caixa Financeira* e aparecerá na seção *Desenvolvimento* de sua fatura.

Faça a seguinte solicitação, `<invoiceSectionId>` `invoiceSectionId` substituindo-a copiada```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```a partir da segunda etapa ( ). Você precisaria passar o `billingProfileId` `skuId` e copiado a partir da segunda etapa nos parâmetros de solicitação da API. Se você quiser especificar proprietários, [aprenda como obter IDs de objeto sustais](grant-access-to-create-subscription.md#userObjectId)de usuário .

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sim      | String | O nome de exibição da assinatura.|
| `billingProfileId`   | Sim      | String | O ID do perfil de faturamento que será cobrado pelos encargos da assinatura.  |
| `skuId` | Sim      | String | O sku ID que determina o tipo de plano Azure. |
| `owners`      | Não       | String | O ID do objeto de qualquer usuário ou diretor de serviço que você gostaria de adicionar como proprietário de RBAC na assinatura quando ele for criado.  |
| `costCenter` | Não      | String | O centro de custos associado à assinatura. Ele aparece no arquivo csv de uso. |
| `managementGroupId` | Não      | String | O ID do grupo de gestão ao qual a assinatura será adicionada. Para obter a lista de grupos de gerenciamento, consulte Grupos de [gerenciamento - API de lista](/rest/api/resources/managementgroups/list). Use o ID de um grupo de gerenciamento da API. |

Em resposta, você obtém um objeto `subscriptionCreationResult` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionCreationResult` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Crie assinaturas para uma conta de faturamento MPA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma função de Agente De administração global ou Admin na conta do provedor de soluções em nuvem da sua organização para criar assinatura para sua conta de faturamento. Para obter mais informações, consulte [Partner Center - Atribuir funções e permissões aos usuários](https://docs.microsoft.com/partner-center/permissions-overview).

O exemplo mostrado abaixo usa APIs REST. Atualmente, não há suporte para o PowerShell e a CLI do Azure.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Encontre as contas de faturamento às quais você tem acesso

Faça a solicitação abaixo para listar todas as contas de faturamento a que você tem acesso.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de faturamento.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use `displayName` a propriedade para identificar a conta de faturamento para a qual você deseja criar assinaturas. Certifique-se de que o tipo de concordância da conta é *MicrosoftPartnerAgreement*. Copie `name` o para a conta. Por exemplo, se você quiser criar `Contoso` uma assinatura para a `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`conta de faturamento, você copiará . Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa.

### <a name="find-customers-that-have-azure-plans"></a>Encontre clientes que tenham planos do Azure

Faça a seguinte solicitação, `<billingAccountName>` `name` substituindo-a copiada```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```a partir da primeira etapa ( ) para listar todos os clientes na conta de faturamento para quem você pode criar assinaturas do Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
A resposta da API lista os clientes na conta de faturamento com os planos do Azure. Você pode criar assinaturas para esses clientes.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Use `displayName` a propriedade para identificar o cliente para o qual você deseja criar assinaturas. Copie `id` o para o cliente. Por exemplo, se você quiser `Fabrikam toys`criar uma assinatura `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`para , você copiaria . Cole este valor em algum lugar para usá-lo nas etapas subsequentes.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcional para provedores indiretos: Obtenha os revendedores para um cliente

Se você é um provedor indireto no modelo de dois níveis CSP, você pode especificar um revendedor enquanto cria assinaturas para os clientes.

Faça a seguinte solicitação, `<customerId>` `id` substituindo pela copiada```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```a partir da segunda etapa ( ) para listar todos os revendedores disponíveis para um cliente.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
A resposta da API lista os revendedores para o cliente:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Use `description` o imóvel para identificar o revendedor que será associado à assinatura. Copie `resellerId` o para o revendedor. Por exemplo, se você `Wingtip`quiser associar, `3xxxxx`você copiaria . Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa.

### <a name="create-a-subscription-for-a-customer"></a>Crie uma assinatura para um cliente

O exemplo a seguir cria uma assinatura chamada *Assinatura Dev Team* para *brinquedos Fabrikam* e associar o revendedor *Wingtip* à assinatura. T

Faça a seguinte solicitação, `<customerId>` `id` substituindo-a copiada```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```a partir da segunda etapa ( ). Passe o *revendedor opcionalIcopiado* a partir da segunda etapa nos parâmetros de solicitação da API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sim      | String | O nome de exibição da assinatura.|
| `skuId` | Sim      | String | A id sku do plano Azure. Use *0001* para assinaturas do tipo Microsoft Azure Plan |
| `resellerId`      | Não       | String | O ID MPN do revendedor que será associado à assinatura.  |

Em resposta, você obtém um objeto `subscriptionCreationResult` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionCreationResult` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

## <a name="next-steps"></a>Próximas etapas

* Para um exemplo sobre a criação de uma assinatura Enterprise Agreement (EA) usando .NET, consulte [o código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)
