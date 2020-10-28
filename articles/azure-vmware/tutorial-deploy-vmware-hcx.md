---
title: Tutorial – Implantar e configurar o VMware HCX
description: Saiba como implantar e configurar uma solução VMware HCX para sua nuvem privada da Solução VMware no Azure.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: c78eae11497702054bb54b5980228fd0a3962577
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367764"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Implantar e configurar o VMware HCX

Neste artigo, percorreremos os procedimentos para implantar e configurar o VMware HCX Connector local em sua nuvem privada da Solução VMware no Azure. Com o VMware HCX, você pode migrar suas cargas de trabalho do VMware para a Solução VMware no Azure e para outros sites conectados por meio de vários tipos de migração. Como a Solução VMware no Azure implanta e configura o HCX Cloud Manager, você precisa baixar, ativar e configurar o HCX Connector em seu datacenter do VMware local.

O VMware HCX Advanced Connector é implantado previamente na Solução VMware no Azure. Ele dá suporte a até três conexões de site (local para nuvem ou nuvem para nuvem). Se você precisar de mais de três conexões de site, envie uma [solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support) para habilitar o complemento [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/). No momento, o complemento está em versão prévia. 

>[!NOTE]
>O VMware HCX EE (Edição Enterprise) está disponível com a Solução VMware no Azure como um serviço em versão prévia. Ele é gratuito e está sujeito aos termos e condições de um serviço em versão prévia. Quando o serviço VMware HCX EE estiver em disponibilidade geral, você receberá um aviso de 30 dias de que a cobrança será iniciada. Você também terá a opção de desligar ou recusar o serviço.

