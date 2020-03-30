---
title: Azure VMware Solution by CloudSimple - Configure o Private Cloud como um site de recuperação de desastres usando o VMware Site Recovery Manager
description: Descreve como configurar sua Cloud Private Cloud cloud como um site de recuperação de desastres para cargas de trabalho VMware no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565920"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configure o Private Cloud como um alvo de recuperação de desastres com o VMware Site Recovery Manager

Você pode usar o CloudSimple Private Cloud como um site de recuperação de desastres (DR) para cargas de trabalho vMware no local.

A solução DR é baseada no srm (VSphere Replication replication and vMware Site Recovery Manager, replicação do vSphere) e no VMware. Uma abordagem semelhante pode ser seguida para habilitar sua Nuvem Privada como um site principal protegido pelo seu site de recuperação no local.

A solução CloudSimple:

* Elimina a necessidade de configurar um data center especificamente para DR.
* Permite aproveitar os locais do Azure onde o CloudSimple é implantado para resiliência geográfica mundial.
* Dá-lhe uma opção para reduzir os custos de implantação e o custo total de propriedade para estabelecer o DR.

A solução CloudSimple exige que você faça o seguinte:

* Instale, configure e gerencie a replicação vSphere e o SRM em sua Nuvem Privada.
* Forneça suas próprias licenças para srm quando a Nuvem Privada for o site protegido. Você não precisa de nenhuma licença adicional de SRM para o site CloudSimple quando ele é usado como o site de recuperação.

Com esta solução, você tem controle total sobre a replicação vSphere e srm. As interfaces de interface de interface, API e CLI familiares permitem o uso de seus scripts e ferramentas existentes.

![Implantação do Gerenciador de Recuperação de Sites](media/srm-deployment.png)

Você pode usar quaisquer versões de vRA e SRM compatíveis com sua Nuvem Privada e ambientes locais. Os exemplos deste guia usam vRA 6.5 e SRM 6.5. Essas versões são compatíveis com o vSphere 6.5, que é suportado pelo CloudSimple.

## <a name="deploy-the-solution"></a>Implantar a solução

As seções a seguir descrevem como implantar uma solução DR usando srm em sua Nuvem Privada.

