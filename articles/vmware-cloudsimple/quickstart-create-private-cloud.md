---
title: Início rápido de soluções do Azure VMware (AVS)-criar uma nuvem privada de AVS
description: Saiba como criar e configurar uma nuvem privada de AVS com soluções VMware do Azure (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018560"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>Início rápido-configurar um ambiente de nuvem privada AVS

Neste artigo, saiba como criar uma nuvem privada de AVS e configurar seu ambiente de nuvem privada de AVS.

## <a name="before-you-begin"></a>Antes de começar

Examine os [pré-requisitos de rede](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-avs-private-cloud"></a>Criar uma nuvem privada de AVS

Uma nuvem privada de AVS é uma pilha VMware isolada que dá suporte a hosts ESXi, vCenter, vSAN e NSX.

As nuvens privadas da AVS são gerenciadas por meio do portal da AVS. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

1. Selecione **Todos os serviços**.
2. Procure **serviços de sincronização automática**.
3. Selecione o serviço AVS no qual você deseja criar sua nuvem privada de AVS.
4. Em **visão geral**, clique em **criar nuvem privada de AVS** para abrir uma nova guia do navegador para o portal da AVS. Se solicitado, entre com suas credenciais de entrada do Azure. 

    ![Criar uma nuvem privada de AVS do Azure](media/create-private-cloud-from-azure.png)

5. No portal da AVS, forneça um nome para sua nuvem privada de AVS.
6. Selecione o **local** da sua nuvem privada de AVS.
7. Selecione o **tipo de nó**, consistente com o que você provisionou no Azure.
8. Especifique a **contagem de nós**. Pelo menos três nós são necessários para criar uma nuvem privada de AVS.

    ![Criar uma nuvem privada de AVS-informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Um intervalo CIDR/24 dá suporte a até 26 nós, um intervalo CIDR/23 dá suporte a até 58 nós, e um intervalo CIDR/22 e/21 dá suporte a nós 64 (o número máximo de nós em uma nuvem privada de AVS). Para saber mais e VLANs e sub-redes, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada da AVS. Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Avançar: examinar e criar**.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

O processo de provisionamento da nuvem privada AVS é iniciado. Pode levar até duas horas para que a nuvem privada de AVS seja provisionada.

## <a name="launch-avs-portal"></a>Iniciar o portal de sincronização automática

Você pode acessar o portal da AVS da portal do Azure. O portal de sincronização automática será iniciado com suas credenciais de logon do Azure usando o SSO (logon único). O acesso ao portal da AVS exige que você autorize o aplicativo de **autorização do serviço AVS** . Para obter mais informações sobre como conceder permissões, consulte [consentir o aplicativo de autorização de serviço AVS](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application).

1. Selecione **Todos os serviços**.
2. Procure **serviços de sincronização automática**.
3. Selecione o serviço AVS no qual você deseja criar sua nuvem privada de AVS.
4. Em visão geral, clique em **ir para o portal de sincronização automática** para abrir uma nova guia do navegador para o portal de sincronização automática. Se solicitado, entre com suas credenciais de entrada do Azure. 

    ![Iniciar o portal de sincronização automática](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar VPN ponto a site

Uma conexão VPN ponto a site é a maneira mais simples de se conectar à sua nuvem privada de AVS do seu computador. Use a conexão VPN ponto a site se você estiver se conectando à nuvem privada da AVS remotamente. Para obter acesso rápido à sua nuvem privada de AVS, siga as etapas abaixo. O acesso à região da AVS da sua rede local pode ser feito usando a [VPN site a site](vpn-gateway.md) ou o [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Criar gateway

1. Inicie o portal da AVS e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN ponto a site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço de AVS é implantado.
    * Especifique a sub-rede do cliente para o gateway ponto a site. Os endereços DHCP serão fornecidos dessa sub-rede quando você se conectar.

5. Para **conexão/usuário**, especifique as seguintes configurações e clique em **Avançar**.

    * Para permitir que todos os usuários atuais e futuros acessem automaticamente a nuvem privada da AVS por meio desse gateway de ponto a site, selecione **adicionar todos os usuários automaticamente**. Quando você seleciona essa opção, todos os usuários na lista de usuários são selecionados automaticamente. Você pode substituir a opção automática desmarcando usuários individuais na lista.
    * Para selecionar somente usuários individuais, clique nas caixas de seleção na lista de usuários.

6. A seção VLANs/sub-redes permite especificar VLANs de gerenciamento e de usuário/sub-redes para o gateway e as conexões.

    * As opções **adicionar automaticamente** definem a política global para esse gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área de **seleção** .
    * Selecione **Adicionar VLANs de gerenciamento/sub-redes de nuvens privadas da AVS**.
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique em **Adicionar VLANs/sub-redes definidas pelo usuário**.
    * As configurações de **selecionar** substituem as configurações globais em **adicionar automaticamente**.

7. Clique em **Avançar** para revisar as configurações. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway de VPN.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Conectar-se à AVS usando VPN ponto a site

O cliente VPN é necessário para se conectar ao AVS do seu computador. Baixe o [openvpn client](https://openvpn.net/community-downloads/) para Windows ou [viscosity](https://www.sparklabs.com/viscosity/download/) para MacOS e os X.

1. Inicie o portal da AVS e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Na lista de gateways de VPN, clique no gateway de VPN ponto a site.
4. Selecione **Usuários**.
5. Clique em **baixar minha configuração de VPN**.

    ![Baixar configuração de VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração em seu cliente VPN.

    * Instruções para [importar a configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar a configuração no MacOS ou no os X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Conecte-se à AVS.

## <a name="create-a-vlan-for-your-workload-vms"></a>Criar uma VLAN para suas VMs de carga de trabalho

Depois de criar uma nuvem privada de AVS, crie uma VLAN na qual você implantará suas VMs de carga de trabalho/aplicativo.

1. No portal da AVS, selecione **rede**.
2. Clique em **VLAN/sub-redes**.
3. Clique em **criar VLAN/sub-rede**.

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet.png)

4. Selecione a **nuvem privada da AVS** para a nova VLAN/sub-rede.
5. Selecione uma ID de VLAN na lista. 
6. Insira um nome de sub-rede para identificar a sub-rede.
7. Especifique o intervalo e a máscara de CIDR da sub-rede. Esse intervalo não deve se sobrepor a nenhuma sub-rede existente.
8. Clique em **Enviar**.

    ![Criar detalhes de VLAN/sub-rede](media/create-new-vlan-subnet-details.png)

A VLAN/sub-rede será criada. Agora você pode usar essa ID de VLAN para criar um grupo de portas distribuídas em seu vCenter de nuvem privada da AVS.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Conectar seu ambiente a uma rede virtual do Azure

A AVS fornece um circuito de ExpressRoute para sua nuvem privada de AVS. Você pode conectar sua rede virtual no Azure ao circuito do ExpressRoute. Para obter detalhes completos sobre como configurar a conexão, siga as etapas em [conexão de rede virtual do Azure usando o ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Entrar no vCenter

Agora você pode entrar no vCenter para configurar máquinas virtuais e políticas.

1. Para acessar o vCenter, inicie no portal da AVS. Na Home Page, em **tarefas comuns**, clique em **Iniciar vSphere cliente**. Selecione a nuvem privada da AVS e clique em **Iniciar cliente do vSphere** na nuvem privada da AVS.

    ![Iniciar cliente do vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione seu cliente vSphere preferencial para acessar o vCenter e entrar com seu nome de usuário e senha. Os padrões são:
    * Nome de usuário: **CloudOwner@AVS.local**
    * Senha: **AVS123!**  

As telas do vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Alterar sua senha do vCenter

A AVS recomenda que você altere sua senha na primeira vez que entrar no vCenter. A senha que você definir deve atender aos seguintes requisitos:

* Tempo de vida máximo: a senha deve ser alterada a cada 365 dias
* Restringir reutilização: os usuários não podem reutilizar nenhuma das cinco senhas anteriores
* Comprimento: 8-20 caracteres
* Caractere especial: pelo menos um caractere especial
* Caracteres alfabéticos: pelo menos um caractere maiúsculo, A-Z e, pelo menos, um caractere minúsculo, a-z
* Números: pelo menos um caractere numérico, 0-9
* Máximo de caracteres adjacentes idênticos: três

    Exemplo: CC ou CCC é aceitável como parte da senha, mas CCCC não é.

Se você definir uma senha que não atenda aos requisitos:

* Se você usar o cliente vSphere flash, ele relatará um erro
* Se você usar o cliente HTML5, ele não relatará um erro. O cliente não aceita a alteração e a senha antiga continua a funcionar.

## <a name="access-nsx-manager"></a>Acessar o Gerenciador do NSX

O NSX Manager é implantado com uma senha padrão. 

* Nome de usuário: **administrador**
* Senha: **AVS123!**

Você pode encontrar o FQDN (nome de domínio totalmente qualificado) e o endereço IP do NSX Manager no portal da AVS.

1. Inicie o portal de AVS e selecione **recursos**.
2. Clique na nuvem privada da AVS, que você deseja usar.
3. Selecionar **rede de gerenciamento do vSphere**
4. Use o FQDN ou o endereço IP do **NSX Manager** e conecte-se usando um navegador da Web.

    ![Localizar FQDN do NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Criar um grupo de portas

Para criar um grupo de portas distribuídas no vSphere:

1. Siga as instruções em "adicionar um grupo de portas distribuídas" no [Guia de rede do vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo de portas distribuídas, forneça a ID de VLAN criada em [criar uma VLAN para suas VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Próximos passos

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar VPN site a site a partir do local](vpn-gateway.md)
