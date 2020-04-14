---
title: Gerenciamento remoto do Windows sobre HTTPS para Azure | Mercado Azure
description: Explica como configurar uma VM baseada no Windows hospedada no Azure para que possa ser gerenciada remotamente com o PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: b2a4bb107309894a7180e0a4585cdba6f04d1bee
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273028"
---
# <a name="windows-remote-management-over-https"></a>Gerenciamento remoto do Windows por HTTPS

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento das ofertas da Sua Máquina Virtual Do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de Máquina Virtual do Azure](https://aka.ms/CreateAzureVMoffer) para gerenciar suas ofertas migradas.

Esta seção explica como configurar uma VM hospedada no Azure, com base no Windows, para que ela possa ser gerenciada e implantada remotamente com o PowerShell.  Para habilitar a comunicação remota do PowerShell, a VM de destino precisa expor um ponto de extremidade HTTPS do WinRM (Gerenciamento Remoto do Windows).  Para obter mais informações sobre a comunicação remota do PowerShell, confira [Executando comandos remotos](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Para obter mais informações sobre o WinRM, confira [Gerenciamento Remoto do Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Se você criou uma VM usando uma das abordagens "clássicas" do Azure — seja o Portal do Gerente de Serviços do Azure ou a API de gerenciamento de [serviços do Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))depreciada — ela será configurada automaticamente com um ponto final do WinRM.  No entanto, se você criar uma VM usando qualquer uma das abordagens "modernas” do Azure, a VM *não* estará configurada para usar WinRM por HTTPS.

- Usando o [portal do Azure](https://portal.azure.com/), normalmente de uma base aprovada, conforme descrito na seção [Criar um VHD compatível com o Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Usando os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Usando o shell de comando do Azure PowerShell ou da CLI do Azure.  Por exemplos, consulte [Quickstart: Crie uma máquina virtual do Windows no Azure com PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [Quickstart: Crie uma máquina virtual Linux com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Esse ponto de extremidade do WinRM também é necessário para executar o kit da ferramenta de certificação para a integração da VM, conforme descrito em [Certificar sua imagem de VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Por outro lado, normalmente, as VMs do Linux são gerenciadas remotamente usando a [CLI do Azure](https://docs.microsoft.com/cli/azure) ou os comandos do Linux em um console SSH.  O Azure também fornece vários métodos alternativos para [executar scripts em sua VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Para cenários mais complexos, há uma série de soluções de integração e automação disponíveis para VMs baseadas no Windows ou no Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurar e implantar com o WinRM

O ponto de extremidade do WinRM de uma VM baseada no Windows pode ser configurado durante dois estágios diferentes de seu desenvolvimento:

- Durante a criação – durante a implantação de uma VM em um VHD existente.  Essa é a abordagem preferencial para novas ofertas.  Essa abordagem exige a criação de um certificado do Azure, usando modelos do Azure Resource Manager fornecidos e executando scripts do PowerShell personalizados.
- Após a implantação – em uma VM existente, hospedada no Azure.  Use essa abordagem se você já tiver uma solução de VM implantada no Azure e precisar habilitar o gerenciamento remoto do Windows para ela.  Essa abordagem requer alterações manuais no portal do Azure e a execução de um script na VM de destino.


## <a name="next-steps"></a>Próximas etapas
Se você estiver criando uma VM, habilite o WinRM durante a [implantação da VM por meio de seus VHDs](./cpp-deploy-vm-vhd.md).  Caso contrário, o WinRM poderá ser habilitado em uma VM existente
