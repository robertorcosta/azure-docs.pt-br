---
title: Instalar o VMware HCX
description: Configurar a solução VMware HCX para sua nuvem privada da solução Azure VMware
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: 76a7432b78ec2141039dcdc5dd1d7572335b18e1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263193"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Instalar a HCX na Solução VMware do Azure

Neste artigo, percorreremos os procedimentos para configurar a solução VMWare HCX para sua nuvem privada da solução Azure VMWare. O HCX permite a migração de suas cargas de trabalho do VMware para a nuvem e outros sites conectados por meio de vários tipos de migração com suporte HCX internos.

O HCX Advanced, a instalação padrão, oferece suporte a até três conexões de site (local ou nuvem para nuvem). Se mais de três conexões de site forem necessárias, os clientes terão a opção de habilitar o complemento do HCX Enterprise por meio do suporte, que está atualmente em versão prévia. A HCX Enterprise incorre em cobranças adicionais para os clientes após a GA (disponibilidade geral), mas fornece [recursos adicionais](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


[Antes de começar](#before-you-begin), analise detalhadamente os [Requisitos de versão do software](#software-version-requirements) e os [Pré-requisitos](#prerequisites). 

Em seguida, vamos examinar todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implantar o HCX OVA (conector) local
> * Ativar e configurar a HCX
> * Configurar o uplink de rede e a malha de serviço
> * Concluir a instalação verificando o status do dispositivo

Depois de concluir a instalação, você pode seguir as próximas etapas recomendadas fornecidas no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar
    
* Examine a [série de tutoriais](tutorial-network-checklist.md)básicos do programa de datacenter (SDDC) da solução VMware do Azure.
* Examine e referencie a [documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , incluindo o guia do usuário do HCX.
* Examine os documentos do VMware [migrando máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Opcionalmente, examine as [considerações de implantação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Opcionalmente, examine os materiais sobre o VMware relacionados à HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) sobre VMware vSphere na HCX. 
* Solicite uma solução do Azure VMware HCX ativação corporativa por meio de canais de suporte da solução Azure VMware.

O dimensionamento de cargas de trabalho em relação aos recursos de computação e armazenamento é uma etapa de planejamento essencial quando você está se preparando para usar a solução de nuvem privada HCX da solução Azure VMware. Resolva a etapa de dimensionamento como parte do planejamento inicial do ambiente de nuvem privada. 

Você também pode dimensionar cargas de trabalho concluindo uma [avaliação de solução do Azure VMware](https://docs.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) no portal de migrações para Azure.

## <a name="software-version-requirements"></a>Requisitos de versão do software

Os componentes de infraestrutura devem estar executando a versão mínima necessária. 
                                                         
| Tipo de componente    | Requisitos de ambiente de origem    | Requisitos de ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se estiver usando o 5,5 U1 ou anterior, use a interface do usuário HCX autônoma para operações HCX.  | 6.0 U2 e posterior   |
| ESXi   | 5.0    | ESXi 6.0 e posterior   |
| NSX    | Para a extensão de rede HCX de comutadores lógicos na origem: NSXv 6.2 + ou NSX-T 2.4 +   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para roteamento de proximidade de HCX: NSXv 6.4 + (o roteamento de proximidade não tem suporte com o NSX-T) |
| vCloud Director   | Não obrigatório – sem interoperabilidade com o vCloud Director no site de origem | Ao integrar o ambiente de destino com o vCloud Director, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

* O ExpressRoute Alcance Global deve ser configurado entre os circuitos do ExpressRoute do Azure no local e na solução VMware.

* Todas as portas necessárias devem ser abertas entre o local e a solução do Azure VMware SDDC (consulte a [documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Um endereço IP para o Gerenciador de HCX no local e um mínimo de dois endereços IP para interconexão (IX) e dispositivo de extensão de rede (NE).

* Os dispositivos HCX IX e NE locais devem ser capazes de alcançar a infraestrutura do vCenter e ESXi.

* Para implantar o dispositivo WAN Interconnect, além do bloco de endereço de rede CIDR /22 usado para implantação do SDDC no portal do Azure, a HCX requer um bloco /29. Certifique-se de considerar esse requisito em seu planejamento de rede.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implantar o VMware HCX OVA no local

1. Entre no Azure VMware Solution HCX Manager na `https://x.x.x.9` porta 443 com as credenciais de usuário do **cloudadmin** e, em seguida, vá para **suporte**.

1. Selecione o link de download para o arquivo VMware HCX OVA. 

1. Entre na solução de VMware do Azure SDDC vCenter e selecione **HCX**.
   
1. Vá para o vCenter local e selecione um modelo de OVF para implantar em seu vCenter local.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Em seguida, vá para o vCenter local e selecione um modelo de OVF para implantar em seu vCenter local.":::

1. Selecione um nome e um local e escolha um recurso/cluster no qual a HCX será implantada. Em seguida, examine os detalhes e os recursos necessários.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Selecione um nome e um local e, em seguida, selecione um recurso/cluster em que HCX precisa ser implantado. Em seguida, examine os detalhes e os recursos necessários.":::

1. Examine os termos de licença e, se concordar, selecione o armazenamento e a rede necessários. Em seguida, selecione **Avançar**.

1. Em **Personalizar modelo**, insira todas as informações necessárias. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="Em Personalizar modelo, insira todas as informações necessárias.":::

1. Selecione **Avançar**, confirme a configuração e selecione **Concluir** para implantar a HCX OVA.

## <a name="activate-hcx"></a>Ativar HCX

Após a instalação, execute as etapas a seguir.

1. Faça logon no Gerenciador de HCX local em `https://HCXManagerIP:9443` e entre com as credenciais de nome de usuário do **administrador** . 

   > [!IMPORTANT]
   > Certifique-se de incluir o `9443` número da porta com o endereço IP do Gerenciador de HCX.

1. Em **Licenciamento**, insira sua **Chave avançada de HCX**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="Em licenciamento, insira sua chave avançada do HCX.":::
    
    > [!NOTE]
    > O Gerenciador de HCX deve ter acesso aberto à Internet ou um proxy configurado.

1. No **vCenter**, se necessário, edite as informações do vCenter.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="No vCenter, se necessário, edite as informações do vCenter.":::

1. Em **Localização do Datacenter**, edite o local do datacenter, se necessário.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="Em localização do datacenter, se necessário, edite o local do datacenter.":::

## <a name="configure-hcx"></a>Configurar HCX 

1. Entre no vCenter local e, em **Home**, selecione **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Entre no vCenter local e, em home, selecione HCX.":::

1. Em **infraestrutura**, selecione **emparelhamento**  >  **de site adicionar um emparelhamento de site**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="Em infraestrutura, selecione emparelhamento de site > adicionar um emparelhamento de site.":::

1. Insira a URL ou o endereço IP do HCX remoto, Azure VMware Solution cloudadmin nome de usuário e senha e, em seguida, selecione **conectar**.

   O sistema mostra o site conectado.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="O sistema mostra o site conectado.":::

1. Em **infraestrutura**, selecione **Interconnect**  >  **serviço vários sites malha**  >  **perfis de rede**  >  **Criar perfil de rede**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="Em infraestrutura, selecione Interconnect > malha de serviço multissite > perfis de rede > criar perfil de rede.":::

1. Para o novo perfil de rede, insira os intervalos de endereços IP HCX IX e NE (um mínimo de dois endereços IP é necessário para o IX e o NE appliances).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Para o novo perfil de rede, insira os intervalos de endereços IP HCX IX e NE (um mínimo de dois endereços IP é necessário para o IX e o NE appliances).":::
  
   > [!NOTE]
   > O dispositivo de extensão de rede (HCX-NE) tem uma relação um-para-um com um DVS (comutador virtual distribuído).  

1. Agora, selecione **Perfil de computação** > **Criar perfil de computação**.

1. Insira um nome de perfil de computação e selecione **Continuar**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Insira um nome de perfil de computação e selecione continuar.":::

1. Selecione os serviços a serem habilitados, como migração, extensão de rede ou recuperação de desastre e, em seguida, selecione **continuar**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Selecione os serviços a serem habilitados, como migração, extensão de rede ou recuperação de desastre e, em seguida, selecione continuar.":::

1. Em **Selecionar Recursos de Serviço**, escolha um ou mais recursos de serviço para os quais os serviços de HCXs selecionados devem ser habilitados. Selecione **Continuar**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="Em selecionar recursos de serviço, selecione um ou mais recursos de serviço para os quais os serviços HCXs selecionados devem ser habilitados. Selecione continuar.":::
  
   > [!NOTE]
   > Selecione clusters específicos para os quais as VMs de origem serão destinadas na migração usando HCX.

1. Selecione **Datastore** e **Continuar**. 
      
   Selecione cada recurso de armazenamento e computação para implantar os dispositivos de interconexão HCX. Quando vários recursos são selecionados, a HCX usa o primeiro recurso selecionado até que sua capacidade seja esgotada.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Selecione cada recurso de computação e armazenamento para implantar os dispositivos de interconexão HCX. Quando vários recursos são selecionados, o HCX usa o primeiro recurso selecionado até que sua capacidade seja esgotada.":::

1. Selecione o perfil de rede de gerenciamento criado em **Perfis de Rede** e clique em **Continuar**.  
      
   Selecione o perfil de rede por meio do qual a interface de gerenciamento do vCenter e os hosts ESXi podem ser alcançados. Caso ainda não tenha definido um perfil de rede, crie-o aqui.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Selecione o perfil de rede por meio do qual a interface de gerenciamento do vCenter e os hosts ESXi podem ser alcançados. Se você ainda não definiu um perfil de rede, você pode criá-lo aqui.":::

1. Selecione **Uplink de rede** e clique em **Continuar**.
      
   Selecione um ou mais perfis de rede para que uma das seguintes opções seja verdadeira:  
   * Os dispositivos de interconexão no site remoto possam ser acessados por esta rede  
   * Os dispositivos remotos possam acessar os dispositivos de interconexão locais por meio dessa rede.  
    
   Se você tem redes ponto a ponto, como Conexão Direta, não compartilhadas em vários sites, ignore esta etapa, pois os perfis de computação são compartilhados com vários sites. Nesses casos, os Perfis de rede de uplink podem ser substituídos e especificados durante a criação da malha do serviço de interconexão.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Selecione uplink de rede e selecione continuar.":::

1. Selecione **Perfil de Rede do vMotion** e clique em **Continuar**.
      
   Selecione o perfil de rede por meio do qual a interface vMotion dos hosts ESXi possa ser alcançada. Caso ainda não tenha definido um perfil de rede, crie-o aqui. Se você não tem o vMotion Network, selecione **Perfil da Rede de Gerenciamento**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Selecione perfil de rede do vMotion e selecione continuar.":::

1. Em **selecionar perfil de rede de replicação do vSphere**, selecione um perfil de rede na interface de replicação do vSphere dos hosts ESXi e selecione **continuar**.
      
   Na maioria dos casos, esse perfil é o mesmo que o perfil de rede de gerenciamento.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="Em selecionar perfil de rede de replicação do vSphere, selecione um perfil de rede na interface de replicação do vSphere dos hosts ESXi e selecione continuar.":::

1. Em **selecionar comutadores distribuídos para extensões de rede**, selecione o DVS no qual você tem redes as VMs que serão integradas e conectadas.  Selecione **Continuar**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="Em selecionar comutadores distribuídos para extensões de rede, selecione o DVS no qual você tem redes as VMs que serão integradas e conectadas.  Selecione continuar.":::

1. Examine as regras de conexão e selecione **continuar**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Examine as regras de conexão e selecione continuar.":::

1.  Selecione **Concluir** para criar o perfil de computação.

## <a name="configure-network-uplink"></a>Configurar Uplink de Rede

Agora, configure a alteração do perfil de rede na solução do Azure VMware SDDC para uplink de rede.

1. Entre no SDDC NSX-T para criar um novo comutador lógico ou use um comutador lógico existente que possa ser usado para o uplink de rede entre o local e a solução SDDC do Azure VMware.

1. Crie um perfil de rede para o uplink HCX no Azure VMware Solution SDDC que pode ser usado para a comunicação do Azure da solução de VMware no local para a Microsoft.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Crie um perfil de rede para o uplink HCX no Azure VMware Solution SDDC que pode ser usado para a comunicação do Azure da solução de VMware no local para a Microsoft.":::

1. Insira um nome para o perfil de rede e, no mínimo, quatro a cinco endereços IP livres com base na extensão de rede L2 necessária.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Insira um nome para o perfil de rede e, no mínimo, quatro a cinco endereços IP livres com base na extensão de rede L2 necessária.":::

1. Selecione **criar** para concluir a configuração de SDDC da solução do Azure VMware

## <a name="configure-service-mesh"></a>Configurar Malha de Serviço

Agora, configure a malha de serviço entre o local e a solução SDDC do Azure VMware.

1. Entre na solução de VMware do Azure SDDC vCenter e selecione **HCX**.

2. Em **infraestrutura**, selecione **interconectar**  >  **malha de serviço**  >  **criar malha de serviço** para configurar a rede e os perfis de computação criados nas etapas anteriores.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="Em infraestrutura, selecione Interconnect > malha de serviço > criar malha de serviço para configurar a rede e os perfis de computação criados nas etapas anteriores.":::

3. Selecione sites emparelhados para habilitar a capacidade híbrida e selecione **continuar**.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Selecione sites emparelhados para habilitar a capacidade híbrida e selecione continuar.":::

4. Selecione os perfis de computação de origem e remoto para habilitar os serviços de híbridoção e selecione **continuar**.
      
   As seleções definem os recursos, em que as VMs podem consumir serviços HCXs.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="As seleções definem os recursos, em que as VMs podem consumir serviços HCXs.":::

5. Selecione os serviços a serem habilitados e selecione **continuar**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Selecione os serviços a serem habilitados e selecione continuar.":::

6. Em **Configuração Avançada – Substituir Perfis de rede de Uplink** clique em **Continuar**.  
      
   Os perfis de rede de uplink são usados para se conectar à rede por meio da qual os dispositivos de interconexão do site remoto podem ser alcançados.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="Os perfis de rede de uplink são usados para se conectar à rede por meio da qual os dispositivos de interconexão do site remoto podem ser alcançados.":::

7. Selecione **Configurar o dispositivo de extensão de rede scale out**. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Selecione configurar o dispositivo de extensão de rede Scale Out.":::

8. Insira a contagem de dispositivo correspondente à contagem de comutador do DVS.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Insira a contagem de dispositivo correspondente à contagem de comutador do DVS.":::

9. Selecione **continuar** para ignorar.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Selecione continuar para ignorar.":::

10. Examine a versão prévia da topologia e selecione **Continuar**. 

11. Insira um nome amigável para esta malha de serviço e selecione **concluir** para concluir.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="Malha de serviço completa":::

   A malha de serviço foi implantada e configurada.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Malha de serviço implantada":::

## <a name="check-appliance-status"></a>Verificar status do dispositivo
Para verificar o status do dispositivo, selecione **Interconexão** > **Dispositivos**. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="Verifique o status do dispositivo.":::

## <a name="next-steps"></a>Próximas etapas

Quando o status do **túnel** de interconexão do dispositivo for **ativo** e verde, você estará pronto para migrar e proteger as VMs da solução Azure VMware usando o HCX. Confira a [Documentação da HCX do VMware](https://docs.vmware.com/en/VMware-HCX/index.html) e [Migrar máquinas virtuais com a HCX do VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica do VMware.
