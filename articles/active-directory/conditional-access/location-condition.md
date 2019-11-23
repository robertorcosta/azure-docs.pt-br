---
title: Location condition in Azure Active Directory Conditional Access
description: Saiba como usar a condição de localização para controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380292"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>What is the location condition in Azure Active Directory Conditional Access? 

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users can access your cloud apps. The location condition of a Conditional Access policy enables you to tie access controls settings to the network locations of your users.

Este artigo fornece as informações necessárias para configurar a condição de localização.

## <a name="locations"></a>Locais

Azure AD enables single sign-on to devices, apps, and services from anywhere on the public internet. Com a condição de localização, você pode controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário. Casos de uso comuns para a condição de localização são:

- Exigir autenticação multifator para usuários que acessam um serviço quando estão fora da rede corporativa.
- Bloqueando o acesso para usuários que acessam um serviço de países e regiões específicas.

A location is a label for a network location that either represents a named location or multi-factor authentication Trusted IPs.

## <a name="named-locations"></a>Localizações nomeadas

With named locations, you can create logical groupings of IP address ranges or countries and regions.

You can access your named locations in the **Manage** section of the Conditional Access page.

![Named locations in Conditional Access](./media/location-condition/02.png)

A localização nomeada tem os seguintes componentes:

![Create a new named location](./media/location-condition/42.png)

- **Nome** – O nome de exibição de uma localização nomeada.
- **Intervalos de IP**: um ou mais intervalos de endereço IPv4 no formato CIDR. Specifying an IPv6 address range is not supported.

   > [!NOTE]
   > IPv6 address ranges cannot currently be included in a named location. This means IPv6 ranges cannot be excluded from a Conditional Access policy.

