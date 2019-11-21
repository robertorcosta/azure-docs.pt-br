---
title: Security guidance for Azure Multi-Factor Authentication - Azure Active Directory
description: Este documento fornece as diretrizes sobre o uso do Azure MFA com contas do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2756d39a93751271c8c7bf2a51108b9fe5b09b1e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208443"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Diretrizes de segurança para o uso da Autenticação Multifator do Azure com contas do Microsoft Azure Active Directory

A verificação em duas etapas é a opção preferencial para a maioria das organizações que desejam aprimorar o processo de autenticação. O MFA (Autenticação Multifator do Azure) ajuda as empresas a atenderem aos seus requisitos de segurança e conformidade, ao mesmo tempo que fornece uma experiência de conexão simples para seus usuários. Este artigo fornece algumas dicas que você deve considerar ao planejar a adoção do MFA do Azure.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Implantar o MFA do Azure na nuvem

Há duas maneiras de [habilitar a MFA do Azure para todos os usuários](howto-mfa-getstarted.md).

* Comprar licenças para cada usuário (o MFA do Azure, Azure AD Premium, ou Enterprise Mobility + Security)
* Criar um provedor de autenticação multifator e pagamento por usuário ou por autenticação

### <a name="licenses"></a>Licenças

![Apply licenses to users, enable, notify](./media/multi-factor-authentication-security-best-practices/ems.png)

Se você tiver o Azure AD Premium ou licenças do Enterprise Mobility + Segurança, você já tem o Azure MFA. Sua organização não precise de nada adicional para estender a funcionalidade de verificação em duas etapas para todos os usuários. Você só precisa atribuir uma licença a um usuário e, em seguida, você pode ativar o MFA.

Ao configurar a Autenticação Multifator, considere estas dicas:

* Não crie um provedor de autenticação por autenticação multifator. Se você fizer isso, poderá acabar pagando solicitações de verificação de usuários que já têm licenças.
* Se você não possui licenças suficientes para todos os usuários, você pode criar um provedor de autenticação multifator por usuário para cobrir o restante da sua organização. 
* O Azure AD Connect só será exigido se você estiver sincronizando o ambiente do Active Directory local com um diretório do Azure AD. Se você usar apenas um diretório do AD do Azure que não está sincronizado com uma instância local do Active Directory, você não precisará do Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Provedor de Multi-Factor Authentication

