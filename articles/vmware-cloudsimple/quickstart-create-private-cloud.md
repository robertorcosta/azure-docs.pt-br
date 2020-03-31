---
title: 'Quickstart: Crie uma nuvem privada'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Saiba como criar e configurar uma Nuvem Privada com soluções Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7460490dbd45862f4269d25e3910373700ec9a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564713"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Quickstart - Configure um ambiente de nuvem privada

Neste artigo, aprenda a criar uma Nuvem Privada Cloud e configure seu ambiente Private Cloud.

## <a name="before-you-begin"></a>Antes de começar

Revisar [pré-requisitos de rede](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma nuvem privada

Um Private Cloud é uma pilha de VMware isolada que suporta hosts ESXi, vCenter, vSAN e NSX.

As nuvens privadas são gerenciadas através do portal CloudSimple. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e em cima de um hardware de metal nu isolado.

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual deseja criar sua Nuvem Privada.
4. Em **Visão Geral,** clique **em Criar nuvem privada** para abrir uma nova guia do navegador para o portal CloudSimple.  Se solicitado, faça login com suas credenciais do Azure.  

    ![Criar nuvem privada do Azure](media/create-private-cloud-from-azure.png)

5. No portal CloudSimple, forneça um nome para sua Nuvem Privada.
6. Selecione a **localização** da sua nuvem privada.
7. Selecione **tipo Deó,** consistente com o que você provisionou no Azure.
8. Especificar **contagem de nó**.  Pelo menos três nós são necessários para criar uma Nuvem Privada.

    ![Criar Nuvem Privada - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique **em Next: Opções avançadas**.
10. Digite a faixa CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma de suas sub-redes no local ou outras sub-redes Azure (redes virtuais) ou com a sub-rede gateway.

    **Opções de faixa CIDR:** /24, /23, /22 ou /21. Uma faixa CIDR de 24 suporta até 26 nódulos, uma faixa de /23 CIDR suporta até 58 nódulos e uma faixa de /22 e /21 CIDR suporta 64 nodes (o número máximo de nódulos em uma Nuvem Privada).  Para saber mais e VLANs e sub-redes, consulte [VLANs e sub-redes de visão geral](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Os endereços IP na gama vSphere/vSAN CIDR são reservados para uso pela infra-estrutura Private Cloud.  Não use o endereço IP neste intervalo em nenhuma máquina virtual.

11. Clique **em Next: Revise e crie**.
12. Revise as configurações. Se você precisar alterar qualquer configuração, clique **em Anterior**.
13. Clique em **Criar**.

O processo de provisionamento privado em nuvem começa.  Pode levar até duas horas para que a Nuvem Privada seja provisionada.

## <a name="launch-cloudsimple-portal"></a>Inicie o portal CloudSimple

Você pode acessar o portal CloudSimple do portal Azure.  O portal CloudSimple será lançado com seu signo Azure em credenciais usando o SSO (Single Sign-On).  Acessar o portal CloudSimple requer que você autorize o aplicativo **CloudSimple Service Authorization.**  Para obter mais informações sobre a concessão de permissões, consulte [Consent to CloudSimple Service Authorization application](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual deseja criar sua Nuvem Privada.
4. Na visão geral, clique **em Ir ao portal CloudSimple** para abrir uma nova guia do navegador para o portal CloudSimple.  Se solicitado, faça login com suas credenciais do Azure.  

    ![Inicie o portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar VPN ponto a ponto

Uma conexão VPN ponto a ponto é a maneira mais simples de se conectar à sua Nuvem Privada a partir do seu computador. Use a conexão VPN ponto a ponto se estiver se conectando à Nuvem Privada remotamente.  Para acesso rápido à sua Nuvem Privada, siga os passos abaixo.  O acesso à região do CloudSimple a partir de sua rede local pode ser feito usando [vpn site-to-site](vpn-gateway.md) ou [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Criar gateway

1. Inicie o portal CloudSimple e selecione **Network**.
2. Selecione **vpn Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração do Gateway,** especifique as configurações a seguir e clique **em "Avançar "Next**"

    * Selecione **vpn ponto a ponto como** o tipo de gateway.
    * Digite um nome para identificar o gateway.
    * Selecione o local do Azure onde o serviço CloudSimple é implantado.
    * Especifique a sub-rede do cliente para o gateway Point-to-Site.  Os endereços DHCP serão fornecidos a partir desta sub-rede quando você se conectar.

5. Para **Conexão/Usuário,** especifique as seguintes configurações e clique **em "A seguir".**

    * Para permitir automaticamente que todos os usuários atuais e futuros acessem a Nuvem Privada através deste gateway Point-to-Site, selecione **Adicionar automaticamente todos os usuários**. Quando você seleciona essa opção, todos os usuários da lista Usuário são selecionados automaticamente. Você pode substituir a opção automática desselecionando usuários individuais na lista.
    * Para selecionar apenas usuários individuais, clique nas caixas de seleção na lista usuário.

6. A seção VLANs/Subnets permite especificar VLANs/sub-redes de gerenciamento e usuário para o gateway e conexões.

    * O **Adicionar automaticamente** opções define a política global para este gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área **Select.**
    * Selecione **Adicionar VLANs/Sub-redes de gerenciamento de nuvens privadas**.
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique **em Adicionar VLANs/Subnets definidas pelo usuário**.
    * As **configurações Select** anulam as configurações globais em **Adicionar automaticamente**.

7. Clique **em Next** para revisar as configurações. Clique nos ícones Editar para fazer qualquer alteração.
8. Clique **em Criar** para criar o gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Conecte-se ao CloudSimple usando vpn ponto a ponto

O cliente VPN é necessário para se conectar ao CloudSimple a partir do seu computador.  Baixe [o cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Inicie o portal CloudSimple e selecione **Network**.
2. Selecione **vpn Gateway**.
3. Na lista de gateways VPN, clique no gateway VPN point-to-site.
4. Selecionar **usuários**.
5. Clique **em Baixar minha configuração VPN**.

    ![Baixar configuração de VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração em seu cliente VPN.

    * Instruções para [importar configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importação de configuração no macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Conecte-se ao CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Crie uma VLAN para suas VMs de carga de trabalho

Depois de criar uma Nuvem Privada, crie uma VLAN onde você implantará suas VMs de carga de trabalho/aplicativo.

1. No portal CloudSimple, selecione **Rede**.
2. Clique **em VLAN/Subnets**.
3. Clique **em Criar VLAN/Sub-net**.

    ![Criar VLAN/Subneta](media/create-new-vlan-subnet.png)

4. Selecione a **Nuvem Privada** para a nova VLAN/sub-rede.
5. Selecione um ID VLAN na lista.  
6. Digite um nome de sub-rede para identificar a sub-rede.
7. Especifique o intervalo e a máscara CIDR da sub-rede.  Este intervalo não deve se sobrepor a quaisquer sub-redes existentes.
8. Clique em **Enviar**.

    ![Criar detalhes da VLAN/Subnet](media/create-new-vlan-subnet-details.png)

A VLAN/sub-rede será criada.  Agora você pode usar este ID VLAN para criar um grupo de porta distribuída no seu vCenter private cloud.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Conecte seu ambiente a uma rede virtual do Azure

O CloudSimple fornece um circuito ExpressRoute para sua Nuvem Privada. Você pode conectar sua rede virtual no Azure ao circuito ExpressRoute. Para obter detalhes completos sobre a configuração da conexão, siga as etapas do [Azure Virtual Network Connection usando o ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Faça login no vCenter

Agora você pode fazer login no vCenter para configurar máquinas e políticas virtuais.

1. Para acessar o vCenter, comece pelo portal CloudSimple. Na página inicial, em **Tarefas Comuns,** clique em **Iniciar vSphere Client**.  Selecione a Nuvem Privada e clique em **Iniciar o cliente vSphere** na Nuvem Privada.

    ![Inicie o cliente vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione seu cliente vSphere preferido para acessar o vCenter e faça login com seu nome de usuário e senha.  Os padrões são:
    * Nome do usuário: **CloudOwner\@cloudsimple.local**
    * Senha: **CloudSimple123!**  

As telas vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Altere sua senha do vCenter

O CloudSimple recomenda que você altere sua senha na primeira vez que fizer login no vCenter.  
A senha definida deve atender aos seguintes requisitos:

* Vida útil máxima: A senha deve ser alterada a cada 365 dias
* Restringir a reutilização: os usuários não podem reutilizar nenhuma das cinco senhas anteriores
* Comprimento: 8 - 20 caracteres
* Personagem especial: Pelo menos um personagem especial
* Caracteres alfabéticos: Pelo menos um caractere maiúsculo, A-Z, e pelo menos um caractere minúsculo, a-z
* Números: Pelo menos um caractere numérico, 0-9
* Caracteres adjacentes idênticos máximos: Três

    Exemplo: CC ou CCC é aceitável como parte da senha, mas cccc não é.

Se você definir uma senha que não atenda aos requisitos:

* se você usar o vSphere Flash Client, ele relata um erro
* Se você usar o cliente HTML5, ele não reportará um erro. O cliente não aceita a mudança e a senha antiga continua funcionando.

## <a name="access-nsx-manager"></a>Acesse o gerenciador NSX

O gerenciador NSX é implantado com uma senha padrão. 

* Nome do usuário: **admin**
* Senha: **CloudSimple123!**

Você pode encontrar o nome de domínio totalmente qualificado (FQDN) e o endereço IP do gerenciador NSX no portal CloudSimple.

1. Inicie o portal CloudSimple e selecione **Recursos**.
2. Clique na Nuvem Privada, que você deseja usar.
3. Selecione **a rede de gerenciamento vSphere**
4. Use o endereço FQDN ou IP do **NSX Manager** e conecte-se usando um navegador da Web.

    ![Encontre o NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Criar um grupo portuário

Para criar um grupo de portas distribuídas no vSphere:

1. Siga as instruções em "Adicionar um grupo de porta distribuída" no [guia de rede vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo de portas distribuídas, forneça o ID VLAN criado em [Criar uma VLAN para suas VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conecte-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar vpn site-to-site a partir de locais](vpn-gateway.md)
