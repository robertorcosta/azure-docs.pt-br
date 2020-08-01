---
title: Configurar operações de vRealize para a solução VMware do Azure (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475943"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Configurar operações de vRealize para a solução VMware do Azure (AVS)


o vRealize Operations Manager é uma plataforma de gerenciamento de operações que permite que os administradores de infraestrutura do VMware monitorem os recursos do sistema. Esses recursos do sistema podem ser objetos de nível de aplicativo ou de infraestrutura (físico e virtual). Historicamente, a maioria dos administradores de VMware usou operações de vRealize para monitorar e gerenciar os componentes de nuvem privada VMware – vCenter, ESXi, NSX-T, vSAN e HCX (extensão de nuvem híbrida). Cada nuvem privada de AVS é provisionada com uma implantação dedicada do vCenter, do NSX-T, do vSAN e do HCX. 

Revisão completa [antes de começar](#before-you-begin) e [pré-requisitos](#prerequisites) primeiro. Em seguida, vamos orientá-lo pelas duas topologias típicas de implantação para vRealize Operations Manager com a AVS:

> [!div class="checklist"]
> * [Operações de vRealize local Gerenciando a implantação da AVS](#on-premises-vrealize-operations-managing-avs-deployment)
> * [Operações vRealize em execução na implantação da AVS](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Antes de começar
* Examine a [documentação do produto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para saber mais sobre a implantação de operações do vRealize. 
* Examine a [série de tutoriais](tutorial-network-checklist.md)do SDDC (Data Center) definido pelo software de AVS básico.
* Opcionalmente, examine a documentação do produto do [controlador remoto do VRealize Operations](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) para a opção de implantação do vRealize operações de gerenciamento de AVS local. 



## <a name="prerequisites"></a>Pré-requisitos
* VPN ou Azure ExpressRoute devem ser configurados entre local e AVS SDDC.
* Uma nuvem privada de AVS foi implantada no Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Operações de vRealize local Gerenciando a implantação da AVS
A maioria dos clientes tem uma implantação local existente de operações vRealize usadas para gerenciar um ou mais domínios vCenters locais. Quando os clientes provisionam uma nova nuvem privada de AVS no Azure, normalmente conectam seu ambiente local com o AVS usando um Azure ExpressRoute ou usando uma solução de VPN de camada 3, como mostrado abaixo.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Operações de vRealize local Gerenciando a implantação da AVS"  border="false":::

Para estender os recursos de operações do vRealize para a nuvem privada da AVS, você cria uma[ instância de adaptador para os recursos de nuvem privada da AVS](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) para coletar dados da nuvem privada da AVS e colocá-los em operações vRealize locais. A instância Operations Manager local do vRealize pode se conectar diretamente ao vCenter e ao NSX-T Manager no AVS ou você pode, opcionalmente, implantar um coletor remoto de operações do vRealize na nuvem privada da AVS. Um coletor remoto de operações do vRealize compacta e criptografa os dados coletados da nuvem privada da AVS antes que eles sejam enviados pela rede de ExpressRoute ou VPN para o vRealize Operations Manager em execução no local. 

> [!TIP]
> Consulte a [documentação do VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>Operações vRealize em execução na implantação da AVS

Outra opção de implantação é implantar uma instância do vRealize Operations Manager em um dos clusters vSphere na nuvem privada da AVS, conforme mostrado abaixo. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operações vRealize em execução na AVS" border="false":::

Depois de implantar a instância de AVS de operações vRealize, você pode configurar operações vRealize para coletar dados do vCenter, ESXi, NSX-T, vSAN e HCX. 

> [!TIP]
> Consulte a [documentação do VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




