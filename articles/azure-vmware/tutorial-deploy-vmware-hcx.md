---
title: Tutorial – Implantar e configurar o VMware HCX
description: Saiba como implantar e configurar a solução VMware HCX para sua nuvem privada da Solução VMware no Azure.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578625"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Implantar e configurar o VMware HCX

Neste artigo, percorreremos os procedimentos para implantar e configurar o VMware HCX para sua nuvem privada da Solução VMware no Azure. O VMware HCX permite a migração das suas cargas de trabalho do VMware para a Solução VMware no Azure e para outros sites conectados por meio de vários tipos de migração.

O VMware HCX Advanced (pré-implantado na Solução VMware no Azure) dá suporte a até três conexões de site (local para nuvem ou nuvem para nuvem). Se mais de três conexões de site para o VMware HCX Enterprise forem necessárias, você terá a opção de habilitar o complemento [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) (que está em *Versão prévia* no momento) enviando uma [solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support). O VMware HCX EE (Edição Enterprise) está disponível com a Solução VMware no Azure como uma função ou um serviço de *Versão prévia*. Embora o VMware HCX EE para a Solução VMware no Azure esteja em *Versão prévia*, ele é uma função ou um serviço gratuito e está sujeito aos termos e condições do serviço de Versão prévia. Depois que o serviço VMware HCX EE ficar em GA, você receberá um aviso de 30 dias de que a cobrança será iniciada. Você também terá a opção de desativar ou recusar o serviço.

