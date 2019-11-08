---
title: No Azure, comparar serviços baseados no Active Directory | Microsoft Docs
description: Nesta visão geral, você compara as diferentes ofertas de identidade para o Active Directory Domain Services, o Azure Active Directory e o Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 04a1f19ddf894467a9129e8a16c951298a6af529
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474720"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Comparar o Active Directory Domain Services autogerenciado, o Azure Active Directory e o Azure Active Directory Domain Services gerenciado

Para fornecer acesso a uma identidade central a aplicativos, serviços ou dispositivos, há três maneiras comuns no Azure de usar serviços baseados no Active Directory. Essa opção nas soluções de identidade proporciona a flexibilidade de usar o diretório mais apropriado para as necessidades da organização. Por exemplo, se você gerenciar principalmente usuários somente de nuvem que executam dispositivos móveis, talvez não faça sentido compilar e executar sua própria solução de identidade de AD DS (Active Directory Domain Services). Em vez disso, você poderia usar apenas o Azure Active Directory.

Embora as três soluções de identidade baseadas em Active Directory compartilhem um nome e uma tecnologia comuns, elas foram projetadas para fornecer serviços que atendem a diferentes demandas dos clientes. Em alto nível, essas soluções de identidade e conjuntos de recursos são:

* **AD DS (Active Directory Domain Services)** – servidor de protocolo LDAP pronto para empresas que fornece recursos importantes como identidade e autenticação, gerenciamento de objetos de computador, política de grupo e relações de confiança.
    * O AD DS é um componente central em muitas organizações com um ambiente de TI local e fornece autenticação de conta de usuário principal e recursos de gerenciamento de computador.
* **Azure AD (Azure Active Directory)** – gerenciamento de identidades e dispositivos móveis baseado em nuvem que fornece serviços de autenticação e de conta de usuário para recursos como o Office 365, o portal do Azure ou aplicativos SaaS.
    * O Azure AD pode ser sincronizado com um ambiente de AD DS local para fornecer uma única identidade para os usuários que trabalham nativamente na nuvem.
* **Azure AD DS (Azure Active Directory Domain Services)** – fornece serviços de domínio gerenciado com um subconjunto de recursos de AD DS tradicionais totalmente compatíveis, tais como ingresso no domínio, política de grupo, LDAP e autenticação Kerberos/NTLM.
    * O Azure AD DS integra-se ao Azure AD, que pode ser sincronizado com um ambiente do AD DS local, para estender casos de uso de identidade central para aplicativos Web tradicionais executados no Azure como parte de uma estratégia de lift-and-shift.

Este artigo de visão geral compara e contrasta como essas soluções de identidade podem trabalhar em conjunto ou ser usadas de forma independente, dependendo das necessidades da organização.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS e AD DS autogerenciado

Se você tiver aplicativos e serviços que precisam de acesso a mecanismos de autenticação tradicionais, tais como Kerberos ou NTLM, haverá duas maneiras de fornecer o Active Directory Domain Services na nuvem:

* Um domínio *gerenciado* que você cria usando o Azure AD DS (Azure Active Directory Domain Services). A Microsoft cria e gerencia os recursos necessários.
* Um *domínio autogerenciado* que você cria e configura usando recursos tradicionais, tais como VMs (máquinas virtuais), SO convidado do Windows Server e AD DS (Active Directory Domain Services). Em seguida, você continua administrando esses recursos.

Com o Azure AD DS, os principais componentes de serviço são implantados e mantidos para você pela Microsoft como uma experiência de domínio *gerenciado*. Você não implanta, não gerencia, não aplica patches e nem protege a infraestrutura de AD DS de componentes como as VMs, o sistema operacional Windows Server ou DCs (controladores de domínio).

