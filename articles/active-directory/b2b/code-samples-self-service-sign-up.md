---
title: Exemplos de código do conector de API para fluxos dos usuários – Azure AD
description: Exemplos de código para conectores de API em fluxos de inscrição por autoatendimento para Identidades Externas do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0814312bb12582dd9e9ebfafc60fba470f6a9a9
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905150"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Exemplos para inscrição por autoatendimento de Identidades Externas

As tabelas a seguir fornecem links para exemplos de código para aproveitar as APIs Web em seus fluxos dos usuários de inscrição por autoatendimento usando [conectores de API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Guias de início rápido da Função do Azure do conector de API

| Amostra                                                                                                                          | Descrição                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Este exemplo de Função do Azure do .NET Core demonstra como limitar os inscrições a domínios de locatário específicos e validar as informações fornecidas pelo usuário. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Este exemplo de Função do Azure do Node.js demonstra como limitar os inscrições a domínios de locatário específicos e validar as informações fornecidas pelo usuário.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Este exemplo de Função do Azure do Python demonstra como limitar os inscrições a domínios de locatário específicos e validar as informações fornecidas pelo usuário.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Fluxos de trabalho de aprovação personalizados

| Amostra | Descrição |
|--------| ----------- |
| [Fluxo de trabalho de aprovação manual](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Este exemplo demonstra um fluxo de trabalho de aprovação de ponta a ponta para gerenciar a criação de conta de usuário convidado na inscrição por autoatendimento |

## <a name="identity-verification"></a>Verificação de identidade

| Amostra                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Este exemplo mostra como verificar uma identidade de usuário como parte de sua inscrição por autoatendimento usando um conector de API para integrar ao IDology. |

<!-- | [Experian](https://github.com/Azure-Samples/) | This sample shows how add identity verification to your self-service sign-up user flow by using an API connector to integrate with Experian. | -->
