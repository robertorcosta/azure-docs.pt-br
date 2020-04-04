---
title: Perguntas frequentes sobre os Serviços de Domínio ad do Azure AD | Microsoft Docs
description: Leia e entenda algumas das perguntas mais frequentes sobre configuração, administração e disponibilidade para serviços de domínio do Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 86b68b794928900717bea25623e7eb833c23e86c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655343"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

Esta página responde perguntas frequentes sobre os Serviços de Domínio do Diretório Ativo do Azure.

## <a name="configuration"></a>Configuração

* [Posso criar vários domínios gerenciados para um único diretório do Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Posso habilitar os Serviços de Domínio Ad do Azure em uma rede virtual clássica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Posso habilitar os Serviços de Domínio do Azure AD em uma rede virtual do Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Posso migrar meu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Posso habilitar o Azure AD Domain Services em uma assinatura do Azure CSP (Provedor de Soluções de Nuvem)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Posso habilitar os Serviços de Domínio Ad do Azure em um diretório Ad azure federado? Eu não sincronizo hashes de senha com Azure AD. Posso habilitar os Serviços de Domínio Ad do Azure para este diretório?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Posso disponibilizar os Azure AD Domain Services em várias redes virtuais na minha assinatura?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Posso habilitar os Serviços de Domínio do AD do Azure usando o PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Posso habilitar os Azure AD Domain Services usando um modelo do Resource Manager?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Posso adicionar controladores de domínio a um domínio gerenciado dos Serviços de Domínio do AD do Azure?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Os usuários convidados para o meu diretório podem usar os Azure AD Domain Services?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Posso mover um domínio gerenciado do Azure AD Domain Services existente para uma assinatura diferente, grupo de recursos, região ou rede virtual?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [O Azure AD Domain Services inclui opções de alta disponibilidade?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios gerenciados para um único diretório do Azure AD?
Não. Você pode criar apenas um único domínio atendido pelo Azure AD Domain Services para um único diretório do Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Posso habilitar os Serviços de Domínio Ad do Azure em uma rede virtual clássica?
Redes virtuais clássicas não são suportadas para novas implantações. Os domínios gerenciados existentes implantados em redes virtuais Classic continuam a ser suportados até que sejam aposentados em 1º de março de 2023. Para implantações existentes, você pode [migrar os Serviços de Domínio Ad do Azure AD do modelo de rede virtual clássica para o Gerenciador de recursos](migrate-from-classic-vnet.md).

Para obter mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso habilitar os Serviços de Domínio do Azure AD em uma rede virtual do Azure Resource Manager?
Sim. O Azure AD Domain Services pode ser habilitado em uma rede virtual do Azure Resource Manager. As redes virtuais clássicas do Azure não estão mais disponíveis quando você cria um domínio gerenciado.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar meu domínio gerenciado existente de uma rede virtual Classic para uma rede virtual do Resource Manager?
Sim. Para obter mais informações, consulte [Migrate Azure AD Domain Services do modelo de rede virtual Classic para Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso habilitar o Azure AD Domain Services em uma assinatura do Azure CSP (Provedor de Soluções de Nuvem)?
Sim. Para obter mais informações, veja [como ativar os Serviços de Domínio Ad do Azure em assinaturas CSP do Azure](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>É possível habilitar o Azure AD Domain Services em um diretório federado do Azure AD? Eu não sincronizo hashes de senha para o Azure AD. É possível habilitar o Azure AD Domain Services nesse diretório?
Não. Para autenticar os usuários via NTLM ou Kerberos, o Azure AD Domain Services precisa ter acesso aos hashes de senha das contas de usuário. Em um diretório federado, os hashes de senha não são armazenados no diretório Azure AD. Portanto, o Azure AD Domain Services não funciona com tais diretórios Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar os Azure AD Domain Services em várias redes virtuais na minha assinatura?
O serviço em si não suporta diretamente esse cenário. Seu domínio gerenciado está disponível somente em uma rede virtual por vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor os Serviços de Domínio Ad do Azure a outras redes virtuais. Para obter mais informações, veja [como conectar redes virtuais no Azure usando gateways VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) ou [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso habilitar os Serviços de Domínio do AD do Azure usando o PowerShell?
Sim. Para obter mais informações, veja [como ativar os serviços de domínio Ad do Azure usando o PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Posso habilitar os Azure AD Domain Services usando um modelo do Resource Manager?
Sim, você pode criar um domínio gerenciado do Azure AD Domain Services usando um modelo do Gerenciador de recursos. Um principal de serviço e o grupo Azure AD para administração devem ser criados usando o portal Azure ou o Azure PowerShell antes que o modelo seja implantado. Para obter mais informações, consulte [Criar um domínio gerenciado pelo Azure AD DS usando um modelo do Azure Resource Manager](template-create-instance.md). Quando você cria um domínio gerenciado do Azure AD Domain Services no portal Azure, há também uma opção para exportar o modelo para uso com implantações adicionais.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerenciado dos Serviços de Domínio do AD do Azure?
Não. O domínio fornecido pelo Azure AD Domain Services é um domínio gerenciado. Você não precisa provisionar, configurar ou gerenciar controladores de domínio para este domínio. Essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não pode adicionar controladores de domínio adicionais (gravação de leitura ou somente leitura) para o domínio gerenciado.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Os usuários convidados para o meu diretório podem usar os Azure AD Domain Services?
Não. Os usuários convidados para o seu diretório AD Azure usando o processo de convite [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) são sincronizados no seu domínio gerenciado dos Azure AD Domain Services. No entanto, as senhas para esses usuários não são armazenadas no diretório Azure AD. Portanto, o Azure AD Domain Services não tem como sincronizar hashes NTLM e Kerberos para esses usuários em seu domínio gerenciado. Esses usuários não podem fazer login ou juntar computadores ao domínio gerenciado.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Posso mover um domínio gerenciado do Azure AD Domain Services existente para uma assinatura diferente, grupo de recursos, região ou rede virtual?
Não. Depois de criar um domínio gerenciado do Azure AD Domain Services, você não pode mover a instância para um grupo de recursos diferente, rede virtual, assinatura, etc. Tenha o cuidado de selecionar a rede de assinatura, grupo de recursos, região e rede virtual mais adequada quando você implantar a instância Azure AD DS.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>O Azure AD Domain Services inclui opções de alta disponibilidade?

Sim. Cada domínio gerenciado do Azure AD Domain Services inclui dois controladores de domínio. Você não gerencia ou se conecta a esses controladores de domínio, eles fazem parte do serviço gerenciado. Se você implantar os Serviços de Domínio Azure AD em uma região que suporta Zonas de Disponibilidade, os controladores de domínio serão distribuídos entre as regiões. Em regiões que não suportam Zonas de disponibilidade, os controladores de domínio são distribuídos entre conjuntos de disponibilidade. Você não tem opções de configuração ou controle de gerenciamento sobre essa distribuição. Para obter mais informações, consulte [opções de disponibilidade para máquinas virtuais no Azure](../virtual-machines/windows/availability.md).

## <a name="administration-and-operations"></a>Administração e operações

* [Posso conectar ao controlador de domínio para meu domínio gerenciado usando a Área de Trabalho Remota?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Habilitei os Serviços de Domínio Ad do Azure. Que conta de usuário uso para dominar as máquinas de adesão a este domínio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [É possível ter privilégios de administrador de domínio no domínio gerenciado fornecido pelo Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios gerenciados?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo demora para que as alterações que eu fiz ao meu diretório do Azure AD fiquem visíveis em meu domínio gerenciado?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [É possível estender o esquema do domínio gerenciado fornecido pelo Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar ou adicionar registros DNS em meu domínio gerenciado?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Qual é a política de tempo de vida da senha em um domínio gerenciado?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [O Azure AD Domain Services fornece proteção de bloqueio de conta para o AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso conectar ao controlador de domínio para meu domínio gerenciado usando a Área de Trabalho Remota?
Não. Você não tem permissões para se conectar a controladores de domínio para o domínio gerenciado usando o Remote Desktop. Os membros do grupo *Administradores AAD DC* podem administrar o domínio gerenciado usando ferramentas de administração AD, como o Active Directory Administration Center (ADAC) ou o AD PowerShell. Essas ferramentas são instaladas usando o recurso *Remote Server Administration Tools* em um servidor Windows unido ao domínio gerenciado. Para obter mais informações, consulte [Criar uma VM de gerenciamento para configurar e administrar um domínio gerenciado do Azure AD Domain Services](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Habilitei os Serviços de Domínio Ad do Azure. Qual conta de usuário eu posso usar para ingressar computadores no domínio para este domínio?
Qualquer conta de usuário que faça parte do domínio gerenciado pelo Azure AD DS pode se juntar a uma VM. Os membros do grupo *Administradores AAD DC* recebem acesso remoto à área de trabalho a máquinas que foram unidas ao domínio gerenciado.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>É possível ter privilégios de administrador de domínio no domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. Você não tem privilégios administrativos no domínio gerenciado. *Os* privilégios de administrador de domínio e *administrador de empresas* não estão disponíveis para você usar dentro do domínio. Os membros dos grupos de administrador de domínio ou administrador de empresas em seu Diretório Ativo no local também não recebem privilégios de domínio/administrador de empresas no domínio gerenciado.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios gerenciados?
Os usuários e grupos sincronizados do Azure Active Directory para o Azure AD Domain Services não podem ser modificados porque sua fonte de origem é o Azure Active Directory. Qualquer usuário ou grupo originário do domínio gerenciado pode ser modificado.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora para que as alterações que eu fiz ao meu diretório do Azure AD fiquem visíveis em meu domínio gerenciado?
As alterações feitas no diretório Azure AD usando a UI AD do Azure ou o PowerShell são sincronizadas automaticamente ao domínio gerenciado. Esse processo de sincronização ocorre em segundo plano. Não há um período de tempo definido para que essa sincronização complete todas as alterações do objeto.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>É possível estender o esquema do domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. As extensões do esquema não são suportadas pelos Serviços de Domínio Ad do Azure.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registros DNS em meu domínio gerenciado?
Sim. Os membros do grupo *Administradores AAD DC* recebem privilégios *de administrador de DNS* para modificar registros DeDNS no domínio gerenciado. Esses usuários podem usar o console DNS Manager em uma máquina executando o Windows Server junto ao domínio gerenciado para gerenciar o DNS. Para usar o console DNS Manager, instale *o DNS Server Tools*, que faz parte do recurso opcional Remote Server Administration *Tools* no servidor. Para obter mais informações, consulte [Administrar DNS em um domínio gerenciado do Azure AD Domain Services](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Qual é a política de tempo de vida da senha em um domínio gerenciado?
O tempo de vida da senha padrão em um domínio gerenciado dos Azure AD Domain Services é de 90 dias. Esse tempo de vida de senha não está sincronizado com o tempo de vida de senha configurado no Azure AD. Portanto, você pode ter uma situação em que as senhas dos usuários expiram no seu domínio gerenciado, mas ainda são válidas no Azure AD. Em tais cenários, os usuários precisam alterar sua senha no Azure AD e a nova senha será sincronizada com seu domínio gerenciado. Além disso, os atributos *password-does-not-expire* e *user-must-change password-on-next-next-logon* para contas de usuário não são sincronizados com o domínio gerenciado.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>O Azure AD Domain Services fornece proteção de bloqueio de conta para o AD?
Sim. Cinco tentativas de senha inválida em dois minutos no domínio gerenciado fazem com que uma conta de usuário seja bloqueada por 30 minutos. Depois de 30 minutos, a conta do usuário será desbloqueada automaticamente. Tentativas de senha inválidas no domínio gerenciado não bloqueiam a conta de usuário no Azure AD. A conta de usuário é bloqueada somente no domínio gerenciado do Azure AD Domain Services. Para obter mais informações, consulte [Políticas de bloqueio de senhas e contas em domínios gerenciados](password-policy.md).

## <a name="billing-and-availability"></a>Disponibilidade e cobrança

* [Os Azure AD Domain Services são um serviço pago?](#is-azure-ad-domain-services-a-paid-service)
* [Há uma avaliação gratuita para o serviço?](#is-there-a-free-trial-for-the-service)
* [Posso pausar um domínio gerenciado do Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [É possível fazer failover do Azure AD Domain Services para outra região para um evento de recuperação de desastre?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso obter serviços de domínio Azure AD como parte do Enterprise Mobility Suite (EMS)? Preciso do Azure AD Premium para usar os serviços de domínio do Azure AD?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Em quais regiões do Azure o serviço está disponível?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Os Azure AD Domain Services são um serviço pago?
Sim. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Há uma avaliação gratuita para o serviço?
O Azure AD Domain Services está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Posso pausar um domínio gerenciado do Azure AD Domain Services?
Não. Depois de habilitar um domínio gerenciado pelo Azure AD Domain Services, o serviço está disponível em sua rede virtual selecionada até que você exclua o domínio gerenciado. Não há como pausar o serviço. A cobrança continuará por hora até que você exclua o domínio gerenciado.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>É possível fazer failover do Azure AD Domain Services para outra região para um evento de recuperação de desastre?
Não. Atualmente, o Azure AD Domain Services não fornece um modelo de implantação geo-redundante. É limitado a uma única rede virtual em uma região do Azure. Se quiser utilizar várias regiões do Azure, você precisará executar seus Controladores de Domínio do Active Directory em VMs IaaS do Azure. Para obter orientação de arquitetura, consulte [Estender seu domínio de Diretório Ativo no local para o Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter os Serviços de Domínio do AD do Azure como parte do EMS (Enterprise Mobility Suite)? Preciso do Azure AD Premium para usar os Serviços de Domínio do Azure AD?
Não. O Azure AD Domain Services é um serviço azure pago e não faz parte do EMS. Os Serviços de Domínio Azure AD podem ser usados com todas as edições do Azure AD (Free and Premium). Você é cobrado de hora em hora, dependendo do uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>Em quais regiões do Azure o serviço está disponível?
Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.

## <a name="troubleshooting"></a>Solução de problemas

Confira o [Guia de solução de problemas](troubleshoot.md) a fim de obter soluções para os problemas comuns encontrados ao configurar ou administrar os Azure AD Domain Services.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os serviços de domínio do Azure AD, consulte [O que é o Azure Active Directory Domain Services?](overview.md)

Para começar, consulte [Criar e configurar uma instância de serviços de domínio do diretório ativo do Azure](tutorial-create-instance.md).
