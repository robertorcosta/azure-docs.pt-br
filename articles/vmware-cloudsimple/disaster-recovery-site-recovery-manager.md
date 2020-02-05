---
title: Soluções VMware do Azure (AVS)-configurar a nuvem privada de AVS como um site de recuperação de desastre usando o VMware Site Recovery Manager
description: Descreve como configurar sua nuvem privada de AVS como um site de recuperação de desastre para cargas de trabalho do VMware locais
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bbd8a12820432ce2f131dda29af6740a2f04e18
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024714"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configurar a nuvem privada de AVS como um destino de recuperação de desastre com o VMware Site Recovery Manager

Você pode usar sua nuvem privada de AVS como um site de recuperação de desastre (DR) para cargas de trabalho do VMware locais.

A solução de DR é baseada em vSphere Replication e VMware Site Recovery Manager (SRM). Uma abordagem semelhante pode ser seguida para habilitar sua nuvem privada de AVS como um site primário que é protegido pelo site de recuperação local.

A solução AVS:

* Elimina a necessidade de configurar um datacenter especificamente para DR.
* Permite que você aproveite os locais do Azure em que a AVS é implantada para resiliência geográfica mundial.
* Oferece uma opção para reduzir os custos de implantação e o custo total de propriedade para estabelecer a DR.

A solução AVS exige que você faça o seguinte:

* Instale, configure e gerencie a replicação e o SRM do vSphere em sua nuvem privada de AVS.
* Forneça suas próprias licenças para SRM quando a nuvem privada da AVS for o site protegido. Você não precisa de nenhuma licença de SRM adicional para o site da AVS quando ele é usado como o site de recuperação.

Com essa solução, você tem controle total sobre a replicação e o SRM do vSphere. A interface do usuário familiar, API e interfaces CLI permitem o uso de seus scripts e ferramentas existentes.

![Implantação do Site Recovery Manager](media/srm-deployment.png)

Você pode usar qualquer versão do vRA e do SRM que seja compatível com sua nuvem privada de AVS e ambientes locais. Os exemplos neste guia usam o vRA 6,5 e o SRM 6,5. Essas versões são compatíveis com o vSphere 6,5, que é compatível com o AVS.

## <a name="deploy-the-solution"></a>Implantar a solução

As seções a seguir descrevem como implantar uma solução de DR usando o SRM em sua nuvem privada de AVS.