O Azure AD DS fornece um subconjunto menor de recursos para o ambiente de AD DS tradicional autogerenciado, que reduz parte da complexidade do design e do gerenciamento. Por exemplo, não há florestas, domínios, sites e links de replicação do AD para criar e manter. Para aplicativos e serviços que são executados na nuvem e precisam de acesso a mecanismos de autenticação tradicionais, como Kerberos ou NTLM, o Azure AD DS fornece uma experiência de domínio gerenciado com o mínimo de sobrecarga administrativa.

Ao implantar e executar um ambiente de AD DS autogerenciado, você precisa manter toda a infraestrutura e os componentes de diretório associados. Há uma sobrecarga de manutenção adicional com um ambiente de AD DS autogerenciado, mas com ele você pode executar tarefas adicionais, tais como estender o esquema e criar relações de confiança de floresta.

Os modelos de implantação comuns para um ambiente do AD DS autogerenciado que fornece identidade para aplicativos e serviços na nuvem incluem o seguinte:

* **AD DS autônomo somente em nuvem** – as VMs do Azure são configuradas como controladores de domínio e um ambiente separado do AD DS somente na nuvem é criado. Esse ambiente do AD DS não se integra a um ambiente do AD DS local. Um conjunto diferente de credenciais é usado para entrar em VMs na nuvem e administrá-las.
* **Implantação de floresta de recursos** – as VMs do Azure são configuradas como controladores de domínio e um domínio do AD DS como parte de uma floresta existente é criado. Uma relação de confiança é então configurada para um ambiente do AD DS local. Outras VMs do Azure podem ingressar em domínio nessa floresta de recursos na nuvem. A autenticação do usuário acontece em uma conexão de VPN/ExpressRoute para o ambiente do AD DS local.
* **Estender o domínio local para o Azure** – uma rede virtual do Azure conecta-se a uma rede local usando uma conexão VPN/ExpressRoute. As VMs do Azure se conectam a essa rede virtual do Azure, que permite que elas ingressem no domínio do ambiente do AD DS local.
    * Uma alternativa é criar VMs do Azure e promovê-las como controladores de domínio de réplica do domínio do AD DS local. Esses controladores de domínio fazem replicação por uma conexão de VPN/ExpressRoute para o ambiente do AD DS local. O domínio do AD DS local é estendido efetivamente para o Azure.

A tabela a seguir descreve alguns dos recursos que podem ser necessários para sua organização e as diferenças entre um domínio gerenciado do Azure AD DS e um domínio autogerenciado do AD DS:

| **Recurso** | **Azure AD DS** | **AD DS autogerenciado** |
| ----------- |:---------------:|:----------------------:|
| **Serviço gerenciado**                               | **&#x2713;** | **&#x2715;** |
| **Implantações seguras**                            | **&#x2713;** | O administrador protege a implantação |
| **Servidor DNS**                                    | **&#x2713;** (serviço gerenciado) |**&#x2713;** |
| **Privilégios de administrador corporativo ou de domínio** | **&#x2715;** | **&#x2713;** |
| **Ingresso no domínio**                                   | **&#x2713;** | **&#x2713;** |
| **Autenticação de domínio usando Kerberos e NTLM** | **&#x2713;** | **&#x2713;** |
| **Delegação restrita de Kerberos**               | Baseado em recursos | Baseado em recursos e em conta|
| **Estrutura de UO personalizada**                           | **&#x2713;** | **&#x2713;** |
| **Política de Grupo**                                  | **&#x2713;** | **&#x2713;** |
| **Extensões de esquema**                             | **&#x2715;** | **&#x2713;** |
| **Relações de confiança de floresta/domínio do AD**                     | **&#x2715;** | **&#x2713;** |
| **LDAPS (LDAP Seguro)**                           | **&#x2713;** | **&#x2713;** |
| **Leitura LDAP**                                     | **&#x2713;** | **&#x2713;** |
| **Gravação LDAP**                                    | **&#x2713;** (dentro do domínio gerenciado) | **&#x2713;** |
| **Implantações com distribuição geográfica**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS e Azure AD

