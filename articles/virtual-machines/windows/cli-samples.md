---
title: Exemplos de CLI do Azure - Windows
description: Exemplos de CLI do Azure - Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3d91197b72071c6379ea74d212aa7fdc550f4780
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288338"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Exemplos de CLI do Azure para máquinas virtuais do Windows

A tabela a seguir inclui links para scripts bash criados usando a CLI do Azure que implanta máquinas virtuais do Windows.

| Script | Descrição |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual do Windows com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria várias máquinas virtuais em uma configuração altamente disponíveis e de balanceamento de carga. |
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e usa a Extensão de Script Personalizado do Azure para instalar o IIS. |
| [Criar uma máquina virtual e executar a configuração da DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e usa a extensão DSC (Configuração de Estado de Desejado) do Azure para instalar o IIS. |
|**Gerenciar armazenamento**||
| [Criar disco gerenciado com base em um VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerenciado de um VHD especializado como um disco do sistema operacional ou de um VHD de dados como um disco de dados.  |
| [Criar um disco gerenciado com base em um instantâneo](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerenciado com base em um instantâneo. |
| [Copiar um disco gerenciado para a mesma assinatura ou outra assinatura](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia um disco gerenciado para a mesma assinatura ou outra assinatura, mas na mesma região do disco gerenciado pai. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta um instantâneo gerenciado como VHD para uma conta de armazenamento em outra região. |
| [Exportar o VHD de um disco gerenciado para uma conta de armazenamento](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento em outra região. |
| [Copiar um instantâneo para a mesma assinatura ou outra assinatura](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia um instantâneo para a mesma assinatura ou outra assinatura, mas na mesma região do instantâneo pai. |
|**Máquinas virtuais de rede**||
| [Proteger o tráfego de rede entre máquinas virtuais](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados e grupos de segurança de rede (NSG) internos e externos. |
|**Proteger máquinas virtuais**||
| [Criptografar uma VM e discos de dados](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um Azure Key Vault, uma chave de criptografia e entidade de serviço e, em seguida, criptografa uma VM. |
|**Monitorar máquinas virtuais**||
| [Monitorar uma VM com o Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual, instala o agente Log Analytics e a VM em um espaço de trabalho do Log Analytics.  |
| | |
