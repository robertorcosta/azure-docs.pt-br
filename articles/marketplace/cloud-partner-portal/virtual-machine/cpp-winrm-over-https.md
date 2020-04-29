---
title: Gerenciamento Remoto do Windows sobre HTTPS para o Azure | Azure Marketplace
description: Explica como configurar uma VM do Windows hospedada no Azure para que ela possa ser gerenciada remotamente com o PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 88941b334a9c218365bd2d97046dbb6c32d50f35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142760"
---
# <a name="windows-remote-management-over-https"></a>Gerenciamento remoto do Windows por HTTPS

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de máquina virtual do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [criar uma máquina virtual do Azure oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) para gerenciar suas ofertas migradas.

Esta seção explica como configurar uma VM hospedada no Azure, com base no Windows, para que ela possa ser gerenciada e implantada remotamente com o PowerShell.  Para habilitar a comunicação remota do PowerShell, a VM de destino precisa expor um ponto de extremidade HTTPS do WinRM (Gerenciamento Remoto do Windows).  Para obter mais informações sobre a comunicação remota do PowerShell, confira [Executando comandos remotos](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Para obter mais informações sobre o WinRM, confira [Gerenciamento Remoto do Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Se você criou uma VM usando uma das abordagens "clássicas" do Azure — o portal de Service Manager do Azure ou o [API de gerenciamento de serviços do Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))preterido, ele será configurado automaticamente com um ponto de extremidade do WinRM.  No entanto, se você criar uma VM usando qualquer uma das abordagens "modernas” do Azure, a VM *não* estará configurada para usar WinRM por HTTPS.

- Usando o [portal do Azure](https://portal.azure.com/), normalmente de uma base aprovada, conforme descrito na seção [Criar um VHD compatível com o Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Usando os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Usando o shell de comando do Azure PowerShell ou da CLI do Azure.  Para obter exemplos, consulte [início rápido: criar uma máquina virtual do Windows no Azure com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [o início rápido: criar uma máquina virtual Linux com o CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Esse ponto de extremidade do WinRM também é necessário para executar o kit da ferramenta de certificação para a integração da VM, conforme descrito em [Certificar sua imagem de VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Por outro lado, normalmente, as VMs do Linux são gerenciadas remotamente usando a [CLI do Azure](https://docs.microsoft.com/cli/azure) ou os comandos do Linux em um console SSH.  O Azure também fornece vários métodos alternativos para [executar scripts em sua VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Para cenários mais complexos, há uma série de soluções de integração e automação disponíveis para VMs baseadas no Windows ou no Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurar e implantar com o WinRM

O ponto de extremidade do WinRM de uma VM baseada no Windows pode ser configurado durante dois estágios diferentes de seu desenvolvimento:

- Durante a criação – durante a implantação de uma VM em um VHD existente.  Essa é a abordagem preferencial para novas ofertas.  Essa abordagem exige a criação de um certificado do Azure, usando modelos do Azure Resource Manager fornecidos e executando scripts do PowerShell personalizados.
- Após a implantação – em uma VM existente, hospedada no Azure.  Use essa abordagem se você já tiver uma solução de VM implantada no Azure e precisar habilitar o gerenciamento remoto do Windows para ela.  Essa abordagem requer alterações manuais no portal do Azure e a execução de um script na VM de destino.


## <a name="next-steps"></a>Próximas etapas
Se você estiver criando uma VM, habilite o WinRM durante a [implantação da VM por meio de seus VHDs](./cpp-deploy-vm-vhd.md).  Caso contrário, o WinRM poderá ser habilitado em uma VM existente
