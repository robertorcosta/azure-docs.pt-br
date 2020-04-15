---
title: Visão geral dos Azure Active Directory Domain Services | Microsoft Docs
description: Nesta visão geral, saiba o que Azure Active Directory Domain Services oferece e como usá-lo em sua organização para fornecer serviços de identidade a aplicativos e serviços na nuvem.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 0f5f890b4f32961e00fb30316a1dc7c88ef93a45
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654833"
---
# <a name="what-is-azure-active-directory-domain-services"></a>O que é o Azure Active Directory Domain Services?

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, política de grupo, protocolo LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você pode usar esses serviços de domínio sem a necessidade de implantar, gerenciar e aplicar um patch em controladores de domínio na nuvem. O Azure AD DS se integra ao locatário existente do Azure AD, o que permite aos usuários entrarem usando suas credenciais atuais. Além disso, você pode usar contas de usuário e grupos existentes para proteger o acesso a recursos, garantindo um lift-and-shift mais tranquilo dos recursos locais para o Azure.

Para começar, [crie um domínio gerenciado do Azure AD DS usando o portal do Azure][tutorial-create].

O Azure AD DS replica informações de identidade do Azure AD. Então, ele funciona com locatários do Azure AD que estão somente na nuvem ou sincronizados com um ambiente local do AD DS (Active Directory Domain Services). O mesmo conjunto de recursos do Azure AD DS existem em ambos os ambientes.

* Se você tiver um ambiente local do AD DS, poderá sincronizar as informações da conta de usuário para fornecer uma identidade consistente aos usuários.
* Para ambientes somente na nuvem, não é necessário um ambiente local tradicional do AD DS para usar os serviços de identidade centralizados do Azure AD DS.

O vídeo a seguir mostra uma visão geral de como o Azure AD DS se integra com aplicativos e cargas de trabalho para fornecer serviços de identidade na nuvem:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Maneiras comuns de fornecer soluções de identidade na nuvem

Ao migrar cargas de trabalho existentes para a nuvem, os aplicativos reconhecidos pelo diretório poderão usar o LDAP para acesso de leitura e gravação a um diretório local do AD DS. Aplicativos em execução no Windows Server normalmente são implantados em VMs (máquinas virtuais) ingressadas em domínio, para que possam ser gerenciados com segurança usando a Política de Grupo. Para autenticar usuários finais, os aplicativos também podem depender da autenticação integrada do Windows, como a autenticação Kerberos ou NTLM.

Geralmente, os administradores de TI usam uma das seguintes soluções para fornecer um serviço de identidade a aplicativos executados no Azure:

* Configurar uma conexão VPN site a site entre as cargas de trabalho executadas no Azure e o ambiente local do AD DS.
    * Os controladores de domínio locais, por sua vez, fornecem autenticação por meio da conexão VPN.
* Criar réplica dos controladores de domínio usando as VMs (máquinas virtuais) do Azure para estender o domínio/floresta do AD DS do local.
    * Os controladores de domínio executados em VMs do Azure fornecem autenticação e replicam informações de diretório entre o ambiente do AD DS local.
* Implantar um ambiente autônomo do AD DS no Azure usando controladores de domínio executados em VMs do Azure.
    * Os controladores de domínio executados em VMs do Azure fornecem autenticação, mas as informações de diretório não são replicadas entre o ambiente do AD DS local.

Com essas abordagens, as conexões VPN com o diretório local tornam os aplicativos vulneráveis a falhas de rede ou interrupções transitórias. Se você implantar controladores de domínio usando VMs no Azure, a equipe de TI precisará gerenciar as VMs e, então, proteger, aplicar patches, monitorar, fazer backup e solucionar os problemas delas.

O Azure AD DS oferece alternativas para a necessidade de criar conexões VPN de volta para um ambiente local do AD DS ou executar e gerenciar VMs no Azure para fornecer serviços de identidade. Como um serviço gerenciado, o Azure AD DS reduz a complexidade da criação de uma solução de identidade integrada para ambientes híbridos e somente em nuvem.