Primeiro, analise detalhadamente as seções [Antes de começar](#before-you-begin), [Requisitos de versão do software](#software-version-requirements) e [Pré-requisitos](#prerequisites) deste artigo. 

Em seguida, percorreremos todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implantar o OVA do VMware HCX (HCX Connector) local.
> * Ativar o VMware HCX Connector.
> * Emparelhar o HCX Connector local com o HCX Cloud Manager da Solução VMware no Azure.
> * Configurar a interconexão (perfil de rede, perfil de computação e malha de serviço).
> * Concluir a instalação verificando o status do dispositivo e validando que a migração é possível.

Após a conclusão, você poderá seguir as próximas etapas recomendadas no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar
   
* Examine a [série de tutoriais](tutorial-network-checklist.md) do SDDC (Datacenter Definido pelo Software) da Solução VMware no Azure.
* Examine e confira a documentação do [VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), incluindo o guia do usuário do HCX.
* Examine [Como migrar máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na Documentação do VMware.
* Opcionalmente, examine as [Considerações sobre a implantação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Opcionalmente, examine os materiais sobre o VMware relacionados à HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) sobre o VMware vSphere. 
* Opcionalmente, solicite uma ativação do HCX Enterprise da Solução VMware no Azure por meio dos canais de suporte da Solução VMware no Azure.
* Opcionalmente, [examine as portas de rede necessárias para o HCX](https://ports.vmware.com/home/VMware-HCX).
* Embora o HCX Cloud Manager da Solução VMware no Azure venha pré-configurada na rede /22 fornecida para a nuvem privada da Solução VMware no Azure, o HCX Connector local exige que você aloque intervalos de rede de sua rede local. Essas redes e intervalos são descritos mais adiante neste artigo.

O dimensionamento de cargas de trabalho em relação aos recursos de computação e de armazenamento é uma etapa de planejamento essencial. Cuide da etapa de dimensionamento como parte do planejamento inicial do ambiente de nuvem privada. 

Você pode dimensionar as cargas de trabalho concluindo uma [Avaliação da Solução VMware no Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) no portal das Migrações para Azure.

## <a name="software-version-requirements"></a>Requisitos de versão do software

Os componentes de infraestrutura devem estar executando a versão mínima necessária. 
                                                         
| Tipo de componente    | Requisitos do ambiente de origem    | Requisitos do ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se estiver usando o 5.5 U1 ou anteriores, use a interface do usuário autônoma do HCX para as operações do HCX.  | 6.0 U2 e posteriores   |
| ESXi   | 5.0    | ESXi 6.0 e posteriores   |
| NSX    | Para a extensão de rede do HCX de comutadores lógicos na origem: NSXv 6.2+ ou NSX-T 2.4+.   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para roteamento de proximidade do HCX: NSXv 6.4+. (O Roteamento de Proximidade não tem suporte com o NSX-T.) |
| vCloud Director   | Não necessário. Não há interoperabilidade com o vCloud Director no site de origem. | Quando você estiver integrando o ambiente de destino ao vCloud Director, o mínimo será 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

### <a name="network-and-ports"></a>Rede e portas

* Configure o [Alcance Global do Azure ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) entre o ambiente local e os circuitos do ExpressRoute do SDDC da Solução VMware no Azure.

* [Todas as portas necessárias](https://ports.vmware.com/home/VMware-HCX) devem estar abertas para comunicação entre os componentes locais e o SDDC da Solução VMware no Azure.

Para obter mais informações, confira a [Documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>Endereços IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Implantar o OVA do VMware HCX Connector localmente

> [!NOTE]
> Antes de implantar o dispositivo virtual no vCenter local, baixe o OVA do VMware HCX Connector. 

1. Abra uma janela do navegador, entre no HCX Manager da Solução VMware no Azure na porta `https://x.x.x.9` 443 com as credenciais do usuário **cloudadmin** e acesse o **Suporte** .

   > [!TIP]
   > Anote o endereço IP do HCX Cloud Manager na Solução VMware no Azure. Para identificar o endereço IP, no painel da Solução VMware no Azure, acesse **Gerenciar** > **Conectividade** e selecione a guia **HCX** . 
   >
   >A senha do vCenter foi definida quando você configurou a nuvem privada.

1. Selecione o link **Baixar** para baixar o arquivo OVA do VMware HCX Connector.

1. Vá até o vCenter local. Selecione um modelo OVF, que é o arquivo OVA que você baixou para a implantar o HCX Connector no vCenter local.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecione um nome e uma localização e escolha um recurso/cluster no qual o HCX Connector será implantado. Em seguida, examine os detalhes e os recursos necessários.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Examine os termos de licença. Se você concordar com eles, selecione o armazenamento e a rede necessários e clique em **Avançar** .

1. Em **Personalizar modelo** , insira todas as informações necessárias. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selecione **Avançar** , verifique a configuração e selecione **Concluir** para implantar o OVA do HCX Connector.
     
   > [!NOTE]
   > Em geral, o VMware HCX Connector que você está implantando agora é implantado na rede de gerenciamento do cluster.  
   
   > [!IMPORTANT]
   > Talvez seja necessário ativar o dispositivo virtual manualmente.  Se esse for o caso, aguarde de 10 a 15 minutos antes de prosseguir para a próxima etapa.

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Implantação do Dispositivo HCX](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>Ativar o VMware HCX

Depois de implantar o OVA do VMware HCX Connector no local e iniciar o dispositivo, você estará pronto para ativar. Primeiro, você precisa obter uma chave de licença do portal da Solução VMware no Azure.

1. No portal da Solução VMware no Azure, acesse **Gerenciar** > **Conectividade** , selecione a guia **HCX** e clique em **Adicionar** .

1. Use as credenciais de **administrador** para entrar no VMware HCX Manager local em `https://HCXManagerIP:9443`. 

   > [!IMPORTANT]
   > Inclua o número da porta `9443` com o endereço IP do VMware HCX Manager.

1. Em **Licenciamento** , insira sua chave correspondente à **Chave Avançada de HCX** .  
   
    > [!NOTE]
    > O VMware HCX Manager precisa ter acesso à Internet aberta ou a um proxy configurado.

1. Em **Localização do Datacenter** , forneça a localização mais próxima para instalação do VMware HCX Manager local está.

1. Em **Nome do Sistema** , modifique o nome ou aceite o padrão.
   
1. Selecione **Sim, Continuar** .
    
1. Em **Conectar o vCenter** , forneça o FQDN ou o endereço IP do vCenter Server e as credenciais apropriadas e clique em **Continuar** .
   
1. Em **Configurar o SSO/PSC** , forneça o FQDN ou o endereço IP de seu Platform Services Controller e clique em **Continuar** .
   
   >[!NOTE]
   >Normalmente, essa entrada é igual ao seu endereço IP ou FQDN do vCenter.

1. Verifique se todas as entradas estão corretas e selecione **Reiniciar** .
    
   > [!NOTE]
   > Haverá um atraso após a reinicialização até o aviso para avançar para a próxima etapa.

Após a reinicialização dos serviços, é importante observar se o vCenter é exibido como verde na tela que aparece. O vCenter e o SSO precisam ter os parâmetros de configuração apropriados, que devem ser iguais aos da tela anterior.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Ativar HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-the-vmware-hcx-connector"></a>Configurar o VMware HCX Connector

Agora você está pronto para adicionar um emparelhamento de site, criar um perfil de rede e de computação e habilitar serviços, como migração, extensão de rede ou recuperação de desastre. 

### <a name="add-a-site-pairing"></a>Adicionar um emparelhamento de site

Você pode conectar (emparelhar) o VMware HCX Cloud Manager na Solução VMware no Azure com o VMware HCX Connector no datacenter. 

1. Entre no vCenter local e, na **Página Inicial** , selecione **HCX** .

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Em **Infraestrutura** , selecione **Emparelhamento de Site** e escolha a opção **Conectar com o Site Remoto** (no meio da tela). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Insira a URL ou o endereço IP do HCX Remoto que você anotou, o nome de usuário cloudadmin@vsphere.local e a senha da Solução VMware no Azure. Depois, selecione **Conectar** .

   > [!NOTE]
   > Para estabelecer um par de sites com êxito, o HCX Connector deve ser capaz de rotear para o IP do Gerenciador de Nuvem do HCX pela porta 443.
   >
   > A senha é a mesma que a usada para entrar no vCenter. Você definiu essa senha na tela inicial de implantação.

   Uma tela é exibida com o HCX Cloud Manager na Solução VMware no Azure e o HCX Connector local conectados (emparelhados).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Captura de tela da navegação até um modelo OVF.":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Emparelhamento de Sites do HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Criar perfis de rede

O VMware HCX implanta um subconjunto de dispositivos virtuais (automatizados) que exigem vários segmentos de IP. Ao criar seus perfis de rede, você define os segmentos de IP, que são identificados durante a [fase de preparação e planejamento de pré-implantação de segmentos de rede do VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Você criará quatro perfis de rede:

   - Gerenciamento
   - vMotion
   - Replicação
   - Uplink

1. Em **Infraestrutura** , selecione **Interconexão** > **Malha de Serviço Multissite** > **Perfis de Rede** > **Criar Perfil de Rede** .

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Para cada perfil de rede, selecione a rede e o grupo de portas, forneça um nome e crie o pool de IPs para esse segmento. Em seguida, selecione **Criar** . 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Captura de tela da navegação até um modelo OVF.":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Perfil de Rede do HCX](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-a-compute-profile"></a>Criar um perfil de computação

1. Selecione **Perfis de Computação** > **Criar Perfil de Computação** .

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Insira um nome para o perfil e clique em **Continuar** .  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecione os serviços a serem habilitados, como migração, extensão de rede ou recuperação de desastre e clique em **Continuar** .
  
   > [!NOTE]
   > Normalmente, nada é alterado aqui.

1. Em **Selecionar Recursos de Serviço** , selecione um ou mais recursos de serviço (clusters) a serem habilitados para os serviços do VMware HCX selecionados.  

1. Quando os clusters aparecerem no datacenter local, clique em **Continuar** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Em **Selecionar o Armazenamento de Dados** , escolha o recurso de armazenamento de dados para a implantação dos dispositivos VMware HCX Interconnect. Depois selecione **Continuar** .

   Quando vários recursos são selecionados, o VMware HCX usa o primeiro recurso selecionado até que sua capacidade seja esgotada.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Em **Selecionar o Perfil de Rede de Gerenciamento** , escolha o perfil de rede de gerenciamento que você criou nas etapas anteriores. Depois selecione **Continuar** .  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > O perfil de rede de gerenciamento permite que os dispositivos VMware HCX se comuniquem com o vCenter. Os hosts ESXi podem ser acessados por esse perfil.

1. Em **Selecionar o Perfil de Rede de Uplink** , escolha o perfil de rede de uplink que você criou nas etapas anteriores. Depois selecione **Continuar** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Em **Selecionar o Perfil de Rede do vMotion** , escolha o perfil de rede do vMotion que você criou nas etapas anteriores. Depois selecione **Continuar** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Em **Selecionar o Perfil de Rede de Replicação do vSphere** , escolha o perfil de rede de replicação que você criou nas etapas anteriores. Depois selecione **Continuar** .

   Na maioria dos casos, o perfil de rede de replicação é o mesmo que o perfil de rede de gerenciamento.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Em **Selecionar os Comutadores Distribuídos para Extensões de Rede** , selecione os comutadores virtuais distribuídos que contêm as máquinas virtuais a serem migradas para a Solução VMware no Azure em uma rede estendida da camada 2. Depois selecione **Continuar** .

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Examine as regras de conexão e clique em **Continuar** .  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecione **Concluir** para criar o perfil de computação.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Perfil de Computação](https://www.youtube.com/embed/qASXi5xrFzM).

### <a name="create-a-service-mesh"></a>Criar malha de serviço

Este é o momento de configurar a malha de serviço entre o ambiente local e o SDDC da Solução VMware no Azure.

   > [!NOTE]
   > Para estabelecer com êxito uma malha de serviço com a Solução VMware no Azure:
   >
   > As portas UDP 500/4500 estão abertas entre seus endereços de perfil de rede 'uplink' definidos pelo conector do HCX no local e os endereços de perfil de rede do 'uplink' do HCX Cloud da Solução VMware no Azure.
   >
   > Certifique-se de examinar as [portas necessárias do HCX](https://ports.vmware.com/home/VMware-HCX).

1. Em **Infraestrutura** , selecione **Interconexão** > **Malha de Serviço** > **Criar Malha de Serviço** .    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Examine os sites que já foram populados e clique em **Continuar** . 

   >[!NOTE]
   >Se esta for sua primeira configuração de malha de serviço, você não precisará modificar esta tela.  

1. Selecione os perfis de computação de origem e remoto nas listas suspensas e clique em **Continuar** .  

   As seleções definem os recursos nos quais as VMs podem consumir os serviços do VMware HCXs.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Examine os serviços que serão habilitados e selecione **Continuar** .  

1. Em **Configuração Avançada – Substituir Perfis de rede de Uplink** clique em **Continuar** .  

   Os perfis de rede de uplink conectam-se à rede por meio da qual os dispositivos de interconexão do site remoto podem ser alcançados.  
  
1. Em **Configuração Avançada – Escala Horizontal do Dispositivo de Extensão de Rede** , examine a configuração e clique em **Continuar** . 

1. Em **Configuração Avançada – Engenharia de Tráfego** , examine a configuração, faça as modificações que julgar necessárias e clique em **Continuar** .

1. Examine a versão prévia da topologia e selecione **Continuar** .

1. Insira um nome amigável para esta malha de serviço e selecione **Concluir** para finalizar.  

1. Selecione **Exibir Tarefas** para monitorar a implantação. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Captura de tela da navegação até um modelo OVF.":::

   Quando a implantação da malha de serviço for concluída com êxito, você verá os serviços como verdes.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifique a integridade da malha de serviço conferindo o status do dispositivo. 
1. Selecione **Interconexão** > **Dispositivos** .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Para obter uma visão geral de ponta a ponta desse procedimento, assista ao vídeo [Solução VMware no Azure: Malha de Serviço](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Opcional) Criar uma extensão de rede

Se você quiser estender alguma rede do ambiente local para a Solução VMware no Azure, siga estas etapas:

1. Em **Serviços** , selecione **Extensão de Rede** e escolha **Criar uma Extensão de Rede** .

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecione cada uma das redes que você deseja estender para a Solução VMware no Azure e clique em **Avançar** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Captura de tela da navegação até um modelo OVF.":::

1. Insira o IP do gateway local de cada uma das redes que você está estendendo e clique em **Enviar** . 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Captura de tela da navegação até um modelo OVF.":::

   Demora alguns minutos até que a extensão de rede seja concluída. Após a conclusão, o status será alterado para **Extensão concluída** .

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Captura de tela da navegação até um modelo OVF." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure: Extensão de Rede](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Próximas etapas

Se você chegou neste ponto e o status do túnel de interconexão do dispositivo é **UP** e está verde, você pode migrar e proteger as VMs da Solução VMware no Azure usando o VMware HCX. A Solução VMware no Azure dá suporte a migrações de carga de trabalho (com ou sem uma extensão de rede). Você ainda pode migrar cargas de trabalho no ambiente vSphere, além da criação local de redes e a implantação de VMs nessas redes.  

Para obter mais informações sobre como usar o HCX, acesse a documentação técnica do VMware:

* [Documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Como migrar máquinas virtuais com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [Portas necessárias do HCX](https://ports.vmware.com/home/VMware-HCX)
