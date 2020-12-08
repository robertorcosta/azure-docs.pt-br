---
title: Sincronização de entrada para provisionamento de nuvem usando o MS API do Graph
description: Este tópico descreve como habilitar a sincronização de entrada usando apenas o API do Graph
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f308f46fc021a1d08f4065d48558a6dd71786c7c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860348"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Sincronização de entrada para provisionamento de nuvem usando o MS API do Graph

O documento a seguir descreve como replicar um perfil de sincronização do zero usando apenas APIs MSGraph.  
A estrutura de como fazer isso consiste nas etapas a seguir.  Elas são:

- [Configuração básica](#basic-setup)
- [Criar entidades de serviço](#create-service-principals)
- [Criar trabalho de sincronização](#create-sync-job)
- [Atualizar domínio de destino](#update-targeted-domain)
- [Iniciar trabalho de sincronização](#start-sync-job)
- [Status da revisão](#review-status)

Use esses [módulo Microsoft Azure Active Directory para Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) comandos para habilitar a sincronização para um locatário de produção, um pré-requisito para poder chamar o serviço Web de administração para esse locatário.

## <a name="basic-setup"></a>Configuração básica

### <a name="enable-tenant-flags"></a>Habilitar sinalizadores de locatário

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
O primeiro desses dois comandos exige Azure Active Directory credenciais. Esses commandlets identificam implicitamente o locatário e os habilitam para sincronização.

## <a name="create-service-principals"></a>Criar entidades de serviço
Em seguida, precisamos criar a [entidade de serviço/aplicativo AD2AAD](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Você precisa usar essa ID de aplicativo 1a4721b3-e57f-4451-ae87-ef078703ec94. O displayName é a URL de domínio do AD, se usada no portal (por exemplo, contoso.com), mas pode ser nomeada outra coisa.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Criar trabalho de sincronização
A saída do comando acima retornará o objectId da entidade de serviço que foi criada. Para este exemplo, o objectId é 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Use Microsoft Graph para adicionar um synchronizationJob à entidade de serviço.  

A documentação para criar um trabalho de sincronização pode ser encontrada [aqui](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

Se você não registrou a ID acima, você pode encontrar a entidade de serviço executando a seguinte chamada do MS Graph. Você precisará de Directory. Read. All Permissions para fazer essa chamada:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Em seguida, procure o nome do aplicativo na saída.

Execute os dois comandos a seguir para criar dois trabalhos: um para provisionamento de usuário/grupo e outro para sincronização de hash de senha. É a mesma solicitação duas vezes, mas com IDs de modelo diferentes.


Chame as duas solicitações a seguir:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Você precisará de duas chamadas se quiser criar ambas.

Exemplo de valor de retorno (para provisionamento):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Atualizar domínio de destino
Para esse locatário, o identificador de objeto e o identificador de aplicativo da entidade de serviço são os seguintes:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Vamos precisar atualizar o domínio para o qual essa configuração está sendo direcionada, portanto, atualize os segredos para esse domínio.

Verifique se o nome de domínio usado é a mesma URL que você definiu para o controlador de domínio local

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

A resposta esperada é... HTTP 204/sem conteúdo

Aqui, o valor de "domínio" realçado é o nome do domínio de Active Directory local do qual as entradas devem ser provisionadas para Azure Active Directory.

## <a name="start-sync-job"></a>Iniciar trabalho de sincronização
O trabalho pode ser recuperado novamente por meio do seguinte comando:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

A documentação para recuperar trabalhos pode ser encontrada [aqui](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Para iniciar o trabalho, emita essa solicitação, usando o objectId da entidade de serviço criada na primeira etapa e o identificador do trabalho retornado da solicitação que criou o trabalho.

A documentação sobre como iniciar um trabalho pode ser encontrada [aqui](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

A resposta esperada é... HTTP 204/sem conteúdo.

Outros comandos para controlar o trabalho são documentados [aqui](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
Para reiniciar um trabalho, ele usaria...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Status da revisão
Recupere seus status de trabalho via...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Procure na seção ' status ' do objeto de retorno para obter detalhes relevantes

## <a name="next-steps"></a>Próximas etapas 

- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
- [Transformações](how-to-transformation.md)
- [API de sincronização do Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
