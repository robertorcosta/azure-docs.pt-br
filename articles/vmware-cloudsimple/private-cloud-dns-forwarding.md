---
title: Solução Azure VMware - Encaminhamento de DNS de nuvem privada para no local
description: Descreve como habilitar seu servidor CloudSimple Private Cloud DNS para encaminhar a busca de recursos no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961121"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Habilite os servidores CloudSimple Private Cloud DNS para encaminhar a busca de Recursos locais para seus servidores DNS

Os servidores DNS privados da Nuvem podem encaminhar a busca de DNS para quaisquer recursos locais para seus servidores DNS.  A ativação da pesquisa permite que os componentes private cloud vSphere procurem quaisquer serviços em execução em seu ambiente local e se comuniquem com eles usando nomes de domínio totalmente qualificados (FQDN).

## <a name="scenarios"></a>Cenários 

O encaminhamento de pesquisas de DNS para o servidor DNS no local permite que você use sua Nuvem Privada para os seguintes cenários:

* Use o Private Cloud como uma configuração de recuperação de desastres para sua solução VMware no local
* Use o Active Directory no local como fonte de identidade para o seu vSphere da Nuvem Privada
* Use hcx para migrar máquinas virtuais de on-premises para Private Cloud

## <a name="before-you-begin"></a>Antes de começar

Uma conexão de rede deve estar presente desde sua rede Private Cloud até sua rede local para o encaminhamento de DNS para o trabalho.  Você pode configurar a conexão de rede usando:

* [Conecte-se de on-premises ao CloudSimple usando expressroute](on-premises-connection.md)
* [Configure um gateway VPN site-to-site](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

As portas de firewall devem ser abertas nesta conexão para o encaminhamento de DNS para o trabalho.  As portas utilizadas são a porta TCP 53 ou a porta UDP 53.

> [!NOTE]
> Se você estiver usando vpn site-to-site, sua sub-rede de servidor DNS no local deve ser adicionada como parte de prefixos no local.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Solicitar o encaminhamento de DNS do Private Cloud para os locais

Para habilitar o encaminhamento de DNS do Private Cloud para o local, envie uma solicitação de [suporte,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações.

* Tipo de emissão: **Técnico**
* Assinatura: Assinatura onde o **serviço CloudSimple é implantado**
* Serviço: **Solução VMware pela CloudSimple**
* Tipo de **problema: Assessoria ou Como eu...**
* Subtipo de **problema: Precisa de ajuda com NW**
* Forneça o nome de domínio do seu domínio local no painel de detalhes.
* Forneça a lista de seus servidores DNS no local para os quais a busca será encaminhada da sua nuvem privada no painel de detalhes.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a configuração do firewall no local](on-premises-firewall-configuration.md)
* [Configuração do servidor DNS no local](on-premises-dns-setup.md)
