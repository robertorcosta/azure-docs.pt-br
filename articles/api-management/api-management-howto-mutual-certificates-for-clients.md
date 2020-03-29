---
title: APIs seguras usando autenticação de certificado de cliente no gerenciamento de API
titleSuffix: Azure API Management
description: Saiba como proteger o acesso às APIs usando certificados do cliente
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713141"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs usando a autenticação de certificado do cliente no Gerenciamento de API

O Gerenciamento de API fornece a capacidade de proteger o acesso às APIs (isto é, cliente para Gerenciamento de API) usando certificados do cliente. Você pode validar o certificado de entrada e verificar as propriedades do certificado em relação aos valores desejados usando expressões de diretiva.

Para obter informações sobre como garantir acesso ao serviço back-end de uma API usando certificados de cliente (ou seja, Gerenciamento de API para back-end), consulte [Como proteger serviços back-end usando a autenticação](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) de certificados do cliente

> [!IMPORTANT]
> Para receber e verificar os certificados do cliente em HTTP/2 nos níveis Desenvolvedor, Básico, Padrão ou Premium, você deve ativar a configuração "Negociar certificado cliente" na lâmina "Domínios personalizados", conforme mostrado abaixo.

![Negociar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Para receber e verificar os certificados do cliente na camada Consumo, você deve ativar a configuração "Solicitar certificado cliente" na lâmina "Domínios personalizados", conforme mostrado abaixo.

![Solicitar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Verificando o emissor e a entidade

As políticas abaixo podem ser configuradas para verificar o emissor e a entidade de um certificado do cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desativar o uso `context.Request.Certificate.VerifyNoRevocation()` da lista `context.Request.Certificate.Verify()`de revogação do certificado de verificação em vez de .
> Se o certificado do cliente for auto-assinado, os certificados CA raiz (ou `context.Request.Certificate.VerifyNoRevocation()` intermediário) devem ser [carregados](api-management-howto-ca-certificates.md) para a API Management `context.Request.Certificate.Verify()` para e para trabalhar.

## <a name="checking-the-thumbprint"></a>Verificando a impressão digital

Veja abaixo que as políticas podem ser configuradas para verificar a impressão digital de um certificado do cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desativar o uso `context.Request.Certificate.VerifyNoRevocation()` da lista `context.Request.Certificate.Verify()`de revogação do certificado de verificação em vez de .
> Se o certificado do cliente for auto-assinado, os certificados CA raiz (ou `context.Request.Certificate.VerifyNoRevocation()` intermediário) devem ser [carregados](api-management-howto-ca-certificates.md) para a API Management `context.Request.Certificate.Verify()` para e para trabalhar.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificação de uma impressão digital em relação a certificados carregados no Gerenciamento de API

O exemplo a seguir mostra como verificar a impressão digital de um certificado do cliente em relação a certificados carregados no Gerenciamento de API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Para desativar o uso `context.Request.Certificate.VerifyNoRevocation()` da lista `context.Request.Certificate.Verify()`de revogação do certificado de verificação em vez de .
> Se o certificado do cliente for auto-assinado, os certificados CA raiz (ou `context.Request.Certificate.VerifyNoRevocation()` intermediário) devem ser [carregados](api-management-howto-ca-certificates.md) para a API Management `context.Request.Certificate.Verify()` para e para trabalhar.

> [!TIP]
> A questão do impasse do certificado do cliente descrito neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode se `403 Forbidden` manifestar de várias maneiras, por exemplo, solicitações congelam, solicitações resultam em código de status após o tempo de cronometragem, `context.Request.Certificate` é `null`. Esse problema geralmente `POST` `PUT` afeta e solicita com um comprimento de conteúdo de aproximadamente 60KB ou maior.
> Para evitar que esse problema ocorra, ative a configuração "Negociar certificado cliente" para os nomes de host desejados na lâmina "Domínios personalizados", conforme mostrado abaixo. Este recurso não está disponível na camada Consumo.

![Negociar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Próximas etapas

-   [Como garantir serviços de back-end usando autenticação de certificado do cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
