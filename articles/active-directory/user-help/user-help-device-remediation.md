---
title: Correções para o erro "você não pode chegar lá daqui" – Azure AD
description: Encontre possíveis correções por que você está recebendo a mensagem de erro "você não pode chegar lá daqui".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: d29168f154c80eb2c66d0316f773375325ded67f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062176"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Possíveis correções para a mensagem de erro "você não pode chegar lá daqui"

Ao acessar a aplicativos Web ou serviços internos da sua organização, você pode receber uma mensagem de erro que diz **Você não pode acessar esse lugar daqui**. Essa mensagem significa que sua organização aplicou uma política que está impedindo o dispositivo de acessar recursos da organização. Talvez você precise entrar em contato com o suporte técnico para corrigir esse problema, mas consulte aqui algumas coisas que você pode tentar primeiro.

## <a name="make-sure-youre-using-a-supported-browser"></a>Verifique se você está usando um navegador com suporte
Se você receber a mensagem **Você não pode acessar esse lugar daqui** informando que está tentando acessar sites da organização por meio de um navegador sem suporte, verifique qual navegador você está executando.

![Mensagem de erro relacionada ao suporte ao navegador](media/user-help-device-remediation/browser-version.png)

Para corrigir esse problema, você deve instalar e executar um navegador com suporte, com base em seu sistema operacional. Se você está usando o Windows 10, os navegadores com suporte incluem o Microsoft Edge, o Internet Explorer e o Google Chrome. Se você estiver usando um sistema operacional diferente, verifique a lista completa dos [navegadores compatíveis](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Verifique se você está usando um sistema operacional compatível
Verifique se você está executando uma versão compatível do sistema operacional, incluindo:

- **Windows Client.** Windows 7 ou posterior.

- **Windows Server.** Windows Server 2008 R2 ou posterior.

- **macOS.** macOS X ou posterior

- **Android e iOS.** Versão mais recente de sistemas operacionais móveis Android e iOS

Para corrigir esse problema, instale e execute um sistema operacional compatível.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Verifique se o dispositivo está adicionado à sua rede
Se você receber a mensagem **Você não pode acessar esse lugar daqui** para informar que o dispositivo não está em conformidade com a política de acesso da organização, verifique se você o adicionou à rede da organização.

![Mensagem de erro relacionada à conexão à rede](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Para verificar se o dispositivo foi adicionado à sua rede
1. Entre no Windows usando sua conta corporativa ou de estudante. Por exemplo, alain@contoso.com.

2. Conecte-se à rede corporativa por meio de uma VPN (rede virtual privada) ou DirectAccess.

3. Após a conexão, pressione a **tecla de logotipo do Windows + a tecla L** para bloquear seu dispositivo.

4. Desbloqueie o dispositivo usando sua conta corporativa ou de estudante e, depois, tente acessar de novo o aplicativo ou serviço problemático.

    Se você vir a mensagem de erro **Você não pode acessar esse lugar daqui** novamente, selecione o link **Mais detalhes** e entre em contato com a Assistência técnica com os detalhes.

### <a name="to-join-your-device-to-your-network"></a>Para adicionar seu dispositivo à rede
Se o dispositivo não estiver adicionado à rede da sua organização, você terá duas opções:

- **Adicionar seu dispositivo de trabalho.** Junte-se ao seu dispositivo Windows 10 de propriedade de trabalho à rede da sua organização para poder acessar recursos possivelmente restritos. Para saber mais e obter instruções passo a passo, consulte [Adicionar seu dispositivo corporativo à rede da sua organização](user-help-join-device-on-network.md).

- **Registrar seu dispositivo pessoal para trabalho.** Registre seu dispositivo pessoal, geralmente um telefone ou tablet, na rede da sua organização. Após o registro do dispositivo, ele poderá acessar os recursos restritos da sua organização. Para saber mais e obter instruções passo a passo, consulte [Registrar seu dispositivo pessoal à rede da sua organização](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Próximas etapas
- [O que é o portal MyApps?](active-directory-saas-access-panel-introduction.md)

- [Entre com seu telefone, não com sua senha](user-help-auth-app-sign-in.md)
