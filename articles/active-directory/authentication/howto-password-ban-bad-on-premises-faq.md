---
title: On-premises password protection FAQ - Azure Active Directory
description: Perguntas frequentes sobre a proteção por senha do Azure AD local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee5d6328c6a3e4ea0b4a6359d4a21494e3ae62c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381696"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Proteção por senha do Azure AD local – perguntas frequentes

This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>Perguntas gerais

**Q: What guidance should users be given on how to select a secure password?**

A diretriz atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Diretrizes de Senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**Q: Is on-premises Azure AD Password Protection supported in non-public clouds?**

Não, locais de proteção por senha do Azure AD têm suporte apenas na nuvem pública. Não foi anunciada nenhuma data para a disponibilidade de nuvem não pública.

The Azure AD portal does allow modification of the on-premises-specific "Password protection for Windows Server Active Directory" configuration even in non-public clouds; such changes will be persisted but otherwise will never take effect. Registration of on-premises proxy agents or forests is unsupported when non-public cloud credentials are used, and any such registration attempts will always fail.

**Q: How can I apply Azure AD Password Protection benefits to a subset of my on-premises users?**

Sem suporte. Depois de implantada e habilitada, a proteção por senha do Azure AD não discrimina, todos os usuários recebem benefícios de segurança iguais.

**Q: What is the difference between a password change and a password set (or reset)?**

A password change is when a user chooses a new password after proving they have knowledge of the old password. For example, a password change is what happens when a user logs into Windows and is then prompted to choose a new password.

A password set (sometimes called a password reset) is when an administrator replaces the password on an account with a new password, for example by using the Active Directory Users and Computers management tool. This operation requires a high level of privilege (usually Domain Admin), and the person performing the operation usually does not have knowledge of the old password. Help-desk scenarios often perform password sets, for instance when assisting a user who has forgotten their password. You will also see password set events when a brand new user account is being created for the first time with a password.

