---
title: Faq de autenticação multifatorial do Azure - Diretório Ativo do Azure
description: Perguntas frequentes e respostas relacionadas à Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271396"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Perguntas frequentes sobre a Autenticação Multifator do Azure

Estas perguntas frequentes respondem a perguntas comuns sobre a Autenticação Multifator do Azure e sobre o uso do serviço de Autenticação Multifator. Ele é dividido em perguntas sobre o serviço em geral, modelos, experiências do usuário, de cobrança e solução de problemas.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o MFA Server para novas implantações. Novos clientes que gostariam de exigir autenticação multifatorial de seus usuários devem usar a Autenticação Multifatorial baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes de 1º de julho poderão baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.
>
> As informações compartilhadas abaixo sobre o Azure Multi-Factor Authentication Server só são aplicáveis para usuários que já têm o servidor MFA em execução.
>
> O licenciamento baseado em consumo não está mais disponível para novos clientes a partir de 1º de setembro de 2018.
> A partir de 1º de setembro de 2018, novos provedores de auth podem não ser mais criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

## <a name="general"></a>Geral

* [Como o Azure Multi-Factor Authentication Server lida com os dados do usuário?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Quais códigos curtos de SMS são usados para enviar mensagens SMS aos meus usuários?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Como o Servidor de Autenticação Multifator do Azure lida com os dados do usuário?

Com o Multi-Factor Authentication Server, os dados do usuário são armazenados apenas nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa a verificação em duas etapas, o Servidor de Autenticação Multifator envia dados ao serviço de nuvem da Autenticação Multifator do Azure para autenticação. A comunicação entre o Servidor de Autenticação Multifator e o serviço de nuvem da Autenticação Multifator usa o protocolo SSL (Secure Sockets Layer) ou TLS (Transport Layer Security) pela porta de saída 443.

Quando solicitações de autenticação são enviadas ao serviço de nuvem, dados são coletados para relatórios de autenticação e uso. Os seguintes campos de dados estão incluídos em registros de verificação em duas etapas:

* **ID Exclusiva** (ou o nome de usuário, ou a ID do Servidor de Autenticação Multifator local)
* **Primeiro e Último Nome** (opcional)
* **Endereço de e-mail** (opcional)
* **Número de Telefone** (ao usar uma chamada de voz ou autenticação SMS)
* **Token de Dispositivo** (ao usar a autenticação de aplicativos móveis)
* **Modo de Autenticação**
* **Resultado da autenticação**
* **Nome do Servidor de Autenticação Multifator**
* **IP do Servidor de Autenticação Multifator**
* **IP do Cliente** (se disponível)

Os campos opcionais podem ser configurados no Servidor de Autenticação Multifator.

O resultado de verificação (sucesso ou negação) e o motivo se ele foi negado, é armazenado com os dados de autenticação. Esses dados estão disponíveis em relatórios de uso e de autenticação.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Quais códigos curtos de SMS são usados para enviar mensagens SMS aos meus usuários?

Nos Estados Unidos, usamos os seguintes códigos curtos de SMS:

* *97671*
* *69829*
* *51789*
* *99399*

No Canadá, usamos os seguintes códigos curtos de SMS:

* *759731*
* *673801*

Não há garantia de entrega rápida de autenticação de SMS consistente ou de autenticação multifatorial baseada em voz pelo mesmo número. No interesse de nossos usuários, podemos adicionar ou remover códigos curtos a qualquer momento à medida que fizermos ajustes de rota para melhorar a entrega de SMS. Não apoiamos códigos curtos para países ou regiões além dos Estados Unidos e Canadá.

## <a name="billing"></a>Cobrança

A maioria das perguntas de cobrança pode ser respondida referindo-se à [página de preços de autenticação multifatorial](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou à documentação para [versões de autenticação multifatorial do Azure e planos de consumo](concept-mfa-licensing.md).

* [Minha organização é cobrada pelo envio de telefonemas e mensagens de texto que são usadas para autenticação?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [O modelo de cobrança por usuário me cobra por todos os usuários habilitados ou apenas pelos que realizaram a verificação em duas etapas?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Como funciona o faturamento de autenticação multifatorial?](#how-does-multi-factor-authentication-billing-work)
* [Existe uma versão gratuita da Autenticação Multifatorial do Azure?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [Minha organização pode alternar entre modelos de faturamento por usuário e por autenticação a qualquer momento?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Minha organização pode alternar entre faturamento baseado em consumo e assinaturas (um modelo baseado em licença) a qualquer momento?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Minha organização tem que usar e sincronizar identidades para usar a autenticação multifatorial do Azure?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>A minha organização é cobrada por enviar as mensagens de texto e realizar as chamadas telefônicas usadas para autenticação?

Não, você não está cobrado por chamadas telefônicas individuais colocadas ou mensagens de texto enviadas aos usuários através da Autenticação Multifatorial do Azure. Se você usar um provedor MFA por autenticação, você será cobrado por cada autenticação, mas não pelo método usado.

Os usuários podem ser cobrados por chamadas telefônicas ou mensagens de texto recebidas, de acordo com seu serviço de telefone pessoal.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>O modelo de cobrança por usuário me cobra por todos os usuários habilitados ou somente pelos que executaram a verificação em duas etapas?

A cobrança se baseia no número de usuários configurados para usar a Autenticação Multifator, independentemente deles terem executado uma verificação de duas etapas naquele mês.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Como funciona a cobrança de Autenticação Multifator?

Quando você cria um provedor MFA por usuário ou por autenticação, a assinatura do Azure da sua organização é cobrada mensalmente com base no uso. Este modelo de faturamento é semelhante ao que o Azure fatura para o uso de máquinas virtuais e Web Apps.

Quando você compra uma assinatura da Autenticação Multifator do Azure, sua organização só paga a taxa anual de licença para cada usuário. Licenças MFA e Office 365, Azure AD Premium ou os pacotes Enterprise Mobility + Security são cobrados dessa maneira.

Para obter mais informações, consulte [Como obter Autenticação Multifator do Azure](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Existe uma versão gratuita da Autenticação Multifator do Azure?

Os padrões de segurança podem ser ativados no nível Azure AD Free. Com os padrões de segurança, todos os usuários estão habilitados para autenticação multifatorial usando o aplicativo Microsoft Authenticator. Não há capacidade de usar mensagens de texto ou verificação de telefone com padrões de segurança, apenas o aplicativo Microsoft Authenticator.

Para obter mais informações, consulte [Quais são os padrões de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Minha organização pode alternar entre os modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?

Se sua organização adquirir o MFA como um serviço autônomo de cobrança com base em consumo, escolha um modelo de cobrança ao criar um provedor MFA. Você não pode alterar o modelo de cobrança depois que um provedor MFA é criado. 

Se o seu provedor de MFA *não* estiver vinculado a um inquilino Azure AD ou você vincular o novo provedor MFA a um inquilino AD diferente, as configurações e as opções de configuração do Azure não forem transferidas. Além disso, os servidores MFA existentes do Azure precisarão ser reativados usando as credenciais de ativação geradas por meio do novo provedor de MFA. Reativar os servidores MFA para vinculá-los para o novo provedor de MFA não afete de telefonema e autenticação de mensagens de texto, mas as notificações de aplicativo móvel deixará de funcionar para todos os usuários até que eles reativarem o aplicativo móvel.

Saiba mais sobre provedores MFA em [Introdução a um provedor de Autenticação Multifator do Azure](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Minha organização pode trocar alternar entre a cobrança baseada no consumo e assinaturas (um modelo baseado em licença) a qualquer momento?

Em alguns casos, sim.

Se o diretório tiver um provedor de Autenticação Multifator do Azure *por usuário*, você pode adicionar licenças do MFA. Os usuários com licenças não são somados a cobrança de baseado em consumo por usuário. Os usuários sem licenças ainda podem ser habilitados para MFA através do provedor MFA. Se você comprar e atribuir licenças para todos os usuários configurados para usar a Autenticação Multifator, poderá excluir o provedor de Autenticação Multifator do Azure. Você sempre pode criar outro provedor MFA por usuário, se você tiver mais usuários do que licenças no futuro.

Se o seu diretório tiver um provedor *de* autenticação azure Multi-Factor, você sempre será cobrado por cada autenticação, desde que o provedor MFA esteja vinculado à sua assinatura. Você pode atribuir licenças MFA para os usuários, mas você ainda será cobrado para cada solicitação de verificação em duas etapas, se se trata de uma pessoa com uma licença MFA atribuída ou não.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Minha organização precisa usar e sincronizar identidades para usar a Autenticação Multifator do Azure?

Se a sua organização usa um modelo de cobrança baseado em consumo, o Azure Active Directory é opcional, mas não é necessário. Se o seu provedor DeM não estiver vinculado a um inquilino Azure AD, você só poderá implantar o Azure Multi-Factor Authentication Server no local.

O Azure Active Directory é necessário para o modelo de licença porque as licenças são adicionadas ao locatário do Azure AD quando você compra e as atribui aos usuários no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerenciar e dar suporte a contas de usuário

* [O que devo dizer aos meus usuários para fazerem se eles não receberem uma resposta em seu telefone?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [O que devo fazer se um dos meus usuários não pode entrar em sua conta?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [O que devo fazer se um dos meus usuários perder um telefone que está usando senhas de aplicativos?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [E se um usuário não puder fazer login em aplicativos que não são do navegador?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Meus usuários dizem que às vezes eles não recebem a mensagem de texto ou os tempos de verificação.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Posso alterar a quantidade de tempo que meus usuários têm para inserir o código de verificação de uma mensagem de texto antes que o sistema seja atualizado?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Posso usar tokens de hardware com o Azure Multi-Factor Authentication Server?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Posso usar o Azure Multi-Factor Authentication Server para proteger serviços de terminal?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Configurei o ID do chamador no Servidor MFA, mas meus usuários ainda recebem chamadas de Autenticação Multifatorial de um chamador anônimo.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Por que meus usuários estão sendo solicitados a registrar suas informações de segurança?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>O que devo dizer aos meus usuários para fazerem se eles não receberem uma resposta em seu telefone?

Peça aos seus usuários até cinco vezes em 5 minutos para receber uma chamada telefônica ou SMS para autenticação. A Microsoft usa vários provedores para entregar mensagens SMS e chamadas. Se essa abordagem não funcionar, abra um caso de suporte para solucionar ainda mais.

Aplicativos de segurança de terceiros também podem bloquear a mensagem de texto do código de verificação ou a chamada telefônica. Se usar um aplicativo de segurança de terceiros, tente desativar a proteção e, em seguida, solicite que outro código de verificação MFA seja enviado.

Se as etapas acima não funcionarem, verifique se os usuários estão configurados para mais de um método de verificação. Tente fazer login novamente, mas selecione um método de verificação diferente na página de login.

Para obter mais informações, consulte o [guia de solução de problemas do usuário final](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>O que devo fazer se um dos meus usuários não conseguir acessar a própria conta?

Você pode redefinir a conta do usuário fazendo com que ele refaça o processo de registro. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com a Autenticação Multifator do Azure na nuvem](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>O que devo fazer se um dos meus usuários perder um telefone que está usando senhas de aplicativo?

Para evitar acesso não autorizado, exclua as senhas do todos os usuários aplicativo. Depois que o usuário tiver outro dispositivo, ele poderá recriar as senhas. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com a Autenticação Multifator do Azure na nuvem](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>E se um usuário não conseguir entrar em aplicativos que não são acessados por navegador?

Se sua organização usa ainda clientes herdados e você [permitido o uso de senhas de aplicativo](howto-mfa-mfasettings.md#app-passwords), em seguida, os usuários não conseguem entrar nesses clientes herdados com seu nome de usuário e senha. Em vez disso, eles precisam [configurar senhas de aplicativo](../user-help/multi-factor-authentication-end-user-app-passwords.md). Os usuários devem limpar (excluir) suas informações de logon, reinicie o aplicativo e entre com seu nome de usuário e *senha de aplicativo* em vez de regulares de senha.

Se sua organização não tiver clientes legados, você não deve permitir que seus usuários criem senhas de aplicativos.

> [!NOTE]
> **Autenticação moderna para clientes do Office 2013**
>
> Senhas de aplicativo são necessárias apenas para aplicativos que não suportam autenticação moderna. Clientes do Office 2013 oferece suporte aos protocolos de autenticação moderna, mas precisam ser configurados. A autenticação moderna está disponível para qualquer cliente que execute a atualização de março de 2015 ou posterior para o Office 2013. Para obter mais informações, consulte o blog [post Escritório Atualizado 365 autenticação moderna](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Meus usuários dizem que às vezes eles não recebem a mensagem de texto ou os tempos de verificação.

A entrega de mensagens SMS não é garantida porque existem fatores incontroláveis que podem afetar a confiabilidade do serviço. Esses fatores incluem o país ou região de destino, a operadora de telefonia móvel e a força do sinal.

Aplicativos de segurança de terceiros também podem bloquear a mensagem de texto do código de verificação ou a chamada telefônica. Se usar um aplicativo de segurança de terceiros, tente desativar a proteção e, em seguida, solicite que outro código de verificação MFA seja enviado.

Se seus usuários geralmente tiverem problemas com o recebimento confiável de mensagens de texto, diga-lhes para usar o aplicativo Microsoft Authenticator ou o método de chamada telefônica. O Microsoft Authenticator pode receber notificações tanto por conexões celulares quanto wi-fi. Além disso, o aplicativo móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem sinal. O aplicativo Microsoft Authenticator está disponível para [Android,](https://go.microsoft.com/fwlink/?Linkid=825072) [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)e [Windows Phone.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Posso alterar a quantidade de tempo que meus usuários precisam inserir o código de verificação de uma mensagem de texto antes do sistema expira?

Em alguns casos, Sim.

Para o SMS unidirecional com o Servidor MFA v7.0 ou posterior do Azure, você pode configurar o tempo limite de configuração definindo uma chave do registro. Depois que o serviço de nuvem MFA envia a mensagem de texto, o código de verificação (ou a senha de uso único) é retornada para o Servidor MFA. O Servidor MFA armazena o código na memória para 300 segundos por padrão. Se o usuário insere seu código depois de 300 segundos, a autenticação será negada. Siga estas etapas para alterar a configuração de tempo limite padrão:

1. Ir para `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Crie uma chave de registro **DWORD** chamada *pfsvc_pendingSmsTimeoutSeconds* e defina a hora em segundos que você deseja que o Servidor MFA do Azure armazene senhas únicas.

>[!TIP]
>
> Se você tiver vários Servidores MFA, apenas aquele que processam a solicitação de autenticação original sabem o código de verificação que foi enviado ao usuário. Quando o usuário insere o código, a solicitação de autenticação para validação deve ser enviada para o mesmo servidor. Se a validação de código é enviada para um servidor diferente, a autenticação será negada.

Se os usuários não responderem ao SMS dentro do período de tempo limite definido, a autenticação será negada.

Para sMS unidirecional com O Zure MFA na nuvem (incluindo o adaptador AD FS ou a extensão Do Servidor de Diretiva de Rede), você não pode configurar a configuração de tempo. O Azure AD armazena o código de verificação durante 180 segundos.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Posso usar tokens de hardware com o Servidor de Autenticação Multifator do Azure?

Se você estiver usando o Azure Multi-Factor Authentication Server, você pode importar tokens baseados em tempo e senha única (TOTP) de terceiros e, em seguida, usá-los para verificação em duas etapas.

Será possível usar tokens ActiveIdentity que sejam TOTP OATH se você colocar a chave secreta em um arquivos CSV e o importar para o Servidor de Autenticação Multifator do Azure. É possível usar tokens OATH com os Serviços de Federação do Active Directory (ADFS), a autenticação baseada em formulários do Servidor de Informações da Internet (IIS) e o serviço RADIUS, quando o sistema cliente aceita entradas do usuário.

Você pode importar tokens OATH TOTP de terceiros com os seguintes formatos:  

- PSKC (Contêiner Portátil de Chave Simétrica)
- CSV se o arquivo contiver um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Posso usar o Servidor de Autenticação Multifator do Azure para proteger os Serviços de Terminal?

Sim, mas se você estiver usando o Windows Server 2012 R2 ou posterior, você só poderá proteger os Serviços de Terminal usando o Remote Desktop Gateway (RD Gateway).

As alterações de segurança no Windows Server 2012 R2 mudaram a forma como o Servidor de Autenticação Multifator do Azure se conecta ao pacote de segurança LSA (Autoridade de Segurança Local) no Windows Server 2012 e versões anteriores. Para versões dos Serviços de Terminal no Windows Server 2012 ou anteriores, você pode [proteger um aplicativo com a Autenticação do Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se você estiver usando o Windows Server 2012 R2, você precisa do RD Gateway.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Eu configurei uma ID de Chamadas no Servidor MFA, mas os usuários ainda recebem chamadas de Autenticação Multifator de um chamador anônimo.

Quando as chamadas da Autenticação Multifator são feitas por meio de rede telefônica pública, às vezes, elas são roteadas por uma operadora que não é compatível com a ID de chamadas. Devido a esse comportamento da operadora, o ID do chamador não é garantido, mesmo que o sistema de Autenticação Multifatorial sempre o envie.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Por que meus usuários estão sendo solicitados para registrar as informações de segurança?

Há vários motivos que os usuários podem ser solicitados para registrar as informações de segurança:

- O usuário foi habilitado para MFA pelo administrador no Azure AD, mas não tem informações de segurança ainda registradas para sua conta.
- O usuário ativou autoatendimento para redefinição de senha no Azure AD. As informações de segurança ajudará a redefinir sua senha no futuro, se ele esquecerem.
- O usuário acessou um aplicativo que tem uma política de Acesso Condicional para exigir MFA e não se registrou anteriormente no MFA.
- O usuário está registrando um dispositivo com o Azure AD (incluindo o Azure AD Join), e sua organização requer MFA para registro de dispositivo, mas o usuário não se registrou anteriormente no MFA.
- O usuário está gerando o Windows Hello for Business no Windows 10 (que requer MFA) e ainda não se registrou no MFA.
- A organização criou e ativado uma diretiva de registro de MFA que foi aplicada ao usuário.
- O usuário registrado para MFA anteriormente, mas escolher um método de verificação que um administrador como desabilitado. O usuário, portanto, deve passar pelo registro MFA novamente para selecionar um novo método de verificação padrão.

## <a name="errors"></a>Errors

* [O que os usuários devem fazer se virem uma mensagem de erro "Solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativos móveis?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [O que os usuários devem fazer se virem uma mensagem de erro 0x800434D4L ao fazer login em um aplicativo que não seja do navegador?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>O que os usuários devem fazer se virem uma mensagem de erro "Solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativos móveis?

Peça ao usuário para concluir o seguinte procedimento para remover sua conta do Autenticador Microsoft e, em seguida, adicioná-la novamente:

1. Acesse [o perfil do portal Azure](https://account.activedirectory.windowsazure.com/profile/) e faça login com uma conta organizacional.
2. Escolha **Verificação de Segurança Adicional**.
3. Remova a conta existente do aplicativo Microsoft Authenticator.
4. Clique **em Configurar**e siga as instruções para reconfigurar o Autenticador Microsoft.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>O que os usuários deverão fazer se receberem uma mensagem de erro 0x800434D4L ao entrar em um aplicativo que não é de navegador?

O erro *0x800434D4L* ocorre quando você tenta entrar em um aplicativo não-navegador, instalado em um computador local, que não funciona com contas que requerem verificação em duas etapas.

Uma solução alternativa para esse erro é ter contas de usuário separadas para operações administrativas e não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, saiba como [fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Próximas etapas

Se sua pergunta não for respondida aqui, as seguintes opções de suporte estão disponíveis:

* Pesquise a [Base de Dados de Conhecimento de Suporte da Microsoft](https://support.microsoft.com) para obter soluções para problemas técnicos comuns.
* Pesquise e navegue perguntas técnicas e respostas da comunidade, ou faça sua própria pergunta no [Diretório Ativo do Azure Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).
* Entre em contato com o profissional da Microsoft através [do suporte ao Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, é útil incluir o máximo possível de informações sobre o problema. As informações que você pode fornecer incluem a página em que viu o erro, o código de erro específico, a ID da sessão específica e a ID do usuário que viu o erro.
* Se você é um cliente phonefactor legado e tem dúvidas ou [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) precisa de ajuda para redefinir uma senha, use o endereço de e-mail para abrir um caso de suporte.
