---
title: Solução de problemas de verificação do editor – Plataforma de identidade da Microsoft | Azure
description: Descreve como solucionar problemas de verificação do editor (versão prévia) para a plataforma de identidade da Microsoft chamando APIs do Microsoft Graph.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: cf886b7b43280e542f1941e7c0edb570868525d9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595745"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Solucionar problemas de verificação do editor (versão prévia)
Se não conseguir concluir o processo ou estiver tendo um comportamento inesperado com a [verificação do editor (versão prévia)](publisher-verification-overview.md), você deverá começar fazendo o seguinte se estiver recebendo erros ou vendo um comportamento inesperado: 

1. Examine os [requisitos](publisher-verification-overview.md#requirements) e certifique-se de que todos foram atendidos.

1. Examine as instruções para [marcar um aplicativo como editor verificado](mark-app-as-publisher-verified.md) e certifique-se de que todas as etapas tenham sido executadas com êxito.

1. Examine a lista de [problemas comuns](#common-issues).

1. Reproduza a solicitação usando o [Explorador do Graph](#making-microsoft-graph-api-calls) para coletar informações adicionais e descartar quaisquer problemas na interface do usuário.

## <a name="common-issues"></a>Problemas comuns
Abaixo estão alguns problemas comuns que podem ocorrer durante o processo. 

- **Não sei minha ID de Microsoft Partner Network (ID de MPN) ou não sei quem é o contato principal para a conta** 
    1. Navegue até a [página de registro do MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Entrar com uma conta de usuário no locatário principal do Azure AD da organização 
    1. Se já existir uma conta do MPN, ela será reconhecida e você será adicionado à conta 
    1. Navegue até a [página de perfil do parceiro](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) onde a ID do MPN e o contato principal da conta serão listados

- **Não sei quem é meu administrador global do Azure AD (também conhecido como administrador da empresa ou administrador de locatários), como posso encontrá-lo? E o administrador de aplicativos ou uma função de administrador diferente?**
    1. Entre no [Portal do Azure AD](https://aad.portal.azure.com) usando uma conta de usuário no locatário principal da sua organização
    1. Navegue até [Gerenciamento de funções](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Clique em “Administrador global” ou na função de administrador desejada
    1. A lista de usuários atribuídos àquela função será exibida

- **Não sei quem é ou quem são os administradores da minha conta do MPN** Vá para a [página de Gerenciamento de usuários do MPN](https://partner.microsoft.com/en-us/pcv/users) e filtre a lista de usuários para ver quais usuários estão nas diferentes funções de administrador.

- **Estou recebendo um erro dizendo que minha ID do MPN é inválida ou que não tenho acesso a ela.**
    1. Acesse o [perfil de parceiro](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) e verifique se: 
        - A ID do MPN está correta. 
        - Não há erros ou “ações pendentes” mostradas, e o status de verificação sob o perfil Negócio jurídico e as informações do Parceiro dizem “autorizado” ou “sucesso”.
    1. Vá para a [página de Gerenciamento de locatários do MPN](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement) e confirme se o locatário no qual o aplicativo está registrado e a conta de usuário que você está usando para entrar estão na lista de locatários associados.
    1. Vá para a [página de Gerenciamento de usuários do MPN](https://partner.microsoft.com/en-us/pcv/users) e confirme se o usuário no qual você está entrando é um administrador global, administrador de MPN ou administrador de contas.

- **Quando eu entro no portal do Azure AD, não vejo nenhum aplicativo registrado. Por quê?** 
    Os registros do aplicativo podem ter sido criados usando uma conta de usuário diferente ou em um locatário diferente. Verifique se você está conectado com a conta correta no locatário onde os registros do aplicativo foram criados.

- **Como faço para saber quem é o proprietário de um registro de aplicativo no Azure AD?** 
    Quando conectado a um locatário onde o aplicativo está registrado, navegue até a folha Registros do aplicativo, clique em um aplicativo e, em seguida, clique em Proprietários.

## <a name="making-microsoft-graph-api-calls"></a>Chamar a API do Microsoft Graph 

Se você está tendo um problema, mas não consegue entender o motivo com base no que você está vendo na interface do usuário, pode ser útil executar outras soluções de problemas através de chamadas do Microsoft Graph para executar as mesmas operações que você pode executar no portal de Registro de aplicativo. Durante a fase de versão prévia, essas APIs só estarão disponíveis no /ponto de extremidade beta do Microsoft Graph.  

A maneira mais fácil de fazer essas solicitações é usando o [Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer). Você também pode considerar outras opções, como usar [Postman](https://www.postman.com/) ou PowerShell para [invocar uma solicitação da Web](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7).  

Você pode usar o Microsoft Graph para definir e remover o editor verificado do seu aplicativo e verificar o resultado depois de executar uma dessas operações. O resultado pode ser visto no objeto de [aplicativo](/graph/api/resources/application?view=graph-rest-beta) correspondente ao registro do aplicativo e a quaisquer [entidades de serviço](/graph/api/resources/serviceprincipal?view=graph-rest-beta) que tenham sido instanciadas a partir desse aplicativo. Para obter mais informações sobre a relação entre esses objetos, consulte: [Objetos de entidade de serviço e aplicativo no Azure Active Directory](app-objects-and-service-principals.md).  

Aqui estão exemplos de algumas solicitações úteis:  

### <a name="set-verified-publisher"></a>Configurar editor verificado 

Solicitação

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Resposta 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* é sua ID do MPN. 

### <a name="unset-verified-publisher"></a>Remover definição de editor verificado 

Solicitação:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Resposta 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Obter informações do editor verificado a partir do aplicativo 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Obter informações do editor verificado a partir da entidade de serviço 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Referência de erro 

Veja a seguir uma lista dos códigos de erro potenciais que você pode receber, seja ao solucionar problemas com Microsoft Graph ou durante o processo no portal de registro de aplicativo.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

A ID de MPN fornecida (<MPNID>) não existe ou você não tem acesso a ela. Forneça uma ID de MPN válida e tente novamente. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

A ID de MPN fornecida (<MPNID>) é inválida. Forneça uma ID de MPN válida e tente novamente. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

A ID de MPN fornecida (<MPNID>) é inválida. Forneça uma ID de MPN válida e tente novamente. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

A ID de MPN (<MPNID>) fornecida não concluiu o processo de verificação. Conclua esse processo no Partner Center e tente novamente. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

A ID de MPN fornecida (<MPNID>) é inválida. Forneça uma ID de MPN válida e tente novamente. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

A ID de MPN fornecida (<MPNID>) é inválida. Forneça uma ID de MPN válida e tente novamente. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

Não é possível localizar o aplicativo de destino (<AppId>). Forneça uma ID de aplicativo válida e tente novamente. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Não há suporte para essa funcionalidade em um locatário do Azure AD B2C. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Não há suporte para essa funcionalidade em um locatário verificado por email. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

O aplicativo de destino (<AppId>) deve ter um conjunto de domínios do editor. Defina um domínio do editor e tente novamente. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

O domínio do editor do aplicativo de destino (<publisherDomain>) não é um domínio verificado neste locatário. Verifique um domínio de locatário usando a verificação de DNS e tente novamente. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

O domínio do editor do aplicativo (<publisherDomain>) não corresponde ao domínio usado para executar a verificação por email no Partner Center (<pcDomain>). Verifique se esses domínios correspondem e tente novamente. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Você não está autorizado a definir a propriedade de editor verificado neste aplicativo (<AppId>) 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

A ID do MPN não foi fornecida no corpo da solicitação ou o tipo de conteúdo da solicitação não era “aplicativo/json”. 

### <a name="msanotsupported"></a>MSANotSupported  

Não há suporte para esse recurso em contas de consumidor da Microsoft. Somente os aplicativos registrados no Azure AD por um usuário do Azure AD têm suporte. 

## <a name="next-steps"></a>Próximas etapas

Se você revisou todas as informações anteriores e ainda continua recebendo um erro de Microsoft Graph, reúna o máximo possível das informações a seguir relacionadas à solicitação com falha e [entre em contato com o suporte da Microsoft](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- ObjectID ou UserPrincipalName do usuário conectado 
- ObjectId do aplicativo de destino
- AppId do aplicativo de destino
- Tenantid onde o aplicativo está registrado
- ID MPN
- Solicitação REST sendo feita 
- Código de erro e mensagem sendo retornada 
