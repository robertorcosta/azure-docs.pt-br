---
title: Solução Azure VMware by CloudSimple - Gerenciar VMs privadas em nuvem no Azure
description: Descreve como gerenciar VMs cloud privados do Cloud no portal Azure, incluindo a adição de discos, alteração da capacidade de VM e adição de interfaces de rede
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014990"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Gerencie suas máquinas virtuais CloudSimple Private Cloud no Azure

Para gerenciar as máquinas virtuais que você [criou para o seu CloudSimple Private Cloud,](azure-create-vm.md)entre no portal [Azure](https://portal.azure.com). Procure e selecione o virtual (pesquise em **Todos os Serviços** ou **Máquinas Virtuais** no menu lateral).

## <a name="control-virtual-machine-operation"></a>Controlar a operação da máquina virtual

Os controles a seguir estão disponíveis na página **Visão Geral** da máquina virtual selecionada.

| Control | Descrição |
| ------------ | ------------- |
| Conectar | Conecte-se à VM especificada.  |
| Iniciar | Inicie a VM especificada.  |
| Reiniciar | Desligue e, em seguida, energize a VM especificada.  |
| Stop | Desligue a VM específica.  |
| Capturar | Capture uma imagem da VM especificada para que ela possa ser usada como uma imagem para criar outras VMs. Veja [Criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Mover | Mova-se para a VM especificada.  |
| Excluir | Remova a VM especificada.  |
| Atualizar | Atualize os dados no visor.  |

### <a name="view-performance-information"></a>Exibir informações de desempenho

Os gráficos na área inferior da página **Visão Geral** apresentam dados de desempenho para o intervalo selecionado (última hora para durar 30 dias; padrão é última hora). Dentro de cada gráfico, você pode exibir os valores numéricos por qualquer tempo dentro do intervalo movendo seu cursor para frente e para trás sobre o gráfico.

Os gráficos a seguir são exibidos.

| Item | Descrição |
| ------------ | ------------- |
| CPU (média) | Utilização média da CPU em porcentagem ao longo do intervalo selecionado.   |
| Rede | Tráfego dentro e fora da rede (MB) durante o intervalo selecionado.  |
| Bytes de disco | Total de dados lidos de disco e gravados em disco (MB) durante o intervalo selecionado.  |
| Operações de disco | Taxa média de operações de disco (operações/segundo) durante o intervalo selecionado. |

## <a name="manage-vm-disks"></a>Gerenciar discos VM

Para adicionar um disco VM, abra a página **Discos** para a VM selecionada. Para adicionar um disco, clique **em Adicionar disco**. Configure cada uma das seguintes configurações inserindo ou selecionando uma opção inline. Clique em **Salvar**.

   | Item | Descrição |
   | ------------ | ------------- |
   | Nome | Insira um nome para identificar o disco.  |
   | Tamanho | Selecione um dos tamanhos disponíveis.  |
   | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para os diferentes sistemas operacionais suportados.  |
   | Mode | Determina como o disco participa de instantâneos. Escolha uma destas opções: <br> - Persistência independente: Todos os dados gravados no disco são gravados permanentemente.<br> - Independente, não persistente: As alterações escritas no disco são descartadas quando você desliga ou reinicia a máquina virtual.  Este modo permite que você sempre reinicie a VM no mesmo estado. Saiba mais na [documentação da VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Para excluir um disco, selecione-o e clique **em Excluir**.

## <a name="change-the-capacity-of-the-vm"></a>Alterar a capacidade da VM

Para alterar a capacidade da VM, abra a página **Tamanho** para a VM selecionada. Especifique qualquer um dos seguintes e clique **em Salvar**.

| Item | Descrição |
| ------------ | ------------- |
| Número de núcleos | Número de núcleos atribuídos à VM.  |
| Virtualização de hardware | Selecione a caixa de seleção para expor a virtualização de hardware ao sistema operacional convidado. Veja o artigo da VMware [Expor a virtualização assistida do hardware vmware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Tamanho da memória | Selecione a quantidade de memória a ser alocada no VM.  

## <a name="manage-network-interfaces"></a>Gerenciar interfaces de rede

Para adicionar uma interface, clique **em Adicionar interface de rede**. Configure cada uma das seguintes configurações inserindo ou selecionada uma opção inline. Clique em **Salvar**.

   | Control | Descrição |
   | ------------ | ------------- |
   | Nome | Insira um nome para identificar o adaptador.  |
   | Rede | Selecione na lista de redes configuradas em seu vSphere da Nuvem Privada.  |
   | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo base de conhecimento da VMware [Escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
   | Ligar na inicialização | Escolha se quer habilitar o hardware da NIC quando a VM for inicializada. O padrão é **Habilitar**. |

Para excluir uma interface de rede, selecione-a e clique **em Excluir**.