## <a name="azure-ad-ds-features-and-benefits"></a>Características e benefícios do Azure AD DS

Para fornecer serviços de identidade a aplicativos e VMs na nuvem, o Azure AD DS é totalmente compatível com um ambiente tradicional do AD DS para operações como ingresso em domínio, LDAPS (LDAP Seguro), Política de Grupo, gerenciamento de DNS e associação LDAP e suporte de leitura. A compatibilidade com a gravação de LDAP está disponível para objetos criados no domínio gerenciado do Azure AD DS, mas não para os recursos sincronizados do Azure AD.

Para saber mais sobre suas opções de identidade, [faça uma comparação entre o Azure AD DS e o Azure AD, o Active Directory Domain Services nas VMs do Azure e o Active Directory Domain Services local][compare].

Os recursos a seguir do Azure AD DS simplificam as operações de implantação e o gerenciamento:

* **Experiência de implantação simplificada:** O Azure AD DS é habilitado para o locatário do Azure AD usando um assistente único no portal do Azure.
* **Integrado ao Azure AD:** Contas de usuário, associações de grupo e credenciais estão automaticamente disponíveis no locatário do Azure AD. Novos usuários, grupos ou alterações em atributos no locatário do Azure AD ou no ambiente local do AD DS são automaticamente sincronizados com Azure AD DS.
    * Contas em diretórios externos vinculadas ao Azure AD não estão disponíveis no Azure AD DS. As credenciais não estão disponíveis para esses diretórios externos, portanto, não podem ser sincronizadas em um domínio gerenciado do Azure AD DS.
* **Use suas senhas/credenciais corporativas:** as senhas de usuários no Azure AD DS são iguais às de seu locatário do Azure AD DS. Os usuários podem usar as credenciais corporativas para ingressar computadores no domínio, fazer logon interativamente ou pela área de trabalho remota e autenticar no domínio gerenciado do Azure AD DS.
* **Autenticação Kerberos e NTLM:** Com a compatibilidade com a autenticação NTLM e Kerberos, é possível implantar aplicativos que usam a autenticação integrada do Windows.
* **Alta disponibilidade:** O Azure AD DS tem vários controladores de domínio, que oferecem alta disponibilidade para seu domínio gerenciado. Essa alta disponibilidade garante o tempo de atividade e a resiliência a falhas.
    * Em regiões que dão suporte a [Zonas de Disponibilidade do Azure][availability-zones], esses controladores de domínio também são distribuídos entre zonas para resiliência adicional.

Alguns aspectos importantes de um domínio gerenciado do Azure AD DS incluem os seguintes:

* O domínio gerenciado do Azure AD DS é um domínio autônomo. Não é uma extensão de um domínio local.
    * Se necessário, você poderá criar relações de confiança de floresta de saída unidirecionais do Azure AD DS para um ambiente do AD DS local. Para obter mais informações, confira [Conceitos e recursos da floresta de recursos do Azure AD DS][ forest-trusts].
* A equipe de TI não precisa gerenciar, aplicar patch ou monitorar controladores de domínio para o domínio gerenciado do Azure AD DS.

Para ambientes híbridos que executam o AD DS no local, não é necessário gerenciar a replicação do AD para o domínio gerenciado do Azure AD DS. Contas de usuário, associações de grupo e credenciais do diretório local são sincronizados com o Azure AD por meio do [Azure AD Connect][azure-ad-connect]. Essas contas de usuário, associações de grupo e credenciais estão automaticamente disponíveis dentro do domínio gerenciado do Azure AD DS.

## <a name="how-does-azure-ad-ds-work"></a>Como funciona o Azure AD DS?

Para fornecer serviços de identidade, o Azure cria uma instância do AD DS disponível em uma rede virtual de sua escolha. Nos bastidores, é criado um par de controladores de domínio do Windows Server executado em VMs do Azure. Você não precisa gerenciar, configurar nem atualizar esses controladores de domínio. A plataforma Azure os gerencia como parte do serviço do Azure AD DS.