1. [Verificar se as versões do produto VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estimar o tamanho do ambiente de recuperação de desastre](#estimate-the-size-of-your-dr-environment)
3. [Criar uma nuvem privada de AVS para seu ambiente](#create-an-avs-private-cloud-for-your-environment)
4. [Configurar a rede de nuvem privada AVS para a solução de SRM](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Configurar uma conexão VPN site a site entre sua rede local e a nuvem privada da AVS e as portas necessárias abertas](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Configurar serviços de infraestrutura em sua nuvem privada de AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [Instalar o dispositivo de replicação vSphere no seu ambiente local](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instalar o dispositivo de replicação vSphere em seu ambiente de nuvem privada de AVS](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Instalar o servidor SRM em seu ambiente local](#install-srm-server-in-your-on-premises-environment)
10. [Instalar o servidor de SRM em sua nuvem privada de AVS](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificar se as versões do produto VMware são compatíveis

As configurações neste guia estão sujeitas aos seguintes requisitos de compatibilidade:

* A mesma versão do SRM deve ser implantada em sua nuvem privada de AVS e em seu ambiente local.
* A mesma versão da replicação do vSphere deve ser implantada em sua nuvem privada de AVS e em seu ambiente local.
* As versões do controlador de serviços de plataforma (PSC) em sua nuvem privada de AVS e seu ambiente local devem ser compatíveis.
* As versões do vCenter em sua nuvem privada de AVS e seu ambiente local devem ser compatíveis.
* As versões do SRM e do vSphere Replication devem ser compatíveis entre si e com as versões do PSC e do vCenter.

Para obter links para a documentação relevante do VMware e informações de compatibilidade, acesse a documentação do [vmware site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Para descobrir as versões do vCenter e do PSC em sua nuvem privada da AVS, abra o portal da AVS. Vá para **recursos**, selecione sua nuvem privada da AVS e clique na guia **rede de gerenciamento do vSphere** .

![vCenter & versões do PSC na nuvem privada da AVS](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Estimar o tamanho do ambiente de recuperação de desastre

1. Verifique se sua configuração local identificada está dentro dos limites com suporte. Para o SRM 6,5, os limites estão documentados no artigo da base de dados de conhecimento VMware sobre [os limites operacionais do site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Verifique se você tem largura de banda de rede suficiente para atender aos requisitos de tamanho e RPO da carga de trabalho. O artigo da base de dados de conhecimento do VMware sobre o [cálculo dos requisitos de largura de banda para a replicação do vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) fornece orientação sobre limites
3. Use a ferramenta de dimensionamento AVS para estimar os recursos necessários em seu site de DR para proteger seu ambiente local.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Criar uma nuvem privada de AVS para seu ambiente

Crie uma nuvem privada de AVS no portal da AVS seguindo as instruções e as recomendações de dimensionamento em [criar uma nuvem privada de AVS](create-private-cloud.md).

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Configurar a rede de nuvem privada AVS para a solução de SRM

Acesse o portal da AVS para configurar a rede de nuvem privada AVS para a solução de SRM.

Crie uma VLAN para a rede de solução de SRM e atribua a ela um CIDR de sub-rede. Para obter instruções, consulte [criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Configurar uma conexão VPN site a site entre sua rede local e a nuvem privada da AVS e as portas necessárias abertas

Para configurar a conectividade site a site entre sua rede local e sua nuvem privada de AVS. Para obter instruções, consulte [Configurar uma conexão VPN para sua nuvem privada da AVS](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Configurar serviços de infraestrutura em sua nuvem privada de AVS

Configure serviços de infraestrutura na nuvem privada da AVS para facilitar o gerenciamento de suas cargas de trabalho e ferramentas.

Você pode adicionar um provedor de identidade externo conforme descrito em [usar o Azure ad como um provedor de identidade para vCenter na nuvem privada de AVS](azure-ad.md) se desejar fazer o seguinte:

* Identifique os usuários do seu Active Directory local (AD) em sua nuvem privada da AVS.
* Configure um anúncio em sua nuvem privada de AVS para todos os usuários.
* Use o Azure AD.

Para fornecer pesquisa de endereço IP, gerenciamento de endereços IP e serviços de resolução de nomes para suas cargas de trabalho na nuvem privada da AVS, configure um servidor DHCP e DNS, conforme descrito em [configurar aplicativos DNS e DHCP e cargas de trabalho em sua nuvem privada de AVS](dns-dhcp-setup.md).

O domínio *. avs.io é usado por VMs de gerenciamento e hosts em sua nuvem privada de AVS. Para resolver solicitações para esse domínio, configure o encaminhamento de DNS no servidor DNS, conforme descrito em [criar um encaminhador condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instalar o dispositivo de replicação vSphere no seu ambiente local

Instale o vSphere Replication Appliance (vRA) no seu ambiente local seguindo a documentação do VMware. A instalação consiste nessas etapas de alto nível:

1. Prepare seu ambiente local para a instalação do vRA.

2. Implante o vRA no seu ambiente local usando o OVF no ISO VR do vmware.com. Para o vRA 6,5, [este blog do VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem as informações relevantes.

3. Registre seu vRA local com o logon único do vCenter no site local. Para obter instruções detalhadas sobre a replicação do vSphere 6,5, consulte a instalação e a configuração do VMware Document [VMware vSphere Replication 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Instalar o dispositivo de replicação vSphere em seu ambiente de nuvem privada de AVS

Antes de começar, verifique se você tem o seguinte:

* Acessibilidade de IP de sub-redes em seu ambiente local para a sub-rede de gerenciamento da sua nuvem privada de AVS
* Acessibilidade de IP da sub-rede de replicação em seu ambiente de vSphere local para a sub-rede da solução de SRM da sua nuvem privada de AVS

Para obter instruções, consulte [Configurar uma conexão VPN para sua nuvem privada da AVS](set-up-vpn.md). As etapas são semelhantes às da instalação local.

A AVS recomenda o uso de FQDNs em vez de endereços IP durante a instalação do vRA e do SRM. Para descobrir o FQDN do vCenter e do PSC em sua nuvem privada da AVS, abra o portal da AVS. Vá para **recursos**, selecione sua nuvem privada da AVS e clique na guia **rede de gerenciamento do vSphere** .

![Localizando o FQDN do vCenter/PSC na nuvem privada de AVS](media/srm-resources.png)

O AVS requer que você não instale o vRA e o SRM usando o usuário padrão ' cloudowner ', mas, em vez disso, crie um novo usuário. Isso é feito para ajudar a garantir um alto tempo de atividade e disponibilidade para seu ambiente de vCenter privada de nuvem da AVS. No entanto, o usuário cloudowner padrão na nuvem privada de sincronização automática não tem privilégios suficientes para criar um novo usuário com privilégios administrativos.

Antes de instalar o vRA e o SRM, você deve escalonar os privilégios do vCenter do usuário cloudowner e, em seguida, criar um usuário com privilégios administrativos no domínio de SSO do vCenter. Para obter detalhes sobre o modelo de permissão e usuário de nuvem privada padrão da AVS, consulte [saiba o modelo de permissão da nuvem privada da AVS](learn-private-cloud-permissions.md).

A instalação consiste nessas etapas de alto nível:

1. [Escalonar privilégios](escalate-private-cloud-privileges.md).
2. Crie um usuário em sua nuvem privada da AVS para a replicação do vSphere e a instalação do SRM. Explicado abaixo na [interface do usuário do vCenter: Crie um usuário na nuvem privada da AVS para a instalação do vRA & SRM](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Prepare seu ambiente de nuvem privada de AVS para a instalação do vRA.
4. Implante o vRA em sua nuvem privada de AVS usando o OVF no ISO VR do vmware.com. Para o vRA 6,5, [este blog do VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) tem informações relevantes.
5. Configurar regras de firewall para vRA. Explicado abaixo no [portal da AVS: configurar regras de firewall para vRA](#avs-portal-configure-firewall-rules-for-vra).
6. Registrar a sincronização de nuvem privada vRA com o logon único do vCenter no site da nuvem privada da AVS.
7. Configurar conexões de replicação vSphere entre os dois dispositivos. Verifique se as portas necessárias estão abertas nos firewalls. Consulte [Este artigo da base de dados de conhecimento do VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem ser abertos para o vSphere Replication 6,5.

Para obter instruções detalhadas de instalação do vSphere Replication 6,5, consulte a [instalação e a configuração do VMware document VMware vSphere Replication 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>interface do usuário do vCenter: criar um usuário na nuvem privada da AVS para a instalação do vRA e do SRM

Entre no vCenter usando as credenciais de usuário do cloudowner após o escalonamento de privilégios do portal da AVS.

Crie um novo usuário, `srm-soln-admin`, no vCenter e adicione-o ao grupo Administradores no vCenter.
Saia do vCenter como o usuário cloudowner e entre como o usuário *SRM-soln-admin* .

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>Portal de sincronização automática: configurar regras de firewall para vRA

Configure as regras de firewall, conforme descrito em [configurar regras e tabelas de firewall](firewall.md) para abrir portas para habilitar a comunicação entre:

* vRA na rede de soluções de SRM e nos hosts vCenter e ESXi na rede de gerenciamento.
* dispositivos vRA nos dois sites.

Consulte este [artigo da base de dados de conhecimento do VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem ser abertos para o vSphere Replication 6,5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instalar o servidor SRM em seu ambiente local

Antes de começar, verifique o seguinte:

* o dispositivo de replicação vSphere é instalado em seus ambientes de nuvem privada locais e AVS.
* Os dispositivos de replicação vSphere em ambos os sites estão conectados entre si.
* Você analisou as informações do VMware sobre os pré-requisitos e as práticas recomendadas. Para o SRM 6,5, você pode consultar os pré-requisitos de documento do VMware [e as práticas recomendadas para o SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Siga a documentação do VMware para executar a instalação do servidor SRM na topologia de dois sites do modelo de implantação com uma instância do vCenter por controlador de serviços de plataforma, conforme descrito neste [documento do VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação do SRM 6,5 estão disponíveis no documento VMware [instalando site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Instalar o servidor de SRM em sua nuvem privada de AVS

Antes de começar, verifique o seguinte:

* o dispositivo de replicação vSphere é instalado em seus ambientes de nuvem privada locais e AVS.
* Os dispositivos de replicação vSphere em ambos os sites estão conectados entre si.
* Você analisou as informações do VMware sobre os pré-requisitos e as práticas recomendadas. Para o SRM 6,5, você pode consultar os [pré-requisitos e as práticas recomendadas para a instalação do servidor do site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

As etapas a seguir descrevem a instalação de SRM de nuvem privada da AVS.

1. [interface do usuário do vCenter: instalar o SRM](#vcenter-ui-install-srm)
2. [Portal de sincronização automática: configurar regras de firewall para SRM](#avs-portal-configure-firewall-rules-for-srm)
3. [interface do usuário do vCenter: configurar SRM](#vcenter-ui-configure-srm)
4. [Portal da AVS: desescalonamento de privilégios](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>interface do usuário do vCenter: instalar o SRM

Depois de fazer logon no vCenter usando credenciais de soln de SRM, siga a documentação do VMware para executar a instalação do servidor SRM na topologia de dois sites do modelo de implantação com uma instância do vCenter por controlador de serviços de plataforma, conforme descrito neste [documento do VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). As instruções de instalação do SRM 6,5 estão disponíveis no documento VMware [instalando site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>Portal de sincronização automática: configurar regras de firewall para SRM

Configure as regras de firewall, conforme descrito em [configurar regras e tabelas de firewall](firewall.md) para permitir a comunicação entre:

O servidor de SRM e o vCenter/PSC na nuvem privada da AVS.
Os servidores de SRM em ambos os sites

Consulte [Este artigo da base de dados de conhecimento do VMware](https://kb.vmware.com/s/article/2087769) para obter uma lista de números de porta que devem ser abertos para o vSphere Replication 6,5.

#### <a name="vcenter-ui-configure-srm"></a>interface do usuário do vCenter: configurar SRM

Após a instalação do SRM na nuvem privada da AVS, execute as seguintes tarefas, conforme descrito nas seções do guia de instalação e configuração do VMware Site Recovery Manager. Para o SRM 6,5, as instruções estão disponíveis no documento VMware [instalando site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Conecte as instâncias de servidor do Site Recovery Manager nos sites protegidos e de recuperação.
2. Estabeleça uma conexão de cliente com a instância de servidor do Gerenciador de Site Recovery remoto.
3. Instale a chave de licença do Site Recovery Manager.

#### <a name="avs-portal-de-escalate-privileges"></a>Portal da AVS: desescalonamento de privilégios

Para desescalonar privilégios, consulte [desescalonamento de privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Gerenciamento contínuo da sua solução de SRM

Você tem controle total sobre a replicação vSphere e o software de SRM em seu ambiente de nuvem privada de AVS e espera executar o gerenciamento de ciclo de vida de software necessário. Certifique-se de que qualquer nova versão do software seja compatível com o vCenter e o PSC da nuvem privada da AVS antes de atualizar ou atualizar a replicação ou o SRM do vSphere.

> [!NOTE]
> Atualmente, a AVS está explorando opções para oferecer um serviço de DR gerenciado. 

## <a name="multiple-replication-configuration"></a>Configuração de replicação múltipla

 A [replicação baseada em matriz e as tecnologias de replicação vSphere podem ser usadas junto com o SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) ao mesmo tempo. No entanto, eles devem ser aplicados a um conjunto separado de VMs (uma determinada VM pode ser protegida por replicação baseada em matriz ou replicação vSphere, mas não ambos). Além disso, o site da AVS pode ser configurado como um site de recuperação para vários sites protegidos. Consulte [Opções de vários sites do SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) para obter informações sobre configurações multissite.

## <a name="references"></a>Referências

* [Documentação do VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limites operacionais para o Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Calculando requisitos de largura de banda para replicação do vSphere](https://kb.vmware.com/s/article/2037268)
* [Opções de OVF ao implantar a replicação do vSphere 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [Instalação e configuração do VMware vSphere Replication 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Pré-requisitos e práticas recomendadas para SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager em uma topologia de dois sites com uma instância de vCenter Server por controlador de serviços de plataforma](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Guia de instalação e configuração do VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog do VMware sobre SRM com replicação baseada em matriz versus replicação vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog do VMware sobre opções de vários sites de SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Os números de porta que devem ser abertos para vSphere Replication 5.8. x, 6. x e 8](https://kb.vmware.com/s/article/2147112)
