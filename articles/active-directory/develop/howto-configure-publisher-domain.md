---
title: Configure o domínio do editor de um aplicativo | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o domínio do editor de um aplicativo para que os usuários saibam para onde suas informações estão sendo enviadas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697125"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Como: Configurar o domínio do editor de um aplicativo

O domínio do editor de um aplicativo é exibido aos usuários no [prompt de consentimento do aplicativo](application-consent-experience.md) para informar aos usuários para onde suas informações estão sendo enviadas. Aplicativos multilocatários que são registrados após 21 de maio de 2019 que não possuem um domínio de editor aparecem como **não verificados**. Aplicativos multilocatários são aplicativos que suportam contas fora de um único diretório organizacional; por exemplo, suporte a todas as contas Azure AD ou suporte a todas as contas Azure AD e contas pessoais da Microsoft.

## <a name="new-applications"></a>Novos aplicativos

Quando você registra um novo aplicativo, o domínio do editor do seu aplicativo pode ser definido como um valor padrão. O valor depende de onde o aplicativo está registrado, particularmente se o aplicativo está registrado em um inquilino e se o inquilino tem domínios verificados.

Se houver domínios verificados pelo inquilino, o domínio do editor do aplicativo será padrão para o domínio verificado principal do inquilino. Se não houver domínios verificados pelo inquilino (que é o caso quando o aplicativo não está registrado em um inquilino), o domínio do editor do aplicativo será definido como nulo.

A tabela a seguir resume o comportamento padrão do valor do domínio do editor.  

| Domínios verificados por inquilinos | Valor padrão do domínio do editor |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primária) | domain2.com |

Se o domínio do editor de um aplicativo de vários locatários não for definido ou se ele estiver definido como um domínio que termina em .onmicrosoft.com, o prompt de consentimento do aplicativo será exibido **não verificado** no lugar do domínio do editor.

## <a name="grandfathered-applications"></a>Aplicações de avô

Se o aplicativo foi registrado antes de 21 de maio de 2019, o prompt de consentimento do aplicativo não será **verificado** se você não tiver definido um domínio de editor. Recomendamos que você defina o valor do domínio do editor para que os usuários possam ver essas informações no prompt de consentimento do seu aplicativo.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configure o domínio do editor usando o portal Azure

Para definir o domínio do editor do seu aplicativo, siga essas etapas.

1. Faça login no [portal do Azure](https://portal.azure.com) usando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.

1. Se sua conta estiver presente em mais de um locatário do Azure AD:
   1. Selecione seu perfil no menu no canto superior direito da página e, em seguida, escolha **Alternar diretório**.
   1. Altere a sessão para o locatário do Azure AD no qual você deseja criar seu aplicativo.

1. Navegue até [o Azure Active Directory > registros do app](https://go.microsoft.com/fwlink/?linkid=2083908) para encontrar e selecionar o aplicativo que deseja configurar.

   Depois de selecionar o aplicativo, você verá a página **Visão Geral** do aplicativo.

1. Na página **Visão Geral** do aplicativo, selecione a seção **Marca.**

1. Encontre o campo **de domínio do Publisher** e selecione uma das seguintes opções:

   - Selecione **Configurar um domínio** se você ainda não configurou um domínio.
   - Selecione **O domínio Atualizar** se um domínio já tiver sido configurado.

Se o aplicativo estiver registrado em um inquilino, você verá duas guias para selecionar: **Selecione um domínio verificado** e **Verifique um novo domínio**.

Se o seu aplicativo não estiver registrado em um inquilino, você só verá a opção de verificar um novo domínio para o seu aplicativo.

### <a name="to-verify-a-new-domain-for-your-app"></a>Para verificar um novo domínio para o seu aplicativo

1. Crie um `microsoft-identity-association.json` arquivo nomeado e cole o seguinte trecho de código JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Substitua o espaço reservado *{YOUR-APP-ID-HERE}* pelo ID do aplicativo (cliente) que corresponde ao seu aplicativo.

1. Hospede o `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`arquivo em: . Substitua o espaço reservado *{SEU-DOMÍNIO-AQUI}* para corresponder ao domínio verificado.

1. Clique no botão **Verificar e salvar o domínio.**

### <a name="to-select-a-verified-domain"></a>Para selecionar um domínio verificado

- Se o inquilino tiver verificado domínios, selecione um dos domínios do **Select a verificação** de domínio saistino.

>[!Note]
> O cabeçalho 'Tipo de conteúdo' `application/json`esperado que deve ser devolvido é . Você pode obter um erro como mencionado abaixo se você usar qualquer outra coisa como`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicações no prompt de consentimento do aplicativo

A configuração do domínio do editor tem um impacto sobre o que os usuários veem no prompt de consentimento do aplicativo. Para entender completamente os componentes do prompt de consentimento, consulte [Entendendo as experiências de consentimento do aplicativo.](application-consent-experience.md)

A tabela a seguir descreve o comportamento dos aplicativos criados antes de 21 de maio de 2019.

![Solicitação de consentimento para aplicativos criados antes de 21 de maio de 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

O comportamento de novos aplicativos criados após 21 de maio de 2019 dependerá do domínio do editor e do tipo de aplicação. A tabela a seguir descreve as alterações que você deve esperar ver com as diferentes combinações de configurações.

![Solicitação de consentimento para aplicativos criados após 21 de maio de 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicações no redirecionamento de URIs

Os aplicativos que fazem login em usuários com qualquer conta de trabalho ou escola, ou contas pessoais da Microsoft[(multi-inquilino)](single-and-multi-tenant-apps.md)estão sujeitos a poucas restrições ao especificar URIs de redirecionamento.

### <a name="single-root-domain-restriction"></a>Restrição de domínio raiz única

Quando o valor de domínio do editor para aplicativos multilocatários é definido como nulo, os aplicativos ficam restritos a compartilhar um único domínio raiz para os URIs de redirecionamento. Por exemplo, a seguinte combinação de valores não é permitida porque o domínio raiz, contoso.com, não corresponde a fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrições de subdomínio

Subdomínios são permitidos, mas você deve registrar explicitamente o domínio raiz. Por exemplo, enquanto as URIs seguintes compartilham um único domínio raiz, a combinação não é permitida.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

No entanto, se o desenvolvedor adicionar explicitamente o domínio raiz, a combinação é permitida.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Exceções

Os seguintes casos não estão sujeitos à restrição de domínio raiz única:

- Aplicativos de inquilino único ou aplicativos que visam contas em um único diretório
- Uso do host local como URIs de redirecionamento
- Redirecionar URIs com esquemas personalizados (não HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configure o domínio do editor de forma programática

Atualmente, não há suporte a Rest API ou PowerShell para configurar o domínio do editor de forma programática.