O domínio gerenciado do Azure AD DS é configurado para realizar uma sincronização unidirecional no Azure AD para dar acesso a um conjunto central de usuários, grupos e credenciais. É possível criar recursos diretamente no domínio gerenciado do Azure AD DS, mas eles não são sincronizados com o Azure AD. Então, aplicativos, serviços e VMs no Azure que se conectam a essa rede virtual poderão usar recursos comuns do AD DS, como o ingresso no domínio, a Política de Grupo, o LDAP e a autenticação Kerberos/NTLM.

Em um ambiente híbrido com um ambiente local do AD DS, o [Azure AD Connect][azure-ad-connect] sincroniza as informações de identidade com o Azure AD, que, por sua vez, é sincronizado como Azure AD DS.

![Sincronização no Azure AD Domain Services com o Azure AD e o Active Directory Domain Services local usando o AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Para ver como o Azure AD DS funciona, veja alguns exemplos:

* [Azure AD DS para organizações híbridas](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS para organizações somente na nuvem](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS para organizações híbridas

Muitas organizações executam uma infraestrutura híbrida que inclui cargas de trabalho locais e de aplicativo. Aplicativos herdados migrados para o Azure como parte de uma estratégia de lift-and-shift podem usar conexões LDAP tradicionais para fornecer informações de identidade. Para apoiar essa infraestrutura híbrida, as informações de identidade de um ambiente local do AD DS podem ser sincronizadas com um locatário do Azure AD. Então, o Azure AD DS fornece esses aplicativos herdados no Azure com uma origem de identidade, sem a necessidade de configurar e gerenciar a conectividade do aplicativo com os serviços de diretório local.

Veja um exemplo da Litware Corporation, uma organização híbrida que executa no local e em recursos do Azure:

![Azure Active Directory Domain Services para uma organização híbrida que inclui sincronização local](./media/overview/synced-tenant.png)

* Os aplicativos e as cargas de trabalho de servidor que exigem serviços de domínio são implantados em uma rede virtual no Azure.
    * Isso pode incluir aplicativos herdados migrados para o Azure como parte de uma estratégia de lift-and-shift.
* A Litware Corporation implantou o [Azure AD Connect][azure-ad-connect] para sincronizar informações de identidade do diretório local para seu locatário do Azure AD.
    * As informações de identidade sincronizadas incluem contas de usuário e associações de grupo.
* A equipe de TI da Litware habilita o Azure AD DS para o locatário do Azure AD dessa forma ou em uma rede virtual emparelhada.
* Os aplicativos e as VMs implantados na rede virtual do Azure podem utilizar recursos do Azure AD DS como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. Não há suporte para instalar o Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta ao Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS para organizações somente na nuvem

Um locatário somente em nuvem do Azure AD não tem uma origem de identidade local. Contas de usuário e associações de grupo, por exemplo, são criadas e gerenciadas diretamente no Azure AD.

Agora, vamos ver um exemplo da Contoso, uma organização somente em nuvem que usa o Azure AD para gerenciar identidades. Todas as identidades de usuário, suas credenciais e associações de grupo são criadas e gerenciadas no Azure AD. Não há outras configurações do Azure AD Connect para sincronizar informações de identidade de um diretório local.

![Azure Active Directory Domain Services para uma organização somente em nuvem sem sincronização local](./media/overview/cloud-only-tenant.png)

* Os aplicativos e as cargas de trabalho de servidor que exigem serviços de domínio são implantados em uma rede virtual no Azure.
* A equipe de TI da Contoso habilita o Azure AD DS para o locatário do Azure AD dessa forma ou em uma rede virtual emparelhada.
* Os aplicativos e as VMs implantados na rede virtual do Azure podem utilizar recursos do Azure AD DS como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Azure AD DS se compara a outras soluções de identidade e ver como funciona a sincronização, confira os seguintes artigos:

* [Comparação entre o Azure AD DS e o Azure AD, Active Directory Domain Services nas VMs do Azure e Active Directory Domain Services local][compare]
* [Saiba como o Azure AD Domain Services sincroniza com o diretório do Azure AD][synchronization]

Para começar, [crie um domínio gerenciado do Azure AD DS usando o portal do Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