The password validation policy behaves the same regardless of whether a password change or set is being done. The Azure AD Password Protection DC Agent service does log different events to inform you whether a password change or set operation was done.  See [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Q: Why are duplicated password rejection events logged when attempting to set a weak password using the Active Directory Users and Computers management snap-in?**

The Active Directory Users and Computers management snap-in will first try to set the new password using the Kerberos protocol. Upon failure, the snap-in will make a second attempt to set the password using a legacy (SAM RPC) protocol (the specific protocols used are not important). If the new password is considered weak by Azure AD Password Protection, this snap-in behavior will result in two sets of password reset rejection events being logged.

**Q: Why are Azure AD Password Protection password validation events being logged with an empty user name?**

Active Directory supports the ability to test a password to see if it passes the domain's current password complexity requirements, for example using the [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. When a password is validated in this way, the testing also includes validation by password-filter-dll based products such as Azure AD Password Protection - but the user names passed to a given password filter dll will be empty. In this scenario, Azure AD Password Protection will still validate the password using the currently in-effect password policy and will issue an event log message to capture the outcome, however the event log message will have empty user name fields.

**Q: Is it supported to install Azure AD Password Protection side by side with other password-filter-based products?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso importante do Windows e não específico para a proteção por senha do Azure AD. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**Q: How can I deploy and configure Azure AD Password Protection in my Active Directory environment without using Azure?**

Sem suporte. A proteção de senha do Azure AD é um recurso do Azure que dá suporte a ser estendido para um ambiente local do Active Directory.

**Q: How can I modify the contents of the policy at the Active Directory level?**

Sem suporte. The policy can only be administered using the Azure AD portal. Confira também a pergunta anterior.

**P: por que o DFSR é necessário para a replicação de sysvol?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure AD serão feitos nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

If your domain is not already using DFSR, you MUST migrate it to use DFSR before installing Azure AD Password Protection. For more information, see the following link:

[SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> The Azure AD Password Protection DC Agent software will currently install on domain controllers in domains that are still using FRS for sysvol replication, but the software will NOT work properly in this environment. Additional negative side-effects include individual files failing to replicate, and sysvol restore procedures appearing to succeed but silently failing to replicate all files. You should migrate your domain to use DFSR as soon as possible, both for DFSR's inherent benefits and also to unblock the deployment of Azure AD Password Protection. Future versions of the software will be automatically disabled when running in a domain that is still using FRS.

**Q: How much disk space does the feature require on the domain sysvol share?**

O uso do espaço preciso varia, pois depende de fatores como o número e o tamanho dos tokens banidos na lista proibida global do Microsoft e a lista personalizada por locatário, além da sobrecarga de criptografia. O conteúdo dessas listas provavelmente aumentará no futuro. Com isso em mente, uma expectativa razoável é que o recurso precisará de pelo menos cinco (5) megabytes de espaço no compartilhamento de sysvol do domínio.

**P: por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

Não. Uma vez que o servidor proxy é sem estado, não é importante saber qual servidor proxy específico é usado.

**Q: Is it okay to deploy the Azure AD Password Protection Proxy service side by side with other services such as Azure AD Connect?**

Sim. O serviço de Proxy de Proteção de Senha do Azure AD e o Azure AD Connect nunca devem entrar em conflito diretamente entre si.

Unfortunately, an incompatibility has been found between the version of the Microsoft Azure AD Connect Agent Updater service that is installed by the Azure AD Password Protection Proxy software and the version of the service that is installed by the [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) software. This incompatibility may result in the Agent Updater service being unable to contact Azure for software updates. It is not recommended to install Azure AD Password Protection Proxy and Azure Active Directory Application Proxy on the same machine.

**Q: In what order should the DC agents and proxies be installed and registered?**

Any ordering of Proxy agent installation, DC agent installation, forest registration, and Proxy registration  is supported.

**Q: Should I be concerned about the performance hit on my domain controllers from deploying this feature?**

O serviço do Agente de Controlador de Domínio de Proteção por Senha do Azure AD não deve afetar significativamente o desempenho do controlador de domínio em uma implantação íntegra existente do Active Directory.

Para a maioria das implantações do Active Directory, operações de alteração de senha são uma pequena proporção da carga de trabalho geral em qualquer controlador de domínio. Como um exemplo, imagine um domínio do Active Directory com 10 mil contas de usuário e uma política de MaxPasswordAge definida como 30 dias. Em média, esse domínio verá 10.000/30 = ~333 operações de alteração de senha por dia, o que é um número pequeno de operações para até mesmo um único controlador de domínio. Considere um possível cenário de pior hipótese: suponha que essas ~333 alterações de senha em um único controlador de domínio tenham sido feitas em uma única hora. Por exemplo, esse cenário poderá ocorrer quando muitos funcionários voltam a trabalhar na segunda-feira pela manhã. Mesmo nesse caso, estamos ainda frente a ~333/60 minutos = seis alterações de senha por minuto, o que, novamente, não é uma carga significativa.

No entanto se os controladores de domínio atuais já estão executando em níveis de desempenho limitado (por exemplo, tiverem atingido o limite máximo com relação à CPU, ao espaço em disco, E/S de disco etc.), recomendamos adicionar mais controladores de domínio ou expandir o espaço em disco disponível antes de implantar esse recurso. Confira também pergunta acima sobre o uso do espaço em disco sysvol acima.

**Q: I want to test Azure AD Password Protection on just a few DCs in my domain. Is it possible to force user password changes to use those specific DCs?**

Não. O sistema operacional de cliente do Windows controla qual controlador de domínio é usado quando um usuário altera sua senha. The domain controller is selected based on factors such as Active Directory site and subnet assignments, environment-specific network configuration, etc. Azure AD Password Protection does not control these factors and cannot influence which domain controller is selected to change a user's password.

Uma maneira de parcialmente atingir essa meta seria implantar a proteção de senha do Azure AD em todos os controladores de domínio em um determinado site do Active Directory. Essa abordagem fornecerá cobertura razoável para os clientes do Windows atribuídos a esse site e, portanto, também para os usuários que estão fazendo logon nesses clientes e alterando suas senhas.

**Q: If I install the Azure AD Password Protection DC Agent service on just the Primary Domain Controller (PDC), will all other domain controllers in the domain also be protected?**

Não. Quando uma senha do usuário for alterada em um controlador de domínio do PDC não fornecido, a senha com texto não criptografado nunca será enviada ao PDC (essa ideia é uma percepção incorreta comum). Depois que uma nova senha é aceita em um determinado controlador de domínio, esse controlador de domínio a usa para criar vários hashes específicos de protocolo de autenticação dessa senha e, em seguida, manter esses hashes no diretório. A senha com texto não criptografado não é persistente. Os hashes atualizados então são replicados para o controlador de domínio primário. As senhas de usuário podem, em alguns casos, ser alteradas diretamente no PDC dependendo de vários fatores, como topologia de rede e design de site do Active Directory. (Confira a pergunta anterior.)

Em resumo, a implantação do serviço de Agente de Controlador de Domínio de Proteção por Senha do Azure AD no PDC é necessária para alcançar 100% de cobertura de segurança do recurso em domínio. Implantar o recurso no PDC apenas não oferece benefícios de segurança de proteção por senha do Azure AD para todos os demais controladores de domínio no domínio.

**Q: Why is custom smart lockout not working even after the agents are installed in my on-premises Active Directory environment?**

Custom smart lockout is only supported in Azure AD. Changes to the custom smart lockout settings in the Azure AD portal have no effect on the on-premises Active Directory environment, even with the agents installed.

**Q: Is a System Center Operations Manager management pack available for Azure AD Password Protection?**

Não.

**Q: Why is Azure AD still rejecting weak passwords even though I've configured the policy to be in Audit mode?**

Audit mode is only supported in the on-premises Active Directory environment. Azure AD is implicitly always in "enforce" mode when it evaluates passwords.

**Q: My users see the traditional Windows error message when a password is rejected by Azure AD Password Protection. Is it possible to customize this error message so that users know what really happened?**

Não. The error message seen by users when a password is rejected by a domain controller is controlled by the client machine, not by the domain controller. This behavior happens whether a password is rejected by the default Active Directory password policies or by a password-filter-based solution such as Azure AD Password Protection.

## <a name="additional-content"></a>Conteúdo adicional

Os links a seguir não fazem parte da principal documentação da Proteção de Senha do Azure AD, mas podem ser uma fonte útil de informações adicionais sobre o recurso.

[Azure AD Password Protection is now generally available!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[O Bloqueio Inteligente e a Proteção por senha do Azure AD agora estão em Versão Prévia Pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Treinamento em suporte da Microsoft Premier\Unified disponível

Se você estiver interessado em aprender mais sobre a proteção por senha do Azure AD e como implantá-la em seu ambiente, poderá aproveitar um serviço proativo da Microsoft disponível para clientes com um contrato de suporte Premier ou Unified. The service is called Azure Active Directory: Password Protection. Para obter mais informações, entre em contato com seu Gerente Técnico de Conta.

## <a name="next-steps"></a>Próximos passos

Se você tiver uma pergunta sobre proteção de senha do Azure AD local que não foi respondida aqui, envie um item de Comentário abaixo. Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