Antes de começar, examine detalhadamente as seções [Antes de começar](#before-you-begin), [Requisitos de versão do software](#software-version-requirements) e [Pré-requisitos](#prerequisites). 

Em seguida, percorreremos todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implantar o OVA do VMware HCX (Conector) local
> * Ativar o VMware HCX
> * Emparelhar o ambiente local com o ambiente da Solução VMware no Azure.
> * Configurar a interconexão (perfil de computação, perfis de rede e malha de serviço)
> * Concluir a instalação verificando o status do dispositivo.

Após a conclusão, você poderá seguir as próximas etapas recomendadas no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar
   
* Examine a [série de tutoriais](tutorial-network-checklist.md) do SDDC (Datacenter Definido pelo Software) da Solução VMware no Azure.
* Examine e confira a documentação do [VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), incluindo o guia do usuário do HCX.
* Examine os documentos do VMware sobre [Como migrar máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Opcionalmente, examine as [Considerações sobre a implantação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Opcionalmente, examine os materiais sobre o VMware relacionados à HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) sobre VMware vSphere na HCX. 
* Opcionalmente, solicite uma ativação do HCX Enterprise da Solução VMware no Azure por meio dos canais de suporte da Solução VMware no Azure.
* Para implantar o dispositivo de Interconexão de WAN, intervalos CIDR específicos já estão alocados do `\22` fornecido pelo cliente para a criação da nuvem privada.

O dimensionamento de cargas de trabalho em relação aos recursos de computação e de armazenamento é uma etapa de planejamento essencial. Resolva a etapa de dimensionamento como parte do planejamento inicial do ambiente de nuvem privada. 

Você também pode dimensionar as cargas de trabalho concluindo uma [Avaliação da Solução VMware no Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) no portal de Migrações para Azure.

## <a name="software-version-requirements"></a>Requisitos de versão do software

Os componentes de infraestrutura devem estar executando a versão mínima necessária. 
                                                         
| Tipo de componente    | Requisitos do ambiente de origem    | Requisitos do ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se estiver usando o 5.5 U1 ou anteriores, use a interface do usuário autônoma do HCX para as operações do HCX.  | 6.0 U2 e posterior   |
| ESXi   | 5.0    | ESXi 6.0 e posterior   |
| NSX    | Para a Extensão de Rede do HCX de comutadores lógicos na origem: NSXv 6.2+ ou NSX-T 2.4+   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para roteamento de proximidade do HCX: NSXv 6.4 ou posterior (não há suporte para o roteamento de proximidade com o NSX-T) |
| vCloud Director   | Não obrigatório – sem interoperabilidade com o vCloud Director no site de origem | Quando o ambiente de destino é integrado ao vCloud Director, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

### <a name="network-and-ports"></a>Rede e portas

* O [Alcance Global do ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) configurado entre o ambiente local e os circuitos do ExpressRoute do SDDC da Solução VMware no Azure.

* Todas as portas necessárias devem estar abertas para a comunicação entre os componentes locais e entre o ambiente local e o SDDC da Solução VMware no Azure (confira a [Documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Os dispositivos HCX IX e NE locais devem ser capazes de alcançar a infraestrutura do vCenter e do ESXi.

### <a name="ip-addresses"></a>Endereços IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implantar o VMware HCX OVA no local

>[!NOTE]
>Antes de implantar o dispositivo virtual no vCenter local, baixe o OVA do VMware HCX. 

1. Entre no HCX Manager da Solução VMware no Azure na porta `https://x.x.x.9` 443 com as credenciais do usuário **cloudadmin** e acesse o **Suporte**.

   >[!TIP]
   >Para identificar o endereço IP do HCX Manager, na folha da Solução VMware no Azure, acesse **Gerenciar** > **Conectividade** e selecione a guia **HCX**. 
   >
   >A senha do vCenter foi definida quando você configurou a nuvem privada.

1. Clique no link **Baixar** para baixar o arquivo OVA VMware HCX.

1. Acesse o vCenter local e selecione um modelo OVF, que é o arquivo OVA que você baixou para a implantação no vCenter local.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecione um nome e uma localização, o recurso ou o cluster no qual você está implantando o HCX e examine os detalhes e os recursos necessários.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Examine os termos de licença e, se você concordar com eles, selecione o armazenamento e a rede necessários e clique em **Avançar**.

1. Em **Personalizar modelo**, insira todas as informações necessárias. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selecione **Avançar**, verifique a configuração e escolha **Concluir** para implantar o OVA do HCX.
     
   >[!NOTE]
   >Em geral, o VMware HCX Manager que você está implantando agora é implantado na rede de gerenciamento do cluster.  
   
   > [!IMPORTANT]
   > Após a conclusão da implantação, talvez seja necessário ligar o dispositivo virtual manualmente.
   > Depois de ligar o dispositivo HCX, aguarde de 10 a 15 minutos para passar para a próxima etapa.

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Implantação do dispositivo VMware HCX](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>Ativar o VMware HCX

Depois de implantar o OVA do VMware HCX local, você estará pronto para ativar o VMware HCX. Para ativar o VMware HCX, recupere uma licença.

1. Na Solução VMware no Azure, acesse **Gerenciar** > **Conectividade**, selecione a guia **HCX** e clique em **Adicionar**.

1. Entre no VMware HCX Manager local em `https://HCXManagerIP:9443` e entre com as credenciais do usuário **administrador**. 

   > [!IMPORTANT]
   > Inclua o número da porta `9443` com o endereço IP do VMware HCX Manager.

1. Em **Licenciamento**, insira sua **Chave avançada de HCX**.  
   
    > [!NOTE]
    > O VMware HCX Manager precisa ter acesso à Internet aberta ou a um proxy configurado.

1. Em **Localização do Datacenter**, forneça a localização mais próxima na qual o VMware HCX Manager local está instalado.

1. Modifique o **Nome do Sistema** ou aceite o padrão.
   
1. Você pode Concluir Mais Tarde ou Continuar, selecionando a opção **Sim, Continuar**.
    
1. Em **Conectar o vCenter**, forneça o FQDN ou o endereço IP do vCenter Server e as credenciais apropriadas e clique em **Continuar**.
   
1. Em **Configurar o SSO/PSC**, forneça o FQDN ou o endereço IP do seu PSC e clique em **Continuar**.
   
   >[!NOTE]
   >Normalmente, trata-se do mesmo que o FQDN ou o IP do vCenter.

1. Verifique se todas as entradas estão corretas e selecione **Reiniciar**.
    
   > [!NOTE]
   > Haverá um atraso após a reinicialização até o aviso para avançar para a próxima etapa.
   >
   >Após a reinicialização dos serviços, é importante observar se o vCenter é exibido como verde na tela que aparece. O vCenter e o SSO têm os parâmetros de configuração apropriados, que devem ser iguais aos da tela anterior.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Ativação do VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-vmware-hcx"></a>Configurar o VMware HCX

Agora você está pronto para adicionar um emparelhamento de site, criar um perfil de rede e de computação e habilitar serviços, como migração, extensão de rede ou recuperação de desastre. 

### <a name="add-a-site-pairing"></a>Adicionar um emparelhamento de site

Você pode conectar (emparelhar) o VMware HCX Manager na Solução VMware no Azure com o VMware HCX Manager no datacenter. 

1. Entre no vCenter local e, na **Página Inicial**, selecione **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Em **Infraestrutura**, selecione **Emparelhamento de Site** e escolha a opção **Conectar com o Site Remoto** (no meio da tela). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Insira a **URL ou o endereço IP do HCX remoto**, o nome de usuário cloudadmin@vsphere.local e a **senha** da Solução VMware no Azure e selecione **Conectar**.

   > [!NOTE]
   > A **URL do HCX remoto** é o HCX Manager de nuvem privada da Solução VMware no Azure, normalmente o endereço ".9" da rede de gerenciamento.  Por exemplo, se o vCenter for 192.168.4.2, a URL do HCX será 192.168.4.9.
   >
   > A **senha** será a mesma senha que a usada para entrar no vCenter. Você definiu essa senha na tela inicial de implantação.

   Uma tela é exibida com o HCX Manager na Solução VMware no Azure e o HCX Manager local conectado (emparelhados).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local.":::

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Emparelhamento de site do VMware HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Criar perfis de rede

O VMware HCX implanta alguns dispositivos virtuais (automatizados), mas precisa de vários segmentos de IP.  Ao criar seus perfis de rede, você define os segmentos de IP, que são identificados durante a [fase de preparação e planejamento de pré-implantação de segmentos de rede do VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Você criará quatro perfis de rede:

   - Gerenciamento
   - vMotion
   - Replicação
   - Uplink

1. Em **Infraestrutura**, selecione **Interconexão** > **Malha de Serviço Multissite** > **Perfis de Rede** > **Criar Perfil de Rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Para cada perfil de rede, selecione a rede e o grupo de portas, forneça um nome, crie o pool de IP para esse segmento específico e selecione **Criar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local.":::

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Criação de perfil de rede do VMware HCX](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-compute-profile"></a>Criar perfil de computação

1. Selecione **Perfis de Computação** > **Criar Perfil de Computação**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Insira um nome para o perfil e clique em **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecione os serviços a serem habilitados, como migração, extensão de rede ou recuperação de desastre e clique em **Continuar**.
  
   > [!NOTE]
   > Geralmente, nada será alterado aqui.

1. Em **Selecionar Recursos de Serviço**, selecione um ou mais recursos de serviço (clusters) a serem habilitados para os serviços do VMware HCX selecionados.  

1. Quando os clusters aparecerem no datacenter local, clique em **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Em **Selecionar o Armazenamento de Dados**, escolha o recurso de armazenamento de dados para a implantação dos dispositivos VMware HCX Interconnect e clique em **Continuar**.

   Quando vários recursos são selecionados, o VMware HCX usa o primeiro recurso selecionado até que sua capacidade seja esgotada.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Em **Selecionar o Perfil de Rede de Gerenciamento**, escolha o perfil de rede de gerenciamento que você criou nas etapas anteriores e clique em **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > O perfil de rede de gerenciamento permite que os dispositivos VMware HCX se comuniquem com o vCenter e que os hosts ESXi sejam acessados.

1. Em **Selecionar o Perfil de Rede de Uplink**, escolha o perfil de rede de uplink que você criou nas etapas anteriores e clique em **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Em **Selecionar o Perfil de Rede do vMotion**, escolha o perfil de rede do vMotion que você criou nas etapas anteriores e clique em **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Em **Selecionar o Perfil de Rede de Replicação do vSphere**, escolha o perfil de rede de replicação do vSphere que você criou nas etapas anteriores e clique em **Continuar**.

   Na maioria dos casos, o perfil de rede de replicação é o mesmo que o perfil de rede de gerenciamento.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Em **Selecionar os Comutadores Distribuídos para Extensões de Rede**, escolha os comutadores virtuais distribuídos que contêm as máquinas virtuais a serem migradas para a Solução VMware no Azure em uma rede estendida da camada 2 e clique em **Continuar**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Examine as regras de conexão e clique em **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecione **Concluir** para criar o perfil de computação.

   Você verá uma tela semelhante à mostrada abaixo.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Criação de perfil de computação do VMware HCX](https://www.youtube.com/embed/qASXi5xrFzM).




### <a name="create-service-mesh"></a>Criar malha de serviço

Este é o momento de configurar a malha de serviço entre o ambiente local e o SDDC da Solução VMware no Azure.

1. Em **Infraestrutura**, selecione **Interconexão** > **Malha de Serviço** > **Criar Malha de Serviço**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Examine os sites que já foram populados e clique em **Continuar**. 

   >[!NOTE]
   >Se esta for sua primeira configuração de malha de serviço, você não precisará modificar esta tela.  

1. Selecione na lista suspensa os perfis de computação de origem e remoto e clique em **Continuar**.  

   As seleções definem os recursos nos quais as VMs podem consumir os serviços do VMware HCXs.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Examine os serviços que serão habilitados e clique em **Continuar**.  

1. Em **Configuração Avançada – Substituir Perfis de rede de Uplink** clique em **Continuar**.  Os perfis de rede de uplink conectam-se à rede por meio da qual os dispositivos de interconexão do site remoto podem ser alcançados.  
  
1. Em **Configuração Avançada – Escala Horizontal do Dispositivo de Extensão de Rede**, examine a configuração e clique em **Continuar**. 

1. Em **Configuração Avançada – Engenharia de Tráfego**, examine a configuração, faça as modificações que julgar necessário e clique em **Continuar**.

1. Examine a versão prévia da topologia e selecione **Continuar**.

1. Insira um nome amigável para esta malha de serviço e selecione **Concluir** para finalizar.  

1. Selecione **Exibir Tarefas** para monitorar a implantação. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local.":::

   Quando a implantação da malha de serviço for concluída com êxito, você verá os serviços como verdes.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifique a integridade da malha de serviço conferindo o status do dispositivo. Selecione **Interconexão** > **Dispositivos**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Criação da malha de serviço do VMware HCX](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Opcional) Criar uma extensão de rede

Se você quiser estender alguma rede do ambiente local para a Solução VMware no Azure, siga estas etapas.

1. Em **Serviços**, selecione **Extensão de Rede** e escolha **Criar uma Extensão de Rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecione cada uma das redes que deseja estender para a Solução VMware no Azure e clique em **Avançar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local.":::

1. Insira o IP do gateway local de cada uma das redes que você está estendendo e clique em **Enviar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local.":::

   Demora alguns minutos até que a extensão de rede seja concluída. Após a conclusão, o status será alterado para **extensão concluída**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Acesse o vCenter local e selecione um modelo OVF a ser implantado no vCenter local." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure – Extensão de rede do VMware HCX](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Próximas etapas

Se você chegou neste ponto e o status do túnel de interconexão do dispositivo é **UP** e está verde, você pode migrar e proteger as VMs da Solução VMware no Azure usando o VMware HCX.  A Solução VMware no Azure dá suporte a migrações de carga de trabalho (com ou com uma extensão de rede).  Você ainda pode fazer migrações de carga de trabalho no ambiente vSphere, a criação local de redes e a implantação de VMs nessas redes.  Para obter mais informações, confira a [documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [Como migrar máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica do VMware para obter detalhes de como usar o HCX.
