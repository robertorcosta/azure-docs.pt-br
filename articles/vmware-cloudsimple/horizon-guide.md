---
title: Azure VMware Solution by CloudSimple - Use o site Private Cloud para hospedar uma infra-estrutura de desktop virtual usando o VMware Horizon
description: Descreve como você pode usar seu site CloudSimple Private Cloud para hospedar uma infra-estrutura de desktop virtual usando o VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025241"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Use o site CloudSimple Private Cloud para hospedar uma infra-estrutura de desktop virtual usando o VMware Horizon

Você pode usar seu site CloudSimple Private Cloud para hospedar uma infra-estrutura de desktop virtual (VDI) usando o VMware Horizon 7.x. A figura a seguir mostra a arquitetura de solução lógica para o VDI.

![Implantação do Horizon](media/horizon-deployment.png)

Com esta solução, você tem controle total sobre horizon view manager e volume de aplicativos. As interfaces de interface de interface, API e CLI familiares permitem o uso de seus scripts e ferramentas existentes.

A solução CloudSimple exige que você faça o seguinte:

* Instale, configure e gerencie o VMware Horizon 7.x em sua Nuvem Privada.
* Forneça maquetes da Horizon.

## <a name="deploy-the-solution"></a>Implantar a solução

As seções a seguir descrevem como implantar uma solução VDI usando horizon em sua Nuvem Privada.

