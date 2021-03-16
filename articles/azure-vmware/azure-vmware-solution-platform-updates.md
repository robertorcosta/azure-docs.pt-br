---
title: Atualizações de plataforma para a solução do Azure VMware
description: Saiba mais sobre as atualizações de plataforma para a solução do Azure VMware.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 4f4c697f345cca093a83eab2f915aaf9e80ab10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563122"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Atualizações de plataforma para a solução do Azure VMware

Atualizações importantes para a solução do Azure VMware serão aplicadas a partir de março de 2021. Você receberá uma notificação por meio da integridade do serviço do Azure que inclui a linha do tempo da manutenção. Neste artigo, você aprenderá o que esperar durante essa operação de manutenção e as alterações em sua nuvem privada.

## <a name="march-15-2021"></a>15 de março de 2021 

- O serviço de solução do Azure VMware executará o trabalho de manutenção para atualizar o vCenter Server em sua nuvem privada para vCenter Server versão 6,7 atualização do 3L até 19 de março de 2021.

- Durante esse tempo, o VMware vCenter estará indisponível e você não poderá gerenciar VMs (parar, iniciar, criar, excluir). A HA (alta disponibilidade) do VMware continuará a operar para fornecer proteção para VMs existentes. O dimensionamento da nuvem privada (adicionando/removendo servidores e clusters) também estará indisponível.
 
Para obter mais informações sobre esta versão do vCenter, consulte [VMware vCenter Server 6,7 Update 3L Release Notes](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 de março de 2021

- As soluções do Azure VMware aplicarão patches ao ESXi em nuvens privadas existentes para [VMware ESXi 6,7, versão de patch ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) até 15 de março de 2021.

- As soluções alternativas documentadas para a pilha vSphere, de acordo com o [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), também serão aplicadas até 15 de março de 2021.

>[!NOTE]
>Isso não causa interrupções e não deve afetar os serviços ou cargas de trabalho do Azure VMware. Durante a manutenção, vários alertas do VMware, como a _conectividade de rede perdida em DVPorts_ e a _redundância de uplink perdida em DVPorts_, aparecem no vCenter e são desmarcados automaticamente à medida que a manutenção progride.

## <a name="post-update"></a>Pós-atualização
Após a conclusão, as versões mais recentes dos componentes do VMware serão exibidas. Se você perceber algum problema ou tiver dúvidas, entre em contato com nossa equipe de suporte abrindo um tíquete de suporte.



