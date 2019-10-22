---
title: Acompanhar o saldo de crédito Azure para um Contrato de Cliente da Microsoft
description: Saiba como verificar o saldo de crédito Azure para um Contrato de Cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375579"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Acompanhar o saldo de crédito Azure no Contrato de Cliente da Microsoft

Verifique o saldo de crédito Azure de sua conta de cobrança para um Contrato de Cliente da Microsoft no portal do Azure. 

Use créditos para pagar os encargos que são qualificados para créditos. Você é cobrado quando usa produtos que não estão qualificados para créditos ou quando seu uso excede seu saldo de crédito. Para obter mais informações, confira [Produtos que não são cobertos pelos créditos Azure](#products-that-arent-covered-by-azure-credits).

Na conta de cobrança de um Contrato de Cliente da Microsoft, os créditos são atribuídos a um perfil de cobrança. Cada perfil de cobrança tem seus próprios créditos. Você precisa ter uma função de proprietário, colaborador, leitor ou gerenciador de faturas no perfil de cobrança ou uma função de proprietário, colaborador ou leitor na conta de cobrança para exibir o saldo de crédito Azure de um perfil de cobrança. Para saber mais sobre as funções, confira [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](billing-understand-mca-roles.md).

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Verificar seu saldo de crédito no portal do Azure

1. Entre no [Portal do Azure]( https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecione **Créditos Azure** no lado esquerdo. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança ou um perfil de cobrança e, em seguida, selecionar **créditos Azure**.

4. A página de créditos Azure exibe as seguintes informações:

   ![Captura de tela do saldo de crédito e das transações de um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | O valor estimado de créditos que você terá depois de considerar todas as transações faturadas e pendentes |
   | Saldo atual    | Valor de créditos da última fatura. Ele não inclui nenhuma transação pendente |
   | Transações       | Transações de cobrança que afetaram o seu saldo de crédito Azure |

   Quando o saldo estimado cair para 0, você será cobrado por todo o seu uso, incluindo os produtos qualificados para créditos.

6. Selecione **Lista de créditos** para exibir a lista de créditos do perfil de cobrança. A lista de créditos fornece as seguintes informações:

   ![Captura de tela da listas de créditos de um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Fonte | A origem da aquisição do crédito |
   | Data de início | A data em que você adquiriu o crédito |
   | Data de validade | A data em que o crédito expira |
   | Saldo atual | O saldo de sua última fatura |
   | Valor original | O valor original do crédito |
   | Status | O status atual do crédito. O status pode ser ativo, usado, expirado ou a expirar |

## <a name="check-your-credit-balance-programmatically"></a>Verificar seu saldo de crédito de forma programática

Use as APIs de [Cobrança do Azure](https://docs.microsoft.com/rest/api/billing/) e [Consumo](https://docs.microsoft.com/rest/api/consumption/) para obter o saldo de crédito de sua conta de cobrança de forma programática.

Os exemplos mostrados abaixo usam APIs REST. Atualmente, não há suporte para o PowerShell e a CLI do Azure.

### <a name="find-billing-profiles-you-have-access-to"></a>Encontrar perfis de cobrança aos quais você tem acesso

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
A resposta da API retorna uma lista de contas de cobrança e os perfis de cobrança delas.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Use a propriedade `displayName` do perfil de cobrança para identificar o perfil de cobrança para o qual deseja verificar o saldo de crédito. Copie a `id` do perfil de cobrança. Por exemplo, caso deseje verificar o saldo de crédito do perfil de cobrança **Desenvolvimento**, copie ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa.

### <a name="get-azure-credit-balance"></a>Obter o saldo de crédito Azure 

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

A resposta da API retorna o saldo estimado e atual do perfil de cobrança.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nome do elemento  | DESCRIÇÃO                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | O valor estimado de créditos que você terá depois de considerar todas as transações cobradas e pendentes. |
| `currentBalance`   | O valor de créditos após a última fatura. Ele não inclui nenhuma transação pendente.    |
| `pendingCreditAdjustments`      | Os ajustes como reembolsos que ainda não foram faturados.  |
| `expiredCredit`      |  O crédito que expirou desde a sua última fatura.  |
| `pendingEligibleCharges`  | Os encargos qualificados para crédito que ainda não foram faturados.   |

### <a name="get-list-of-credits"></a>Obter a lista de créditos

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
A resposta da API retorna listas de créditos Azure de um perfil de cobrança.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nome do elemento  | DESCRIÇÃO                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | O valor original do crédito. |
| `closedBalance`   | O saldo da última fatura.    |
| `source`      | A origem que define quem adquiriu o crédito. |
| `startDate`      |  A data em que o crédito se tornou ativo.  |
| `expirationDate`  | A data em que o crédito expira.   |
| `poNumber`  | O número da ordem de compra da fatura na qual o crédito foi cobrado.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Obter as transações que afetaram o saldo de crédito

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Você precisará passar uma **startDate** e uma **endDate** para obter transações para a duração necessária.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
A resposta da API retorna todas as transações que afetaram o saldo de crédito de seu perfil de cobrança.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nome do elemento  | DESCRIÇÃO                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | A data em que a transação ocorreu. |
| `description` | A descrição da transação. |
| `adjustments`   | Os ajustes de crédito para a transação.    |
| `creditExpired`      | O valor de crédito expirado. |
| `charges`      |  Os encargos da transação.  |
| `closedBalance`  | O saldo após a transação.   |
| `eventType`  | O tipo de transação.   |
| `invoiceNumber`  | O número da fatura na qual a transação é cobrada. Ele estará vazio para a transação pendente.   |

## <a name="how-credits-are-used"></a>Como os créditos são usados

Em uma conta de cobrança para um Contrato de Cliente da Microsoft, você usa perfis de cobrança para gerenciar suas faturas e suas formas de pagamento. Uma fatura mensal é gerada para cada perfil de cobrança e você usa as formas de pagamento para pagar a fatura.

Você atribui créditos adquiridos a um perfil de cobrança. Quando uma fatura é gerada para o perfil de cobrança, os créditos são automaticamente aplicados aos encargos totais para calcular o valor que você precisa pagar. Você paga o valor restante com formas de pagamento como cheque/transferência eletrônica ou cartão de crédito.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não são cobertos pelos créditos Azure

 Os produtos a seguir não são cobertos pelos créditos Azure. Você é cobrado por usar esses produtos, independentemente de seu saldo de crédito:

- Canônico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Usuário registrado
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)
- Produtos do Azure Marketplace
- Planos de Suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entender a conta de cobrança para um Contrato de Cliente da Microsoft](billing-mca-overview.md)
- [Entender os termos em sua fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-invoice.md)