1. [Verifique se as versões do produto VMware são compatíveis](#verify-that-vmware-product-versions-are-compatible)
2. [Estime o tamanho do seu ambiente de desktop](#estimate-the-size-of-your-desktop-environment)
3. [Crie uma Nuvem Privada para o seu ambiente](#create-a-private-cloud-for-your-environment)
4. [Instale o VMware Horizon em sua nuvem privada](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verifique se as versões do produto VMware são compatíveis

* Verifique se suas versões atuais e planejadas do Horizon, Volumes de Aplicativos, Gateway de Acesso Unificado e Gerente de Ambiente do Usuário são compatíveis entre si e com o vCenter e PSC na Nuvem Privada. Para obter informações sobre compatibilidade, consulte [Matriz de Compatibilidade VMware para Horizon 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Para descobrir as versões atuais do vCenter e PSC em sua Nuvem Privada, vá para **Recursos** no [portal CloudSimple,](access-cloudsimple-portal.md)selecione sua Nuvem Privada e clique na guia **vSphere Management Network.**

![versões do vCenter e PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Estime o tamanho do seu ambiente de desktop

* Verifique se a configuração identificada está dentro dos limites operacionais do VMware.
* Estime os recursos necessários para todos os seus desktops e seus componentes de gerenciamento Horizon.

### <a name="create-a-private-cloud-for-your-environment"></a>Crie uma Nuvem Privada para o seu ambiente

1. Crie uma nuvem privada a partir do portal CloudSimple seguindo as instruções em [Configurar um ambiente de Nuvem Privada](quickstart-create-private-cloud.md).  O CloudSimple cria um usuário padrão do vCenter chamado 'cloudowner' em cada Nuvem Privada recém-criada. Para obter detalhes sobre o modelo padrão de permissão e usuário da Nuvem Privada, consulte [Aprenda o modelo de permissões da Nuvem Privada](learn-private-cloud-permissions.md).
2. Crie uma VLAN em sua Nuvem Privada para o plano de gerenciamento Horizon e atribua-lhe um CIDR de sub-rede. Para obter instruções, consulte [Criar e gerenciar VLANs/Subnets](create-vlan-subnet.md). Esta é a rede onde todos os componentes da solução (Gateway de acesso unificado, servidor de conexão, servidor de volume de aplicativo e servidores do Gerente de Ambiente do Usuário) serão instalados.
3. Decida se deseja usar um provedor de identidade externo com seu vCenter private cloud. Se sim, escolha uma dessas opções:
    * Use seu Diretório Ativo no local como provedor de identidade externo. Para obter instruções, consulte [as fontes de identidade do vCenter](set-vcenter-identity.md).
    * Configure um servidor active directory no plano de gerenciamento Private Cloud in Horizon vLAN para usar como seu provedor de identidade externa. Para obter instruções, consulte [as fontes de identidade do vCenter](set-vcenter-identity.md).
    * Configure um servidor DHCP e DNS no plano de gerenciamento Horizon VLAN na Nuvem Privada. Para obter instruções, [consulte Configurar aplicativos DNS e DHCP e cargas de trabalho em sua Nuvem Privada CloudSimple](dns-dhcp-setup.md).
4. Configure o encaminhamento de DNS no servidor DNS instalado na Nuvem Privada. Para obter instruções, consulte [Criar um encaminhador condicional](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Instale o VMware Horizon em sua nuvem privada

O diagrama de implantação a seguir mostra uma solução Horizon implantada em uma nuvem privada. O Gateway de acesso unificado, o AD/DC, o View e o App Volume Server estão instalados na VLAN 234 criada pelo usuário. O Unified Access Gateway tem um endereço IP público atribuído que pode ser acessado a partir da Internet. As VMs do pool de desktop Horizon são implantadas na VLAN 235 para fornecer isolamento e segurança adicionais.

![Implantação do Horizon na Nuvem Privada](media/horizon-private-cloud.png)

As seções a seguir descrevem as instruções para configurar uma implantação semelhante à que é representada na figura. Antes de começar, verifique se você tem o seguinte:

* Uma Nuvem Privada criada usando o portal CloudSimple com capacidade suficiente para executar seus pools de desktop.
* Largura de banda suficiente entre o ambiente local e o ambiente Private Cloud para suportar o tráfego de rede para seus desktops.
* Um túnel VPN site-to-site configurado entre o datacenter local e a Nuvem Privada.
* Capacidade de alcance IP de sub-redes de usuário final em seu ambiente local para as sub-redes Cloud Private Cloud do Cloud.
* AD/DHCP/DNS instalado para sua Nuvem Privada.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal CloudSimple: Crie uma VLAN/sub-rede dedicada para pools de desktop

Crie uma VLAN para os pools de desktop Horizon e atribua-a uma CIDR de sub-rede. Para obter instruções, consulte [Criar e gerenciar VLANs/Subnets](create-vlan-subnet.md). Esta é a rede onde todas as máquinas virtuais de desktop serão executadas.

Siga as práticas recomendadas de segurança padrão para garantir sua implantação no Horizon:

* Permita apenas tráfego RDP /SSH de desktop para suas VMs de desktop.
* Permita apenas o tráfego de gerenciamento entre o plano de gerenciamento Horizon VLAN e o desktop pool VLAN.
* Permitir apenas o tráfego de gerenciamento da rede local.

Você pode impor essas práticas recomendadas configurando regras de [firewall](firewall.md) a partir do portal CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal CloudSimple: Configure regras de firewall para proteger o plano de gerenciamento horizon

Configure as seguintes regras no portal CloudSimple. Para obter instruções, [consulte Configurar tabelas e regras de firewall](firewall.md).

1. Configure as regras de firewall no firewall CloudSimple N-S para permitir a comunicação entre sub-redes locais e o VLAN de gerenciamento do Horizon para que apenas as portas de rede listadas na [lista de portas Horizon](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) do documento VMware sejam permitidas.

2. Crie regras de firewall E-W entre a VLAN de gerenciamento horizon e a VLAN do desktop pool na Nuvem Privada.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal CloudSimple: Crie um endereço IP público para gateway de acesso unificado

Crie um endereço IP público para o aparelho Do Gateway de Acesso Unificado para permitir conexões de clientes de desktop a partir da internet. Para obter instruções, consulte [Alocar endereços IP públicos](public-ips.md).

Quando a configuração estiver concluída, o endereço IP público é atribuído e listado na página de IPs públicos.

#### <a name="cloudsimple-portal-escalate-privileges"></a>Portal CloudSimple: Escalar privilégios

O usuário padrão 'cloudowner' não tem privilégios suficientes no vCenter da Nuvem Privada para instalar o Horizon, de modo que os privilégios do vCenter do usuário devem ser aumentados. Para obter mais informações, consulte [Escalar privilégios](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>iu do vCenter: crie um usuário na instalação private cloud for Horizon

1. Faça login no vCenter usando as credenciais de usuário 'cloudowner'.
2. Crie um novo usuário, 'horizon-soln-admin', no vCenter e adicione o usuário ao grupo de administradores no vCenter.
3. Faça login no vCenter como usuário 'cloudowner' e faça login como o usuário 'horizon-soln-admin'.

#### <a name="vcenter-ui-install-vmware-horizon"></a>iU do vCenter: instale o VMware Horizon

Como mencionado na seção de arquitetura lógica anterior, a solução Horizon tem os seguintes componentes:

* VMware Horizon View
* Gateway de acesso unificado da VMware
* Gerenciador de volumes de aplicativos VMware
* Gerente de Ambiente do Usuário VMware

Instale os componentes da seguinte forma:

1. Instale e configure o Gateway de Acesso Unificado seguindo as instruções fornecidas no documento VMware [Implantando e Configurando o Gateway de Acesso Unificado vMware](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Instale a Vista do Horizonte na Nuvem Privada seguindo as instruções no [Guia de Instalação de Exibição](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Instale o App Volume Manager seguindo as instruções em [Instalar e Configurar volumes de aplicativos VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Instale e configure o User Environment Manager seguindo as instruções em [Sobre a instalação e configuração do Gerenciador de Ambientes do Usuário vMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Arquive uma solicitação de suporte para carregar volumes de aplicativos pré-embalados do VMware Horizon

Como parte do processo de instalação, o App Volume Manager usa volumes pré-embalados para provisionar pilhas de aplicativos e volumes graváveis. Esses volumes servem como modelos para pilhas de aplicativos e volumes graváveis.

O upload dos volumes para o armazenamento de dados Private Cloud requer a senha raiz ESXi. Para assistência, envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Anexe o pacote instalador do AppVolumes para que o pessoal de suporte do CloudSimple possa carregar os modelos para o ambiente private cloud.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portal CloudSimple: Desescalar privilégios

Agora você pode [desescalar os privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges) do usuário 'cloudowner'.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gestão contínua da sua solução Horizon

Você tem controle total sobre o software Horizon e App Volume Manager em seu ambiente Private Cloud e espera-se que execute o gerenciamento necessário do ciclo de vida do software. Certifique-se de que quaisquer novas versões de software sejam compatíveis com o Private Cloud vCenter e PSC antes de atualizar ou atualizar o Horizon ou o App Volume.