![Multi-Factor Authentication Provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se você não tiver licenças que incluem o MFA do Azure, poderá [criar um Provedor de Autenticação do MFA](concept-mfa-authprovider.md).

Ao criar o Provedor de Autenticação, você precisará selecionar um diretório e considerar os seguintes detalhes:

* Você não precisa de um diretório do Azure AD para criar um Provedor de Autenticação Multifator, mas obtém mais funcionalidade com um. Os seguintes recursos são habilitados quando você associa o provedor de autenticação um diretório do Azure AD:
  * Estender a verificação em duas etapas para todos os usuários
  * Os administradores globais oferecem recursos adicionais, como o portal de gerenciamento, saudações personalizadas e relatórios.
* If you synchronize your on-premises Active Directory environment with an Azure AD directory, you need DirSync or Azure AD Sync. If you use an Azure AD directory that is not synchronized with an on-premises instance of Active Directory, you do not need DirSync or Azure AD Sync.
* Escolha o modelo de consumo mais adequada para sua empresa. Depois de selecionar o modelo de uso, não é possível alterá-lo. Os dois modelos são:
  * Por autenticação: cobra para cada verificação. Use esse modelo se desejar verificação em duas etapas para qualquer pessoa que acessa um determinado aplicativo, não para usuários específicos.
  * Por usuário habilitado: cobra para cada usuário que você habilita para o Azure MFA. Use esse modelo se você tiver alguns usuários com licenças de Enterprise Mobility Suite ou Azure AD Premium e outros sem licenças.

### <a name="supportability"></a>Capacidade de suporte

Como a maioria dos usuários estão acostumados a usar apenas senhas para a autenticação, é importante que a sua empresa conscientize todos os usuários sobre esse processo. Essa conscientização pode reduzir a probabilidade de que os usuários chamarão seu suporte técnico para resolver pequenos problemas relacionados ao MFA. No entanto, existem alguns cenários em que é necessário desabilitar temporariamente o MFA. Use as diretrizes abaixo para entender como lidar com esses cenários:

* Treine sua equipe de suporte técnico para lidar com cenários em que o usuário não consegue se conectar devido ao aplicativo móvel ou ao telefone não receber uma notificação ou chamada telefônica. O suporte técnico pode [habilitar um desvio único](howto-mfa-mfasettings.md#one-time-bypass) para permitir que um usuário faça a autenticação uma única vez ao “desviar” a verificação em duas etapas. O desvio é temporário e expira após um número especificado de segundos.
* Considere o [IPs confiáveis recurso](howto-mfa-mfasettings.md#trusted-ips) no Azure MFA como uma maneira de minimizar verificação em duas etapas. Com este recurso, os administradores de um locatário gerenciado ou federado podem ignorar a verificação em duas etapas para usuários que entram pela intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que tenham licenças do Azure AD Premium, Enterprise Mobility Suite ou Autenticação Multifator do Azure.

## <a name="best-practices-for-an-on-premises-deployment"></a>Práticas recomendadas para a implantação no local

Se a sua empresa decidiu aproveitar sua própria infraestrutura para habilitar o MFA, você precisará [implantar um Servidor de Autenticação Multifator do Azure localmente](howto-mfaserver-deploy.md). Os componentes do Servidor MFA são mostrados no seguinte diagrama:

![The default MFA Server components](./media/multi-factor-authentication-security-best-practices/server.png) \*Not installed by default \**Installed but not enabled by default

O Servidor de Autenticação Multifator do Azure pode proteger os recursos da nuvem e locais usando a federação. Você deve ter o AD FS e torná-lo federado com seu locatário do Azure AD.
Ao configurar o Servidor de Autenticação Multifator, considere os seguintes detalhes:

* Se estiver protegendo os recursos do Azure AD usando o AD FS (Serviços de Federação do Active Directory), a primeira etapa de verificação é executada localmente usando o AD FS. A segunda etapa é realizada localmente, cumprindo a declaração.
* Você não precisa instalar o Servidor de Autenticação Multifator do Azure no servidor de federação do AD FS. No entanto, você deve instalar o adaptador da Autenticação Multifator para o AD FS no Windows Server 2012 R2 que está executando o AD FS. Você pode instalar o servidor em outro computador, desde que seja uma versão com suporte, e instalar o adaptador do AD FS separadamente no seu servidor de federação do AD FS. 
* O assistente de instalação do adaptador AD FS da Autenticação Multifator cria um grupo de segurança chamado PhoneFactor Admins em seu Active Directory e, em seguida, adiciona a conta de serviço do AD FS para esse grupo. Verifique se o grupo PhoneFactor Admins foi criado no controlador de domínio e se a conta de serviço do AD FS é membro desse grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no controlador de domínio.

### <a name="user-portal"></a>Portal do Usuário

O portal do usuário permite funcionalidades de autoatendimento e fornece um conjunto completo de funcionalidades de administração do usuário. Ele é executado em um site do IIS (Internet Information Server). Use as diretrizes abaixo para configurar este componente:

* Use o IIS 6 ou superior
* Instalar e registrar o ASP.NET v2.0.507207
* Garanta que esse servidor possa ser implantado em uma rede de perímetro

### <a name="app-passwords"></a>Senhas de aplicativo

Se sua organização for federada para o SSO com o Azure AD e você pretender usar o MFA do Azure, esteja ciente dos seguintes detalhes:

* A senha de aplicativo é verificada pelo Azure AD e, portanto, ignora a federação. A federação é usada apenas durante a configuração de senhas de aplicativo.
* Para usuários federados (SSO), as senhas são armazenadas no ID organizacional. Se o usuário sair da empresa, essa informação deve fluir para o ID da organização usando o DirSync. A desabilitação/exclusão da conta pode levar até três horas para sincronizar, atrasando a desabilitação/exclusão de senhas de aplicativo no Azure AD.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela senha de aplicativo.
* Nenhum recurso de registro/auditoria de autenticação local está disponível para senhas de aplicativo.
* Alguns projetos arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo ao usar a verificação em duas etapas com os clientes, dependendo de onde eles se autenticam. Para clientes que fazem a autenticação em uma infraestrutura local, você usaria um nome de usuário e senha organizacional. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.
* Por padrão, os usuários não podem criar senhas de aplicativo. Se você precisar permitir que os usuários criem senhas de aplicativo, selecionadas o **permitem que os usuários criem senhas de aplicativo para entrar em aplicativos sem navegador** opção.

## <a name="additional-considerations"></a>Considerações adicionais

Use esta lista para obter outras considerações e diretrizes para cada componente implantado localmente:

* Configure a Autenticação Multifator do Azure com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md).
* Instalar e configurar o servidor Azure MFA com [autenticação RADIUS](howto-mfaserver-dir-radius.md).
* Instalar e configurar o servidor Azure MFA com [autenticação IIS](howto-mfaserver-iis.md).
* Instalar e configurar o servidor Azure MFA com [autenticação do Windows](howto-mfaserver-windows.md).
* Instalar e configurar o servidor Azure MFA com [autenticação LDAP](howto-mfaserver-dir-ldap.md).
* Instalar e configurar o servidor Azure MFA com [Gateway de área de trabalho remota e servidor de Autenticação Multifator do Azure usando RADIUS](howto-mfaserver-nps-rdg.md).
* Instalar e configurar a sincronização entre o servidor Azure MFA e [do Active Directory do Windows Server](howto-mfaserver-dir-ad.md).
* [Implante o serviço Web do aplicativo móvel do Servidor de Autenticação Multifator do Azure](howto-mfaserver-deploy-mobileapp.md).
* [Configuração de VPN com autenticação multifator do Azure avançada](howto-mfaserver-nps-vpn.md) para dispositivos Cisco ASA Netscaler da Citrix e Juniper/Pulse Secure VPN usando o LDAP ou RADIUS.

## <a name="next-steps"></a>Próximos passos

Embora este artigo destaque algumas práticas recomendadas para o Azure MFA, há outros recursos que você também pode usar ao planejar sua implantação do MFA. A lista abaixo contém alguns artigos principais que podem ajudá-lo durante esse processo:

* [Relatórios na Autenticação Multifator do Azure](howto-mfa-reporting.md)
* [A experiência de registro de verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Perguntas frequentes sobre a Autenticação Multifator do Azure](multi-factor-authentication-faq.md)
