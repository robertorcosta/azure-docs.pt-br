---
title: Planejar e solucionar problemas no nome do princípio do usuário do Azure (UPN) altera
description: Entenda problemas e atenuações conhecidas para mudanças na UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743327"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planejar e solucionar problemas do nome principal do usuário no Diretório Ativo do Azure

Um Nome Principal de Usuário (UPN) é um atributo que é um padrão de comunicação na Internet para contas de usuários. Um UPN consiste em um prefixo UPN (o nome da conta de usuário) e um sufixo UPN (um nome de domínio de DNS). O prefixo se junta ao sufixo usando o símbolo "@". Por exemplo, someone@example.com. Um UPN deve ser exclusivo entre todos os objetos de entidade de segurança em uma floresta de diretórios. 

> [!NOTE]
> Para desenvolvedores, recomendamos que você use o objectID do usuário como o identificador imutável, em vez de UPN. Se seus aplicativos estiverem usando upn no momento, recomendamos configurar o UPN para combinar com o endereço de e-mail principal do usuário para melhorar sua experiência.<br> **Em um ambiente híbrido, é importante que a UPN para um usuário seja idêntica no diretório local e no Azure Active Directory**.

**Este artigo pressupõe que você está usando upn como identificador de usuário. Ele aborda o planejamento de alterações de UPN e a recuperação de problemas que podem resultar de alterações de UPN.**

## <a name="learn-about-upns-and-upn-changes"></a>Conheça as alterações de UPNs e UPN
As páginas de login geralmente solicitam que os usuários insiram seu endereço de e-mail quando o valor necessário é, na verdade, sua UPN. Portanto, você deve ter certeza de alterar a UPN dos usuários sempre que seu endereço de e-mail principal for alterado.

Os endereços de e-mail principais dos usuários podem mudar por muitas razões:

* rebranding empresa

* funcionários se movendo para diferentes divisões da empresa 

* fusões e aquisições

* mudanças de nome do funcionário

### <a name="types-of-upn-changes"></a>Tipos de alterações de UPN

Você pode alterar um UPN alterando o prefixo, o sufixo ou ambos.

* **Alterando o prefixo**.

   *  Por exemplo, se o nome de uma pessoa for alterado, você pode alterar o nome da conta:  
BSimon@contoso.com paraBJohnson@contoso.com

   * Você também pode alterar o padrão corporativo para prefixos:  
Bsimon@contoso.com paraBritta.Simon@contoso.com

* **Mudando o sufixo**. <br>

    Por exemplo, se uma pessoa mudou de divisão, você pode mudar seu domínio: 

   * Britta.Simon@contoso.com em Britta.Simon@contosolabs.com <br>
     Ou<br>
    * Britta.Simon@corp.contoso.com em Britta.Simon@labs.contoso.com 

Altere a UPN do usuário toda vez que o endereço de e-mail principal de um usuário for atualizado. Não importa o motivo da alteração de e-mail, a UPN deve estar sempre atualizada para corresponder.

Durante a sincronização inicial do Active Directory para o Azure AD, certifique-se de que os e-mails dos usuários sejam idênticos aos seus UPNs.

### <a name="upns-in-active-directory"></a>UPNs em Diretório Ativo

No Active Directory, o sufixo UPN padrão é o nome DNS do domínio onde você criou a conta de usuário. Na maioria dos casos, este é o nome de domínio que você registra como domínio corporativo na internet. Se você criar a conta de usuário no domínio contoso.com, o UPN padrão será

username@contoso.com

 No entanto, você pode [adicionar mais sufixos UPN](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) usando domínios e trusts do Active Directory. 

Por exemplo, você pode querer adicionar labs.contoso.com e pedir que as UPNs e o e-mail dos usuários reflitam isso. Eles se tornariam, então,

username@labs.contoso.com.

>[!IMPORTANT]
> Se as UPNs no diretório Ativo e o Diretório Ativo do Azure não corresponderem, surgirão problemas. Se você estiver [alterando o sufixo no Active Directory,](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)você deve garantir que um nome de domínio personalizado correspondente tenha sido [adicionado e verificado no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain). 

