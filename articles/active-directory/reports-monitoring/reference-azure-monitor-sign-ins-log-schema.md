---
title: Esquema de log de login no Monitor Azure | Microsoft Docs
description: Descreva o sinal azure AD no esquema de log para uso no Monitor Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748669"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interprete o esquema de logs de login do Azure AD no Monitor Do Azure

Este artigo descreve o esquema de log de logon do Microsoft Azure Active Directory no Azure Monitor. A maioria das informações relacionadas aos logins é fornecida sob o atributo *Propriedades* do `records`objeto.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Descrições de campo

| Nome do campo | Descrição |
|------------|-------------|
| Hora | Data e hora em UTC. |
| ResourceId | Esse valor não é mapeado e você pode ignorar esse campo com segurança.  |
| OperationName | Para inscrições, esse valor é sempre *Atividade de login*. |
| OperationVersion | Versão da API REST solicitada pelo cliente. |
| Categoria | Para inscrições, esse valor é sempre *Atividade de login*. | 
| TenantId | O GUID de locatário associado aos logs. |
| ResultType | O resultado da operação de login pode ser *Sucesso* ou *Falha*. | 
| ResultSignature | Contém o código de erro, se houver, para a operação de entrada. |
| ResultDescription | Fornece a descrição do erro para a operação de entrada. |
| riscoDetalhe | riscoDetalhe | Fornece a "razão" por trás de um estado específico de um usuário arriscado, login ou detecção de risco. Os valores `none`possíveis `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange`são: `adminConfirmedSigninSafe` `aiConfirmedSigninSafe`, `userPassedMFADrivenByRiskBasedPolicy` `adminDismissedAllRiskForUser`, `adminConfirmedSigninCompromised` `unknownFutureValue`, `userPerformedSecuredPasswordReset`, , , , , , , . . O `none` valor significa que nenhuma ação foi realizada no usuário ou login até agora. <br>**Nota:** Os detalhes desta propriedade exigem uma licença Azure AD Premium P2. Outras licenças `hidden`devolvem o valor. |
| riskEventTypes | riskEventTypes | Tipos de detecção de risco associados ao login. Os valores `unlikelyTravel`possíveis `anonymizedIPAddress` `maliciousIPAddress`são: `malwareInfectedIPAddress` `suspiciousIPAddress`, `leakedCredentials` `investigationsThreatIntelligence`, `generic`, `unknownFutureValue` `unfamiliarFeatures`, , , , , , e . |
| riscoLevelAgregado | riscoNível | Nível de risco agregado. Os valores `none`possíveis `low` `medium`são: , , , `high`, `hidden`e . `unknownFutureValue` O `hidden` valor significa que o usuário ou login não estava habilitado para a Proteção de Identidade Ad do Azure. **Nota:** Os detalhes desta propriedade estão disponíveis apenas para clientes Azure AD Premium P2. Todos os outros clientes serão devolvidos. `hidden` |
| nível de riscoDuranteSignIn | riscoNível | Nível de risco durante a entrada. Os valores `none`possíveis `low` `medium`são: , , , `high`, `hidden`e . `unknownFutureValue` O `hidden` valor significa que o usuário ou login não estava habilitado para a Proteção de Identidade Ad do Azure. **Nota:** Os detalhes desta propriedade estão disponíveis apenas para clientes Azure AD Premium P2. Todos os outros clientes serão devolvidos. `hidden` |
| riscoEstado | riscoEstado | Relata o status do usuário de risco, o login ou a detecção de riscos. Os valores `none`possíveis `confirmedSafe`são: `atRisk` `confirmedCompromised`, `unknownFutureValue`, `remediated`, `dismissed`, , , . . |
| DurationMs |  Esse valor não é mapeado e você pode ignorar esse campo com segurança. |
| CallerIpAddress | Endereço IP do cliente que fez a solicitação. | 
| CorrelationId | GUID opcional passado pelo cliente. Esse valor pode ajudar a correlacionar operações do lado do cliente com operações do lado do servidor e é útil ao rastrear logs que abrangem os serviços. |
| Identidade | A identidade do token que foi apresentada ao fazer a solicitação. Pode ser uma conta de usuário, conta do sistema ou principal de serviço. |
| Nível | Fornece o tipo de mensagem. Para auditoria, é sempre *informativo*. |
| Location | Fornece o local da atividade de entrada. |
| Propriedades | Lista todas as propriedades associadas a logins. Para obter mais informações, consulte [API Reference do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Esse esquema usa os mesmos nomes de atributos como no recurso de entrada, para legibilidade.

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Saiba mais sobre os Logs de Diagnóstico do Azure](../../azure-monitor/platform/platform-logs-overview.md)
