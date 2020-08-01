---
title: Instalar HCX (Extensão de Nuvem Híbrida)
description: Configurar a solução de HCX (Extensão de Nuvem Híbrida) do VMware para sua nuvem privada da AVS (Solução VMware do Azure)
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: ea968cb21812f7273af342763d307c2faba1eea6
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475440"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Instalar a HCX na Solução VMware do Azure

Neste artigo, percorreremos procedimentos para configurar a solução de HCX (extensão de nuvem híbrida) do VMWare para sua nuvem privada da AVS (solução VMWare) do Azure. O HCX permite a migração de suas cargas de trabalho do VMware para a nuvem e outros sites conectados por meio de vários tipos de migração com suporte HCX internos.

O HCX Advanced, a instalação padrão, dá suporte a até três vCenters. Se mais de três forem necessários, os clientes terão a opção de habilitar o complemento do HCX Enterprise por meio do suporte. A instalação do HCX Enterprise transporta cobranças adicionais para os clientes após a disponibilidade geral (GA), mas fornece [recursos adicionais](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


[Antes de começar](#before-you-begin), analise detalhadamente os [Requisitos de versão do software](#software-version-requirements) e os [Pré-requisitos](#prerequisites). 

Em seguida, vamos examinar todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implantar a HCX OVA no local
> * Ativar e configurar a HCX
> * Configurar o uplink de rede e a malha de serviço
> * Concluir a instalação verificando o status do dispositivo

Depois de concluir a instalação, você pode seguir as próximas etapas recomendadas fornecidas no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar
    
* Examine a [série de tutoriais](tutorial-network-checklist.md)do SDDC (Data Center) definido pelo software de AVS básico.
* Examine e referencie a [documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , incluindo o guia do usuário do HCX.
* Examine os documentos do VMware [migrando máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Opcionalmente, examine as [considerações de implantação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Opcionalmente, examine os materiais sobre o VMware relacionados à HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) sobre VMware vSphere na HCX. 
* Solicite a ativação da HCX Enterprise para AVS por meio dos canais de suporte da AVS.

Dimensionar cargas de trabalho nos recursos de armazenamento e computação é uma etapa de planejamento essencial da preparação para uso da solução de HCX de nuvem privada da AVS. Resolva a etapa de dimensionamento como parte do planejamento inicial do ambiente de nuvem privada.   

## <a name="software-version-requirements"></a>Requisitos de versão do software
Os componentes de infraestrutura devem estar executando a versão mínima necessária. 
                                                         
| Tipo de componente    | Requisitos de ambiente de origem    | Requisitos de ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se estiver usando o 5,5 U1 ou anterior, use a interface do usuário HCX autônoma para operações HCX.  | 6.0 U2 e posterior   |
| ESXi   | 5.0    | ESXi 6.0 e posterior   |
| NSX    | Para a extensão de rede HCX de comutadores lógicos na origem: NSXv 6.2 + ou NSX-T 2.4 +   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para roteamento de proximidade de HCX: NSXv 6.4 + (o roteamento de proximidade não tem suporte com o NSX-T) |
| vCloud Director   | Não obrigatório – sem interoperabilidade com o vCloud Director no site de origem | Ao integrar o ambiente de destino com o vCloud Director, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

* O ExpressRoute Alcance Global deve ser configurado entre os circuitos do ExpressRoute do SDDC local e AVS.

* Todas as portas necessárias devem ser abertas entre o local e o SDDC da AVS (confira a [documentação sobre a HCX do VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Um endereço IP para o Gerenciador de HCX no local e um mínimo de dois endereços IP para interconexão (IX) e dispositivo de extensão de rede (NE).

* Os dispositivos HCX IX e NE locais devem ser capazes de alcançar a infraestrutura do vCenter e ESXi.

* Para implantar o dispositivo WAN Interconnect, além do bloco de endereço de rede CIDR /22 usado para implantação do SDDC no portal do Azure, a HCX requer um bloco /29. Certifique-se de considerar esse requisito em seu planejamento de rede.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implantar o VMware HCX OVA no local

1. Entre no SDDC do AVS vCenter e selecione **HCX**.

    ![Selecione HCX no AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Em **Administração**, selecione **atualizações do sistema** e, em seguida, selecione **solicitar download link** para baixar o arquivo VMware HCX ova.

    ![Obter Atualizações do Sistema](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Em seguida, vá para o vCenter local e selecione um modelo de OVF para implantar em seu vCenter local.  

    ![Selecionar modelo de OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selecione um nome e um local e escolha um recurso/cluster no qual a HCX será implantada. Em seguida, examine os detalhes e os recursos necessários.  

    ![Examinar detalhes do modelo](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Examine os termos de licença e, se concordar, selecione o armazenamento e a rede necessários. Em seguida, selecione **Avançar**.

1. Em **Personalizar modelo**, insira todas as informações necessárias. 

    ![Personalizar modelo](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selecione **Avançar**, confirme a configuração e selecione **Concluir** para implantar a HCX OVA.

## <a name="activate-hcx"></a>Ativar HCX

Após a instalação, execute as etapas a seguir.

1. Faça logon no Gerenciador de HCX local em `https://HCXManagerIP:9443` e entre com seu nome de usuário e sua senha. 

   > [!IMPORTANT]
   > Certifique-se de incluir o `9443` número da porta com o endereço IP do Gerenciador de HCX.

1. Em **Licenciamento**, insira sua **Chave avançada de HCX**.  

    ![Inserir chave HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > O Gerenciador de HCX deve ter acesso aberto à Internet ou um proxy configurado.

1. No **vCenter**, se necessário, edite as informações do vCenter.

    ![Configurar o vCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Em **Localização do Datacenter**, edite o local do datacenter, se necessário.

    ![Localização do banco de dados](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Configurar HCX 

1. Entre no vCenter local e, em **Home**, selecione **HCX**.

    ![HCX no VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Em **infraestrutura**, selecione **emparelhamento**  >  **de site adicionar um emparelhamento de site**.

    ![Adicionar emparelhamento de site](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Insira a URL ou o endereço IP do HCX remoto, AVS cloudadmin nome de usuário e senha e, em seguida, selecione **conectar**.

   O sistema mostra o site conectado.
   
    ![Conexão do site](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Em **infraestrutura**, selecione **Interconnect**  >  **serviço vários sites malha**  >  **perfis de rede**  >  **Criar perfil de rede**.

    ![Criar perfil de rede](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Para o novo perfil de rede, insira os intervalos de endereços IP HCX IX e NE (um mínimo de dois endereços IP é necessário para o IX e o NE appliances).
    
   ![Inserir intervalo de endereços IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > O dispositivo de extensão de rede (HCX-NE) tem uma relação um-para-um com um DVS (comutador virtual distribuído).  

1. Agora, selecione **Perfil de computação** > **Criar perfil de computação**.

1. Insira um nome de perfil de computação e selecione **Continuar**.  

    ![Criar perfil de computação](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selecione os serviços a serem habilitados, como migração, extensão de rede ou recuperação de desastre e, em seguida, selecione **continuar**.

    ![Selecionar serviços](./media/hybrid-cloud-extension-installation/select-services.png)

1. Em **Selecionar Recursos de Serviço**, escolha um ou mais recursos de serviço para os quais os serviços de HCXs selecionados devem ser habilitados. Selecione **Continuar**.
    
   ![Selecionar recursos de serviço](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Selecione clusters específicos para os quais as VMs de origem serão destinadas na migração usando HCX.

1. Selecione **Datastore** e **Continuar**. 
      
    Selecione cada recurso de armazenamento e computação para implantar os dispositivos de interconexão HCX. Quando vários recursos são selecionados, a HCX usa o primeiro recurso selecionado até que sua capacidade seja esgotada.  
    
    ![Selecionar recursos de implantação](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Selecione o perfil de rede de gerenciamento criado em **Perfis de Rede** e clique em **Continuar**.  
      
    Selecione o perfil de rede por meio do qual a interface de gerenciamento do vCenter e os hosts ESXi podem ser alcançados. Caso ainda não tenha definido um perfil de rede, crie-o aqui.  
    
    ![Selecionar perfil de rede de gerenciamento](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Selecione **Uplink de rede** e clique em **Continuar**.
      
    Selecione um ou mais perfis de rede para que uma das seguintes opções seja verdadeira:  
    * Os dispositivos de interconexão no site remoto possam ser acessados por esta rede  
    * Os dispositivos remotos possam acessar os dispositivos de interconexão locais por meio dessa rede.  
    
    Se você tem redes ponto a ponto, como Conexão Direta, não compartilhadas em vários sites, ignore esta etapa, pois os perfis de computação são compartilhados com vários sites. Nesses casos, os Perfis de rede de uplink podem ser substituídos e especificados durante a criação da malha do serviço de interconexão.  
    
    ![Selecionar Perfil de rede de uplink](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Selecione **Perfil de Rede do vMotion** e clique em **Continuar**.
      
   Selecione o perfil de rede por meio do qual a interface vMotion dos hosts ESXi possa ser alcançada. Caso ainda não tenha definido um perfil de rede, crie-o aqui. Se você não tem o vMotion Network, selecione **Perfil da Rede de Gerenciamento**.  
    
   ![Selecionar Perfil de Rede do vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Em **selecionar perfil de rede de replicação do vSphere**, selecione um perfil de rede na interface de replicação do vSphere dos hosts ESXi e selecione **continuar**.
      
   Na maioria dos casos, esse perfil é o mesmo que o perfil de rede de gerenciamento.  
    
   ![Selecionar Perfil de Rede de Replicação do vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Em **selecionar comutadores distribuídos para extensões de rede**, selecione o DVS no qual você tem redes as VMs que serão integradas e conectadas.  Selecione **Continuar**.  
      
    ![Selecionar Comutadores Virtuais Distribuídos](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Examine as regras de conexão e selecione **continuar**.  

    ![Criar perfil de computação](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Selecione **Concluir** para criar o perfil de computação.

## <a name="configure-network-uplink"></a>Configurar Uplink de Rede

Agora, configure a alteração do perfil de rede em no SDDC da AVS para o Uplink de Rede.

1. Entre no SDDC NSX-T para criar um novo comutador lógico ou use um comutador lógico existente que possa ser usado para o uplink de rede entre o SDDC local e o AVS de sincronização.

1. Crie um perfil de rede para o uplink HCX no AVS SDDC que pode ser usado para comunicação do SDDC de local para AVS.  
    
   ![Criar perfil de rede para uplink](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Insira um nome para o perfil de rede e, no mínimo, quatro a cinco endereços IP livres com base na extensão de rede L2 necessária.  
    
   ![Configurar o perfil de rede para o uplink](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Selecione **Criar** para concluir a configuração do SDDC da AVS

## <a name="configure-service-mesh"></a>Configurar Malha de Serviço

Agora, configure a Malha de Serviço entre local e o SDDC da AVS.

1. Entre no SDDC do AVS vCenter e selecione **HCX**.

2. Em **infraestrutura**, selecione **interconectar**  >  **malha de serviço**  >  **criar malha de serviço** para configurar a rede e os perfis de computação criados nas etapas anteriores.    
      
    ![Configurar malha de serviço](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Selecione sites emparelhados para habilitar a capacidade híbrida e selecione **continuar**.   
    
    ![Selecionar sites emparelhados](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Selecione os perfis de computação de origem e remoto para habilitar os serviços de híbridoção e selecione **continuar**.
      
    As seleções definem os recursos, em que as VMs podem consumir serviços HCXs.  
      
    ![Habilitar serviços de hibridismo](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Selecione os serviços a serem habilitados e selecione **continuar**.  
      
    ![Selecionar serviços HCXs](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. Em **Configuração Avançada – Substituir Perfis de rede de Uplink** clique em **Continuar**.  
      
    Os perfis de rede de uplink são usados para se conectar à rede por meio da qual os dispositivos de interconexão do site remoto podem ser alcançados.  
      
    ![Substituir perfis de uplink](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Selecione **Configurar o dispositivo de extensão de rede scale out**. 
      
    ![Escalabilidade horizontal da extensão de rede](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Insira a contagem de dispositivo correspondente à contagem de comutador do DVS.  
      
    ![Configurar contagem de dispositivos](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Selecione **continuar** para ignorar.  
      
    ![Configurar engenharia de tráfego](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Examine a versão prévia da topologia e selecione **Continuar**. 

11. Insira um nome amigável para esta malha de serviço e selecione **concluir** para concluir.  
      
    ![Concluir malha de serviço](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    A malha de serviço foi implantada e configurada.  
      
    ![Malha de serviço implantada](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Verificar status do dispositivo
Para verificar o status do dispositivo, selecione **Interconexão** > **Dispositivos**. 
      
![Status do dispositivo](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Próximas etapas

Quando o **Status de Túnel** da interconexão do dispositivo estiver em verde e indicando **UP**, você estará pronto para migrar e proteger as VMs AVS usando a HCX. Confira a [Documentação da HCX do VMware](https://docs.vmware.com/en/VMware-HCX/index.html) e [Migrar máquinas virtuais com a HCX do VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica do VMware.