1. [Verifique se as versões do produto VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estimar o tamanho do seu ambiente DR](#estimate-the-size-of-your-dr-environment)
3. [Crie uma Nuvem Privada para o seu ambiente](#create-a-private-cloud-for-your-environment)
4. [Configurar rede private cloud para a solução SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Configure uma conexão VPN site-to-site entre sua rede local e a Nuvem Privada e abra as portas necessárias](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Configure serviços de infra-estrutura em sua Nuvem Privada](#set-up-infrastructure-services-in-your-private-cloud)
7. [Instale o aparelho de replicação vSphere em seu ambiente local](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instale o dispositivo de replicação vSphere em seu ambiente Private Cloud](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Instale o servidor SRM no ambiente local](#install-srm-server-in-your-on-premises-environment)
10. [Instale o servidor SRM em sua Nuvem Privada](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verifique se as versões do produto VMware são compatíveis

As configurações deste guia estão sujeitas aos seguintes requisitos de compatibilidade:

* A mesma versão do SRM deve ser implantada em sua Nuvem Privada e no seu ambiente local.
* A mesma versão da replicação vSphere deve ser implantada em sua Nuvem Privada e no ambiente local.
* As versões do Platform Services Controller (PSC) em sua Nuvem Privada e seu ambiente local devem ser compatíveis.
* As versões do vCenter em sua Nuvem Privada e seu ambiente local devem ser compatíveis.
* As versões de replicação SRM e vSphere devem ser compatíveis entre si e com as versões do PSC e do vCenter.

Para obter links para as informações relevantes sobre documentação e compatibilidade do VMware, acesse a documentação [do VMware Site Recovery Manager.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Para descobrir as versões do vCenter e PSC em sua Nuvem Privada, abra o portal CloudSimple. Vá para **Recursos,** selecione sua Nuvem Privada e clique na guia **vSphere Management Network.**

![vCenter & versões de PSC no Private Cloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Estimar o tamanho do seu ambiente DR

1. Verifique se a configuração local identificada está dentro dos limites suportados. Para srm 6.5, os limites estão documentados no artigo base de conhecimento VMware sobre [limites operacionais para o Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110).
2. Certifique-se de que você tem largura de banda de rede suficiente para atender ao tamanho da carga de trabalho e aos requisitos de RPO. O artigo base de conhecimento do VMware sobre [o cálculo dos requisitos de largura de banda para a replicação vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) fornece orientação sobre os limites de largura de banda.
3. Use a ferramenta Dernador CloudSimple para estimar os recursos necessários em seu site de DR para proteger seu ambiente local.

### <a name="create-a-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada para o seu ambiente

Crie uma Nuvem Privada a partir do portal CloudSimple seguindo as instruções e recomendações de dimensionamento em [Criar uma Nuvem Privada](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Configurar rede private cloud para a solução SRM

Acesse o portal CloudSimple para configurar a rede Private Cloud para a solução SRM.

Crie uma VLAN para a rede de soluções SRM e atribua-a uma CIDR de sub-rede. Para obter instruções, consulte [Criar e gerenciar VLANs/Subnets](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Configure uma conexão VPN site-to-site entre sua rede local e a Nuvem Privada e abra as portas necessárias

Para configurar a conectividade site-to-site entre sua rede local e sua Nuvem Privada. Para obter instruções, [consulte Configure uma conexão VPN com a nuvem privada CloudSimple](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Configure serviços de infra-estrutura em sua Nuvem Privada

Configure serviços de infra-estrutura na Nuvem Privada para facilitar o gerenciamento de suas cargas de trabalho e ferramentas.

Você pode adicionar um provedor de identidade externo conforme descrito no [Use Azure AD como um provedor de identidade para o vCenter no CloudSimple Private Cloud,](azure-ad.md) se quiser fazer qualquer um dos seguintes procedimentos:

* Identifique os usuários do seu Active Directory (AD) no local em sua Nuvem Privada.
* Configure um AD em sua Nuvem Privada para todos os usuários.
* Use Azure AD.

Para fornecer pesquisa de endereçoip, gerenciamento de endereços IP e serviços de resolução de nomes para suas cargas de trabalho na Nuvem Privada, configure um servidor DHCP e DNS conforme descrito em [configurar aplicativos e cargas de trabalho DNS e DHCP em sua Cloud Private Cloud.](dns-dhcp-setup.md)

O domínio *.cloudsimple.io é usado por VMs de gerenciamento e hosts em sua Nuvem Privada. Para resolver as solicitações a este domínio, configure o encaminhamento de DNS no servidor DNS conforme descrito em [Criar um encaminhador condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instale o vSphere Replication Appliance em seu ambiente local

Instale o vSphere Replication Appliance (vRA) em seu ambiente local seguindo a documentação do VMware. A instalação consiste nessas etapas de alto nível:

1. Prepare seu ambiente local para instalação de vRA.

2. Implante o vRA em seu ambiente local usando o OVF no ISO VR de vmware.com. Para vRA 6.5, [este blog vMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem as informações relevantes.

3. Registre seu vRA no local com o vCenter Single Sign-On no site local. Para obter instruções detalhadas sobre a replicação vSphere 6.5, consulte o documento [VMware VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Instale o dispositivo de replicação vSphere em seu ambiente Private Cloud

Antes de começar, verifique se você tem o seguinte:

* Capacidade de alcance ip de sub-redes em seu ambiente local para a sub-rede de gerenciamento do seu Nuvem Privada
* Capacidade de alcance IP da sub-rede de replicação em seu ambiente vSphere no local até a sub-rede de solução SRM da sua Nuvem Privada

Para obter instruções, [consulte Configure uma conexão VPN com a nuvem privada CloudSimple](set-up-vpn.md). As etapas são semelhantes às da instalação no local.

O CloudSimple recomenda o uso de FQDNs em vez de endereços IP durante a instalação do vRA e do SRM. Para descobrir o FQDN do vCenter e PSC em sua Nuvem Privada, abra o portal CloudSimple. Vá para **Recursos,** selecione sua Nuvem Privada e clique na guia **vSphere Management Network.**

![Encontrando FQDN do vCenter/PSC em Nuvem Privada](media/srm-resources.png)

O CloudSimple exige que você não instale o vRA e o SRM usando o usuário 'cloudowner' padrão, mas, em vez disso, crie um novo usuário. Isso é feito para ajudar a garantir alto tempo de atividade e disponibilidade para o seu ambiente private cloud vCenter. No entanto, o usuário padrão do cloudowner no Private Cloud vCenter não tem privilégios suficientes para criar um novo usuário com privilégios administrativos.

Antes de instalar o vRA e o SRM, você deve escalar os privilégios do vCenter do usuário cloudowner e, em seguida, criar um usuário com privilégios administrativos no domínio vCenter SSO. Para obter detalhes sobre o modelo padrão de permissão e usuário da Nuvem Privada, consulte [Aprenda o modelo de permissão da Private Cloud](learn-private-cloud-permissions.md).

A instalação consiste nessas etapas de alto nível:

1. [Escalar privilégios.](escalate-private-cloud-privileges.md)
2. Crie um usuário em sua Nuvem Privada para a replicação vSphere e instalação do SRM. Explicado abaixo no [vCenter UI: Crie um usuário em Nuvem Privada para instalação de & SRM de vRA](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Prepare seu ambiente de Nuvem Privada para instalação de vRA.
4. Implantar vRA em sua Nuvem Privada usando o OVF no ISO VR de vmware.com. Para vRA 6.5, [este blog vMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem informações relevantes.
5. Configure as regras de firewall para vRA. Explicado abaixo no [portal CloudSimple: Configure regras de Firewall para vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registre o VRA do Private Cloud com o vCenter Single Sign-On no site da Private Cloud.
7. Configure as conexões de replicação vSphere entre os dois aparelhos. Certifique-se de que as portas necessárias sejam abertas entre os firewalls. Consulte este artigo base de [conhecimento da VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de portas que devem estar abertos para a replicação vSphere 6.5.

Para obter instruções detalhadas de instalação para a replicação vSphere 6.5, consulte o documento [VMware VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>iU do vCenter: Crie um usuário em Nuvem Privada para instalação de vRA e SRM

Faça login no vCenter usando credenciais de usuário do cloudowner após aumentar os privilégios do portal CloudSimple.

Crie um novo `srm-soln-admin`usuário, no vCenter e adicione-o ao grupo de administradores no vCenter.
Faça login no vCenter como usuário de cloudowner e faça login como usuário *srm-soln-admin.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>Portal CloudSimple: Configure regras de firewall para vRA

Configure as regras de firewall conforme descrito em [Configurar tabelas de firewall e regras](firewall.md) para abrir portas para permitir a comunicação entre:

* vRA na rede de soluções SRM e hosts vCenter e ESXi na rede de gerenciamento.
* aparelhos vRA nos dois locais.

Consulte este artigo base de [conhecimento da VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de portas que devem estar abertos para a replicação vSphere 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instale o servidor SRM no ambiente local

Antes de começar, verifique o seguinte:

* O vSphere Replication Appliance está instalado em seus ambientes on-premises e Private Cloud.
* Os dispositivos de replicação vSphere em ambos os locais estão conectados entre si.
* Você revisou as informações da VMware sobre pré-requisitos e práticas recomendadas. Para SRM 6.5, você pode consultar os [pré-requisitos e práticas recomendadas](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)do documento VMware para SRM 6.5 .

Siga a documentação do VMware para executar a instalação do servidor SRM no modelo de implantação 'Topologia de dois andares com uma instância de vCenter por controlador de serviços de plataforma' conforme descrito neste [documento VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação do SRM 6.5 estão disponíveis no documento VMware [Installing Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Instale o servidor SRM em sua Nuvem Privada

Antes de começar, verifique o seguinte:

* O vSphere Replication Appliance está instalado em seus ambientes on-premises e Private Cloud.
* Os dispositivos de replicação vSphere em ambos os locais estão conectados entre si.
* Você revisou as informações da VMware sobre pré-requisitos e práticas recomendadas. Para SRM 6.5, você pode consultar [pré-requisitos e práticas recomendadas para o Gerenciador de Recuperação de Site 6.5 Server Installation](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

As etapas a seguir descrevem a instalação private cloud SRM.

1. [vCenter UI: Instale o SRM](#vcenter-ui-install-srm)
2. [Portal CloudSimple: Configure regras de firewall para SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [iU do vCenter: Configure SRM](#vcenter-ui-configure-srm)
4. [Portal CloudSimple: desescalar privilégios](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: Instale o SRM

Após fazer login no vCenter usando credenciais srm-soln-admin, siga a documentação do VMware para executar a instalação do servidor SRM no modelo de implantação 'Topologia de dois sites com uma instância de vCenter por Controlador de Serviços de Plataforma' conforme descrito neste [documento vMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação do SRM 6.5 estão disponíveis no documento VMware [Installing Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portal CloudSimple: Configure regras de firewall para SRM

Configure as regras de firewall conforme descrito em [Configurar tabelas de firewall e regras](firewall.md) para permitir a comunicação entre:

O servidor SRM e o vCenter/PSC na Nuvem Privada.
Os servidores SRM em ambos os locais

Consulte este artigo base de [conhecimento da VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de portas que devem estar abertos para a replicação vSphere 6.5.

#### <a name="vcenter-ui-configure-srm"></a>iU do vCenter: Configure SRM

Depois que o SRM for instalado na nuvem privada, execute as seguintes tarefas conforme descrito nas seções do VMware Site Recovery Manager Installation and Configuration Guide. Para SRM 6.5, as instruções estão disponíveis no documento VMware Instalando o [Gerenciador de Recuperação do Site](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Conecte as instâncias do servidor do gerenciador de recuperação de site nos sites protegidos e de recuperação.
2. Estabeleça uma conexão com o cliente à instância do servidor do gerenciador de recuperação de site remoto.
3. Instale a chave de licença do gerenciador de recuperação do site.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portal CloudSimple: Desescalar privilégios

Para desescalar privilégios, consulte [De-escalar privilégios.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-srm-solution"></a>Gerenciamento contínuo da sua solução SRM

Você tem controle total sobre a replicação vSphere e o software SRM em seu ambiente Private Cloud e espera-se que execute o gerenciamento necessário do ciclo de vida do software. Certifique-se de que qualquer nova versão do software seja compatível com o Private Cloud vCenter e PSC antes de atualizar ou atualizar a replicação vSphere ou o SRM.

> [!NOTE]
> CloudSimple está atualmente explorando opções para oferecer um serviço de DR gerenciado. 

## <a name="multiple-replication-configuration"></a>Configuração de replicação múltipla

 [Ambas as tecnologias de replicação baseadas em array e vSphere podem ser usadas em conjunto com](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) o SRM ao mesmo tempo. No entanto, eles devem ser aplicados a um conjunto separado de VMs (uma determinada VM pode ser protegida por replicação baseada em array ou replicação vSphere, mas não ambas). Além disso, o site CloudSimple pode ser configurado como um site de recuperação para vários sites protegidos. Consulte [opções multisites srm](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) para obter informações sobre configurações em vários sites.

## <a name="references"></a>Referências

* [Documentação do Gerenciador de Recuperação de Sites da VMware](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limites operacionais para gerente de recuperação de site 6.5](https://kb.vmware.com/s/article/2147110)
* [Calculando os requisitos de largura de banda para replicação do vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [Opções de OVF ao implantar a replicação vSphere 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replication 6.5 Instalação e Configuração](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Pré-requisitos e melhores práticas para SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Gerente de recuperação de site em uma topologia de dois sites com uma instância de servidor vCenter por controlador de serviços de plataforma](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 Guia de instalação e configuração](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog vMware no SRM com replicação baseada em array vs. replicação vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog vMware sobre opções de vários sites srm](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Números de porta que devem estar abertos para a replicação vSphere 5.8.x, 6.x e 8](https://kb.vmware.com/s/article/2147112)
