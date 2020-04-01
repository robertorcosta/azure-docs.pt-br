---
title: Acesso remoto seguro à VM nos serviços de domínio azure AD | Microsoft Docs
description: Saiba como garantir o acesso remoto às VMs usando NPS (Network Policy Server) e Autenticação Multifatorial do Azure com uma implantação de Serviços de Desktop Remoto em um domínio gerenciado do Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: f2a222c6a382fa2c316211e293547780a8778177
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423139"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Acesso remoto seguro a máquinas virtuais nos Serviços de Domínio do Diretório Ativo do Azure

Para garantir o acesso remoto a máquinas virtuais (VMs) que são executadas em um domínio gerenciado pelo Azure Active Directory Domain Services (Azure AD DS), você pode usar RDS (Remote Desktop Services, serviços de desktop remotos) e NPS (Network Policy Server). O Azure AD DS autentica os usuários à medida que solicitam acesso através do ambiente RDS. Para maior segurança, você pode integrar a Autenticação Multifatorial do Azure para fornecer um prompt de autenticação adicional durante eventos de login. A autenticação multifatorial do Azure usa uma extensão para NPS para fornecer esse recurso.

> [!IMPORTANT]
> A maneira recomendada de se conectar com segurança às suas VMs em um domínio gerenciado pelo Azure AD DS é usando o Azure Bastion, um serviço PaaS totalmente gerenciado pela plataforma que você fornece dentro de sua rede virtual. Um host bastião fornece conectividade RDP (Remote Desktop Protocol, protocolo de desktop remoto) seguro e perfeito às suas VMs diretamente no portal Azure sobre SSL. Quando você se conecta através de um host de bastião, suas VMs não precisam de um endereço IP público e você não precisa usar grupos de segurança de rede para expor o acesso ao RDP na porta TCP 3389.
>
> Recomendamos fortemente que você use o Azure Bastion em todas as regiões onde ele é suportado. Em regiões sem disponibilidade do Azure Bastion, siga os passos detalhados neste artigo até que o Azure Bastion esteja disponível. Tome cuidado com a atribuição de endereços IP públicos às VMs unidas ao Azure AD DS, onde todo o tráfego RDP de entrada é permitido.
>
> Para obter mais informações, consulte [O que é O Bastião do Azure?][bastion-overview].

Este artigo mostra como configurar rds no Azure AD DS e, opcionalmente, usar a extensão NPS de autenticação multifatorial do Azure.

![Visão geral do RDS (Remote Desktop Services, serviços de desktop remotos)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma sub-rede *de cargas de trabalho* criada em sua rede virtual Azure Active Directory Domain Services.
    * Se necessário, configure a rede virtual para um domínio gerenciado do [Azure Active Directory Domain Services][configure-azureadds-vnet].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Implantar e configurar o ambiente de desktop remoto

Para começar, crie um mínimo de duas VMs azure que executam o Windows Server 2016 ou o Windows Server 2019. Para redundância e alta disponibilidade do ambiente RD (Remote Desktop, área de trabalho remota), você pode adicionar e carregar hosts adicionais de equilíbrio mais tarde.

Uma implantação rds sugerida inclui as seguintes duas VMs:

* *RDGVM01* - Executa o servidor RD Connection Broker, o servidor RD Web Access e o servidor RD Gateway.
* *RDSHVM01* - Executa o servidor RD Session Host.

Certifique-se de que as VMs sejam *implantadas* em uma sub-rede de cargas de trabalho da sua rede virtual Azure AD DS e, em seguida, junte-se às VMs para o domínio gerenciado do Azure AD DS. Para obter mais informações, veja como [criar e juntar uma VM do Windows Server a um domínio gerenciado pelo Azure AD DS][tutorial-create-join-vm].

A implantação do ambiente RD contém uma série de etapas. O guia de implantação RD existente pode ser usado sem alterações específicas para usar em um domínio gerenciado pelo Azure AD DS:

1. Faça login em VMs criados para o ambiente RD com uma conta que faz parte do grupo *Azure AD DC Administrators,* como *contosoadmin*.
1. Para criar e configurar rds, use o guia de implantação do [ambiente de área remota][deploy-remote-desktop]existente . Distribua os componentes do servidor RD em suas VMs do Azure conforme desejado.
1. Se você quiser fornecer acesso usando um navegador da Web, [configure o cliente web do Remote Desktop para seus usuários][rd-web-client].

Com o RD implantado no domínio gerenciado pelo Azure AD DS, você pode gerenciar e usar o serviço como faria com um domínio AD DS no local.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Implantar e configurar NPS e a extensão Azure MFA NPS

Se você quiser aumentar a segurança da experiência de login do usuário, você pode, opcionalmente, integrar o ambiente RD com a Autenticação Multifatorial do Azure. Com essa configuração, os usuários recebem um prompt adicional durante o login para confirmar sua identidade.

Para fornecer esse recurso, um NPS (Network Policy Server) adicional é instalado em seu ambiente, juntamente com a extensão NPS de autenticação multifatorial do Azure. Esta extensão se integra ao Azure AD para solicitar e retornar o status de solicitações de autenticação multifatorial.

Os usuários devem ser [registrados para usar a Autenticação Multifatorial do Azure,][user-mfa-registration]que pode exigir licenças AD adicionais do Azure.

Para integrar a autenticação multifatorial do Azure no ambiente de desktop remoto AZure AD DS, crie um servidor NPS e instale a extensão:

1. Crie uma VM adicional do Windows Server 2016 ou 2019, como *o NPSVM01,* que está conectado a uma sub-rede *de cargas de trabalho* em sua rede virtual Azure AD DS. Junte-se à VM ao domínio gerenciado pelo Azure AD DS.
1. Faça login no NPS VM como conta que faz parte do grupo *Azure AD DC Administrators,* como *contosoadmin*.
1. No **Gerenciador de servidores,** **selecione Adicionar funções e recursos**e instale a função Política de rede e *serviços de acesso.*
1. Use o artigo de como fazer para [instalar e configurar a extensão Azure MFA NPS][nps-extension].

Com o servidor NPS e a extensão NPS de autenticação multifatorial instalada, complete a próxima seção para configurá-la para uso com o ambiente RD.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integrar gateway de desktop remoto e autenticação multifatorial do Azure

Para integrar a extensão NPS de autenticação multifatorial do Azure, use o artigo de como fazer para [integrar sua infra-estrutura do Remote Desktop Gateway usando a extensão NPS (Network Policy Server) e o Azure AD][azure-mfa-nps-integration].

As seguintes opções adicionais de configuração são necessárias para integrar-se a um domínio gerenciado pelo Azure AD DS:

1. Não [registre o servidor NPS no Active Directory][register-nps-ad]. Esta etapa falha em um domínio gerenciado pelo Azure AD DS.
1. Na [etapa 4 para configurar a diretiva de rede,][create-nps-policy]verifique também a caixa para ignorar as propriedades de **discagem da conta do usuário**.
1. Se você usar o Windows Server 2019 para o servidor NPS e a extensão NPS de autenticação multifatorial do Azure, execute o seguinte comando para atualizar o canal seguro para permitir que o servidor NPS se comunique corretamente:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Os usuários agora são solicitados para um fator de autenticação adicional quando fazem login, como uma mensagem de texto ou um prompt no aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como melhorar a resiliência de sua implantação, consulte [Remote Desktop Services - Alta disponibilidade][rds-high-availability].

Para obter mais informações sobre como garantir o login do usuário, consulte [Como funciona: Autenticação Multifatorial Do Azure][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