- **Marcar como local confiável** – Um sinalizador que você pode definir para uma localização nomeada indicar um local confiável. Normalmente, os locais confiáveis são áreas de rede controladas pelo departamento de TI. In addition to Conditional Access, trusted named locations are also used by Azure Identity Protection and Azure AD security reports to reduce [false positives](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Países/regiões** - esta opção permite que você selecione um ou mais países ou regiões para definir uma localização nomeada.
- **Include unknown areas** - Some IP addresses are not mapped to a specific country or region. Esta opção permite que você escolha se esses endereços IP devem ser incluídos na localização nomeada. Use essa configuração quando a política usando a localização nomeada deve aplicar-se a localizações desconhecidas.

O número de localizações que você pode configurar é restrito pelo tamanho do objeto relacionado no Azure AD. You can configure locations based on of the following limitations:

- Uma localização nomeada com até 1200 intervalos de IP.
- Um máximo de 90 localizações nomeadas com um intervalo IP atribuído a cada um delas.

Conditional Access policy applies to IPv4 and IPv6 traffic. Currently named locations do not allow IPv6 ranges to be configured. This limitation causes the following situations:

- Conditional Access policy cannot be targeted to specific IPv6 ranges
- Conditional Access policy cannot exclude specific IPV6 ranges

If a policy is configured to apply to “Any location”, it will apply to IPv4 and IPv6 traffic. Named locations configured for specified countries and regions only support IPv4 addresses. IPv6 traffic is only included if the option to “include unknown areas” selected.

## <a name="trusted-ips"></a>IPs confiáveis

Você também pode configurar intervalos de endereços IP que representam a Intranet local da sua organização nas [configurações do serviço de autenticação multifator](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esse recurso permite que você configure até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. For more information, see [Trusted IPs](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

If you have Trusted IPs configured, they show up as **MFA Trusted IPS** in the list of locations for the location condition.

### <a name="skipping-multi-factor-authentication"></a>Ignorando a autenticação multifator

Na página de configurações do serviço de autenticação multifator, você pode identificar os usuários da Intranet corporativa selecionando **Ignorar autenticação multifator para solicitações de usuários federados na minha Intranet**. Essa configuração indica que a declaração de rede corporativa interna, que é emitida pelo AD FS, deve ser confiável e usada para identificar o usuário como estando na rede corporativa. For more information, see [Enable the Trusted IPs feature by using Conditional Access](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

After checking this option, including the named location **MFA Trusted IPS** will apply to any policies with this option selected.

For mobile and desktop applications, which have long lived session lifetimes, Conditional Access is periodically reevaluated. O padrão é uma vez a cada hora. Quando a declaração da rede corporativa interna é emitida somente no momento da autenticação inicial, o Microsoft Azure AD pode não ter uma lista de intervalos de IP confiáveis. Nesse caso, é mais difícil determinar se o usuário ainda está na rede corporativa:

1. Verifique se o endereço IP do usuário está em um dos intervalos de IP confiáveis.
2. Check whether the first three octets of the user’s IP address match the first three octets of the IP address of the initial authentication. The IP address is compared with the initial authentication when the inside corporate network claim was originally issued and the user location was validated.

Se ambas as etapas falharem, um usuário é considerado como não estando em um IP confiável.

## <a name="location-condition-configuration"></a>Configuração de condição de localização

Quando você configurar a condição de localização, você tem a opção de fazer a distinção entre:

- Qualquer local
- Todos os locais confiáveis
- Locais selecionados

![Configuração de condição de localização](./media/location-condition/01.png)

### <a name="any-location"></a>Qualquer local

Por padrão, selecionar **Qualquer local** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Essa configuração não está limitada aos endereços IP que você configurou como localização nomeada. Quando seleciona **Qualquer local**, você ainda pode excluir locais específicos de uma política. Por exemplo, você pode aplicar uma política para todos os locais confiáveis de exceções de locais para definir o escopo para todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais confiáveis

Esta opção se aplica a:

- Todos os locais que foram marcados como local confiável
- **IPs confiáveis de MFA** (se configurado)

### <a name="selected-locations"></a>Locais selecionados

Com essa opção, você pode selecionar uma ou mais localizações nomeadas. Para uma política com essa configuração a ser aplicada, um usuário precisa se conectar de qualquer um dos locais selecionados. Quando você clica em **Selecionar**, é aberto o controle de seleção da rede nomeada que mostra a lista de redes nomeadas. A lista também mostra se o local de rede foi marcado como confiável. A localização nomeada chamada **IPs confiáveis de MFA** é usada para incluir as configurações de IP podem ser configuradas na página de configuração do serviço de autenticação multifator.

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="when-is-a-location-evaluated"></a>Quando um local é avaliado?

Conditional Access policies are evaluated when:

- Um usuário inicialmente entra em um aplicativo Web, aplicativo da área de trabalho ou móvel.
- Um aplicativo da área de trabalho ou móvel que usa autenticação moderna, usa um token de atualização para adquirir um novo token de acesso. By default this check is once an hour.

This check means for mobile and desktop applications using modern authentication, a change in location would be detected within an hour of changing the network location. Para aplicativos da área de trabalho e móveis que não utilizam autenticação moderna, a política é aplicada em cada solicitação de token. A frequência da solicitação pode variar de acordo com o aplicativo. Da mesma forma, para aplicativos Web, a política é aplicada no início da entrada e é adequada para o tempo de vida da sessão no aplicativo Web. Devido às diferenças nos tempos de vida da sessão em todos os aplicativos, o tempo entre a avaliação de política também varia. Sempre que o aplicativo solicitar um novo token de entrada, a política será aplicada.

Por padrão, o Microsoft Azure AD emite um token de hora em hora. Depois de remover a rede corporativa, em uma hora a política é aplicada para aplicativos que usam autenticação moderna.

### <a name="user-ip-address"></a>Endereço IP do usuário

O endereço IP que é usado na avaliação de política é o endereço IP público do usuário. For devices on a private network, this IP address is not the client IP of the user’s device on the intranet, it is the address used by the network to connect to the public internet.

> [!WARNING]
> If your device has only an IPv6 address, configuring the location condition is not supported.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento em massa e download de localizações nomeadas

Quando você cria ou atualiza localizações nomeadas, para atualizações em massa, você pode carregar ou fazer o download de um arquivo CSV com os intervalos de IP. Um carregamento substitui os intervalos de IP na lista com aqueles do arquivo. Cada linha do arquivo contém um intervalo de endereços IP no formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies e VPNs na nuvem

Quando você usa um proxy ou solução VPN hospedado na nuvem, o endereço IP que o Microsoft Azure AD usa ao avaliar uma política é o endereço IP do proxy. O cabeçalho de X-Forwarded-For (XFF) que contém o endereço IP público dos usuários não é usado porque não há validações que venham de uma fonte confiável, portanto apresentaria um método para simular um endereço IP.

Quando um proxy na nuvem está em uso, uma política que é usada para solicitar um dispositivo ingressado no domínio pode ser usada, ou a declaração corpnet interna do AD FS.

### <a name="api-support-and-powershell"></a>Suporte à API e PowerShell

API and PowerShell is not yet supported for named locations, or for Conditional Access policies.

## <a name="next-steps"></a>Próximos passos

- Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md).