![Uma captura de tela de domínios verificados](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPNs no Azure Active Directory

Os usuários fazem login no Azure AD com o valor em seu atributo userPrincipalName. 

Quando você usa o Azure AD em conjunto com o Seu Diretório Ativo no local, as contas de usuário são sincronizadas usando o serviço Azure AD Connect. Por padrão, o assistente Azure AD Connect usa o atributo userPrincipalName do Active Directory on-premises como upn no Azure AD. Você pode alterá-lo para um atributo diferente em uma instalação personalizada.

É importante que você tenha um processo definido quando você atualiza um Nome Principal de Usuário (UPN) de um único usuário ou para toda a sua organização. 

Consulte os problemas e as soluçãos conhecidas neste documento.

Quando estiver sincronizando contas de usuário do Active Directory com o Azure AD, certifique-se de que as UPNs no mapa Active Directory para verificar domínios no Azure AD.

![Captura de tela de domínios verificados](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Se o valor do atributo userPrincipalName não corresponder a um domínio verificado no Azure AD, o processo de sincronização substituirá o sufixo por um valor padrão .onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Alterações de UPN em massa de roll-out

Siga as melhores práticas para um piloto para [mudanças](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) upn a granel. Também tenha um plano de reversão testado para reverter UPNs se você encontrar problemas que não podem ser resolvidos rapidamente. Uma vez que o piloto esteja sendo executado, você pode começar a segmentar pequenos conjuntos de usuários com várias funções organizacionais e seus conjuntos específicos de aplicativos ou dispositivos.

Passar por esse primeiro subconjunto de usuários lhe dará uma boa idéia do que os usuários devem esperar como parte da mudança. Inclua essas informações nas comunicações do usuário.

Crie um procedimento definido para alterar UPNs em usuários individuais como parte de operações normais. Recomendamos ter um procedimento testado que inclua documentação sobre questões conhecidas e soluçãos.

As seções a seguir detalham possíveis problemas conhecidos e soluções-primas quando as UPNs são alteradas.

## <a name="apps-known-issues-and-workarounds"></a>Aplicativos conhecidos problemas e soluçãos

Os aplicativos [de software como serviço (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) e Linha de Negócios (LoB) geralmente dependem de UPNs para encontrar usuários e armazenar informações de perfil do usuário, incluindo funções. Aplicativos que usam [provisionamento Just in Time](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) para criar um perfil de usuário quando os usuários fazem login no aplicativo pela primeira vez podem ser afetados por alterações na UPN.

**Problema conhecido**<br>
Alterar a UPN de um usuário pode quebrar a relação entre o usuário do Azure AD e o perfil de usuário criado no aplicativo. Se o aplicativo usar [o provisionamento Just in Time,](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)ele poderá criar um novo perfil de usuário. Isso exigirá que o administrador do aplicativo faça alterações manuais para corrigir essa relação.

**Solução alternativa**<br>
[O Azure AD Automated User Provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) permite criar, manter e remover automaticamente suas identidades de usuário em aplicativos de nuvem suportados. A configuração do provisionamento automatizado do usuário em seus aplicativos atualiza automaticamente as UPNs nos aplicativos. Teste os aplicativos como parte da implantação progressiva para validar que eles não são afetados pelas alterações da UPN.
Se você é um desenvolvedor, considere [adicionar suporte ao SCIM ao seu aplicativo](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) para permitir o provisionamento automático do usuário do Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Dispositivos gerenciados problemas conhecidos e soluçãos

Ao [trazer seus dispositivos para o Azure AD,](https://docs.microsoft.com/azure/active-directory/devices/overview)você maximiza a produtividade de seus usuários através de um único login (SSO) através de seus recursos em nuvem e no local.

### <a name="azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD

Os dispositivos [azure AD são](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) unidos diretamente ao Azure AD e permitem que os usuários entrem no dispositivo usando a identidade de sua organização.

**Problemas conhecidos** <br>
Os usuários podem ter problemas de login único com aplicativos que dependem do Azure AD para autenticação.

**Solução alternativa** <br>
Permitir tempo suficiente para que a alteração UPN sincronize com o Azure AD. Depois de verificar se a nova UPN está refletida no Portal Azure AD, peça ao usuário para selecionar o azulejo "Outro usuário" para fazer login com sua nova UPN. Você também pode verificar através do [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Depois de fazer login com sua nova UPN, as referências à UPN antiga ainda podem aparecer na configuração do Windows "Acesso ao trabalho ou à escola".

![Captura de tela de domínios verificados](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD híbrido

Os dispositivos [adeptos do Azure AD híbrido saem](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) do Active Directory e do Azure AD. Você pode implementar o AD híbrido do Azure se o seu ambiente tiver uma pegada de Diretório Ativo no local e também deseja se beneficiar dos recursos fornecidos pelo Azure AD.

**Problemas conhecidos** 

Os dispositivos azure híbridos do Windows 10 provavelmente sofrerão reinicializações inesperadas e problemas de acesso.

Se os usuários fizerem login no Windows antes que o novo UPN tenha sido sincronizado com o Azure AD ou continuarem a usar uma sessão do Windows existente, eles poderão ter problemas de logon único com aplicativos que usam o Azure AD para autenticação se o Acesso Condicional tiver sido configurado para impor o uso de dispositivos Hybrid Joined para acessar recursos. 

Além disso, a seguinte mensagem aparecerá, forçando uma reinicialização após um minuto. 

"Seu PC será reiniciado automaticamente em um minuto. O Windows teve um problema e precisa ser reiniciado. Você deve fechar esta mensagem agora e salvar o seu trabalho".

**Solução alternativa** 

O dispositivo deve ser desunido do Azure AD e reiniciado. Após a reinicialização, o dispositivo se juntará automaticamente ao Azure AD novamente e o usuário deverá fazer login usando a nova UPN selecionando o azulejo "Outro usuário". Para desjuntar um dispositivo do Azure AD, execute o seguinte comando em um prompt de comando:

**dsregcmd /leave**

O usuário precisará [se reinscrever](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) no Windows Hello for Business se estiver sendo usado. Os dispositivos Windows 7 e 8.1 não são afetados por esse problema após alterações na UPN.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator conhecido problemas e soluçãos

Sua organização pode exigir o uso do [aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) para fazer login e acessar aplicativos e dados organizacionais. Embora um nome de usuário possa aparecer no aplicativo, a conta não está configurada para funcionar como um método de verificação até que o usuário complete o processo de registro.

O [aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) tem quatro funções principais:

* Autenticação multifatorial através de um código de notificação ou verificação push

* Aja como um Corretor de Autenticação em dispositivos iOS e Android para fornecer login único para aplicativos que usam [autenticação intermediada](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Registro de dispositivos (também conhecido como Workplace Join) para o Azure AD, que é um requisito para outros recursos como Intune App Protection e Device Registration/Management,

* Faça login telefônico, o que requer MFA e registro de dispositivos.

### <a name="multi-factor-authentication-with-android-devices"></a>Autenticação multifatorial com dispositivos Android

O aplicativo Microsoft Authenticator oferece uma opção de verificação fora da banda. Em vez de colocar uma chamada telefônica ou SMS automatizada para o usuário durante o login, o [MFA (Multi-Factor Authentication, autenticação multifatorial)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) solicita uma notificação ao aplicativo Microsoft Authenticator no smartphone ou tablet do usuário. O usuário simplesmente toca em Aprovar (ou digita um PIN ou biométrico e toca em "Autenticar") no aplicativo para completar seu login.

**Problemas conhecidos** 

Quando você altera a UPN de um usuário, a UPN antiga ainda é exibida na conta do usuário e uma notificação pode não ser recebida. [Os códigos de verificação](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) continuam funcionando.

**Solução alternativa**

Se uma notificação for recebida, instrua o usuário a rejeitar a notificação, abra o aplicativo Authenticator, toque na opção "Verificar notificações" e aprove o prompt do MFA. Depois disso, a UPN exibida na conta será atualizada. Observe que o UPN atualizado pode ser exibido como uma nova conta, isso é devido a outras funcionalidades do Authenticator sendo usadas. Para obter mais informações, consulte os problemas conhecidos adicionais neste artigo.

### <a name="brokered-authentication"></a>Autenticação intermediada

Em corretores Android e iOS como o Microsoft Authenticator habilitam:

* Único login (SSO) - Seus usuários não precisarão fazer login em cada aplicativo.

* Identificação do dispositivo - O corretor acessa o certificado do dispositivo criado no dispositivo quando estava no local de trabalho.

* Verificação de identificação de aplicativo - Quando um aplicativo liga para o corretor, ele passa sua URL de redirecionamento, e o corretor verifica-o.

Além disso, permite que os aplicativos participem de recursos mais avançados, como [o Conditional Access,](https://docs.microsoft.com/azure/active-directory/conditional-access/)e suporta [cenários microsoft intune](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Problemas conhecidos**<br>
O usuário é apresentado com solicitações de autenticação mais interativas em novos aplicativos que usam login assistido por corretor devido a uma incompatibilidade entre o login_hint passado pelo aplicativo e a UPN armazenada no corretor.

**Solução alternativa** <br> O usuário precisa remover manualmente a conta do Microsoft Authenticator e iniciar um novo login a partir de um aplicativo assistido por corretor. A conta será adicionada automaticamente após a autenticação inicial.

### <a name="device-registration"></a>Registro de dispositivos

O aplicativo Microsoft Authenticator é responsável por registrar o dispositivo no Azure AD. O registro do dispositivo permite que o dispositivo seja autenticado no Azure AD e é um requisito para os seguintes cenários:

* Proteção de Aplicativo do Intune

* Inscrição de dispositivo intune

* Login telefônico

**Problemas conhecidos**<br>
Quando você altera o UPN, uma nova conta com a nova UPN aparece listada no aplicativo Microsoft Authenticator, enquanto a conta com a UPN antiga ainda está listada. Além disso, a UPN antiga é exibida na seção Registro de Dispositivos nas configurações do aplicativo. Não há alteração na funcionalidade normal do Registro de Dispositivos ou nos cenários dependentes.

**Solução alternativa** <br> Para remover todas as referências ao UPN antigo no aplicativo Microsoft Authenticator, instrua o usuário a remover manualmente as contas antigas e novas do Microsoft Authenticator, recadastrar-se no MFA e voltar ao dispositivo.

### <a name="phone-sign-in"></a>Entrada por telefone

O login do telefone permite que os usuários entrem no Azure AD sem uma senha. Para habilitar o login do telefone, o usuário precisa se registrar para o MFA usando o aplicativo Authenticator e, em seguida, ativar o login do telefone diretamente no Autenticador. Como parte da configuração, o dispositivo se registra com o Azure AD.

**Problemas conhecidos** <br>
Os usuários não podem usar o login do telefone porque não recebem nenhuma notificação. Se o usuário tocar em Verificar notificações, ele terá um erro.

**Solução alternativa**<br>
O usuário precisa selecionar o menu suspenso na conta ativada para login de telefone e selecionar Desativar o login do telefone. Se desejar, o login do telefone pode ser ativado novamente.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Chave de segurança (FIDO2) problemas conhecidos e soluçãos

**Problemas conhecidos** <br>
Quando vários usuários são registrados na mesma tecla, o sinal na tela mostra uma página de seleção da conta onde o UPN antigo é exibido. As logins usando chaves de segurança não são afetadas por alterações de UPN.  

**Solução alternativa**<br>
Para remover referências a UPNs antigas, os usuários devem [redefinir a chave de segurança e reregistrar](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive problemas conhecidos e soluçãos

Os usuários do OneDrive são conhecidos por sofrer problemas após as alterações do UPN. Para obter mais informações, consulte [como as alterações do UPN afetam os recursos de URL do OneDrive e OneDrive](https://docs.microsoft.com/onedrive/upn-changes).

## <a name="next-steps"></a>Próximas etapas

Veja estes recursos:
* [Azure AD Connect: Conceitos de design](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Preenchimento de UserPrincipalName do Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Tokens de ID da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