O Azure AD permite que você gerencie a identidade dos dispositivos usados pela organização e controle o acesso aos recursos corporativos desses dispositivos. Os usuários também podem registrar os dispositivos pessoais deles (um modelo BYO – traga seu próprio) com o Azure AD, que fornece uma identidade ao dispositivo. O Azure AD autentica então o dispositivo quando um usuário fizer logon no Azure AD e usar o dispositivo para acessar recursos protegidos. O dispositivo pode ser gerenciado usando o software de MDM (Gerenciamento de Dispositivo Móvel), como o Microsoft Intune. Essa capacidade de gerenciamento permite que você restrinja o acesso a recursos confidenciais somente a dispositivos gerenciados e em conformidade com a política.

Computadores e laptops tradicionais também podem ingressar no Azure AD. Esse mecanismo oferece os mesmos benefícios de registrar um dispositivo pessoal com o Azure AD, tais como permitir que os usuários entrem no dispositivo usando as credenciais corporativas deles.

Os dispositivos adicionados ao Azure AD oferecem os seguintes benefícios:

* SSO (logon único) para aplicativos protegidos pelo Azure AD.
* Roaming em conformidade com a política corporativa das configurações de usuário entre dispositivos.
* Acesso à Microsoft Store para Empresas usando credenciais corporativas.
* Windows Hello for Business.
* Acesso restrito aos aplicativos e recursos de dispositivos em conformidade com as políticas corporativas.

Os dispositivos podem ser ingressados no Azure AD com ou sem uma implantação híbrida que inclui um ambiente do AD DS local. A tabela a seguir descreve os modelos de propriedade de dispositivo comuns e como eles normalmente seriam ingressados em um domínio:

| **Tipo de dispositivo**                                        | **Plataformas de dispositivo**             | **Mecanismo**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Dispositivos pessoais                                          | Windows 10, iOS, Android e Mac OS | Registrado no Azure AD    |
| Dispositivo pertencente à organização não adicionado ao AD DS local | Windows 10                       | Adicionado ao Azure AD        |
| Dispositivo pertencente à organização adicionado a um AD DS local  | Windows 10                       | Adicionado ao Azure AD híbrido |

Em um dispositivo registrado ou ingressado no Azure AD, a autenticação de usuário ocorre por meio do uso de protocolos modernos baseados em OAuth/OpenID Connect. Esses protocolos são projetados para funcionarem pela Internet e são ótimos para cenários móveis, nos quais os usuários acessam recursos corporativos de qualquer lugar.

Com os dispositivos ingressados no Azure AD DS, os aplicativos podem usar os protocolos Kerberos e NTLM para autenticação e, portanto, podem dar suporte a aplicativos herdados migrados para execução em VMs do Azure como parte de uma estratégia de lift-and-shift. A tabela a seguir mostra as diferenças em como os dispositivos são representados e podem se autenticar no diretório:

| **Aspecto**                      | **Ingressados no Azure AD**                                 | **Ingressados no Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Dispositivo controlado por            | AD do Azure                                            | Domínio gerenciado do Azure AD DS                                                |
| Representação no diretório | Objetos de dispositivo no diretório do Azure AD            | Objetos de computador no domínio gerenciado do Azure AD DS                        |
| Authentication                  | Protocolos baseados em OAuth/OpenID Connect              | Protocolos NTLM e Kerberos                                               |
| Gerenciamento                      | Software de MDM (Gerenciamento de Dispositivo Móvel) como o Intune | Política de Grupo                                                              |
| Rede                      | Funciona pela Internet                             | Deve estar conectado à rede virtual em que o domínio gerenciado está implantado ou estar emparelhado com ela |
| Excelente para...                    | Dispositivos da área de trabalho ou móveis de usuários finais                  | VMs de servidor implantadas no Azure                                              |

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure AD DS, [crie um domínio gerenciado do Azure AD DS usando o portal do Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
