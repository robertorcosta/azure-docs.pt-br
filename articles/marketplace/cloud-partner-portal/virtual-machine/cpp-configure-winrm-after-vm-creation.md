---
title: Configurar o WinRM após a criação da máquina virtual do Azure | Azure Marketplace
description: Explica como configurar o WinRM (Gerenciamento Remoto do Windows) após a criação de uma máquina de virtual hospedada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 7d050b32b212f66623a24bcf87d40111fc5973a5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77481367"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configurar o WinRM após a criação da máquina virtual

Este artigo explica como configurar uma VM (máquina virtual hospedada no Azure) existente para habilitar o WinRM por HTTPS.  Essa configuração aplica-se somente a VMs baseadas no Windows e requer o processo de duas etapas a seguir:

1. Habilite o tráfego da porta para o WinRM pelo protocolo HTTPS.  Defina essa configuração para a VM no portal do Azure.
2. Configure a VM para habilitar o WinRM executando os scripts do PowerShell fornecidos.


## <a name="enabling-port-traffic"></a>Habilitado o tráfego da porta

O protocolo WinRM via HTTPS usa a porta 5986, que não está habilitada por padrão em VMs pré-configuradas do Windows oferecidas no Azure Marketplace. Para habilitar esse protocolo, siga as etapas a seguir para adicionar uma nova regra ao NSG (Grupo de Segurança de Rede) com o [portal do Azure](https://portal.azure.com).  Para obter mais informações sobre os NSGs, confira [Grupos de Segurança](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Navegue até a folha **Máquinas virtuais >**   <*nome-da-vm*>   **> Configurações/Rede**.
2.  Clique no nome do NSG (neste exemplo, **testvm11002**) para exibir suas propriedades:

    ![Propriedades do grupo de segurança de rede](./media/nsg-properties.png)
 
3. Em **Configurações**, selecione **Regras de segurança de entrada** para exibir essa folha.
4. Clique em **+ Adicionar** para criar uma regra chamada `WinRM_HTTPS` para a porta TCP 5986.

    ![Adicionar regra de segurança de rede de entrada](./media/nsg-new-rule.png)

5. Clique em **OK** quando tiver terminado de fornecer valores.  A lista de regras de segurança de entrada deve conter as seguintes novas entradas.

    ![Lista de regras de segurança de rede de entrada](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configurar a VM para habilitar o WinRM 

Siga as seguintes etapas para habilitar e configurar o recurso Gerenciamento Remoto do Windows em sua VM do Windows.   

1. Estabeleça uma conexão de Área de Trabalho Remota com a VM hospedada no Azure.  Para obter mais informações, confira [Como conectar-se e entrar em uma máquina virtual do Azure que executa o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  As etapas restantes serão executadas na VM.
2. Baixe os arquivos a seguir e salve-os em uma pasta na VM:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Abra o **Console do PowerShell** com privilégios elevados (**Executar como Administrador**). 
4. Execute o seguinte comando, fornecendo o parâmetro necessário: o FQDN (nome de domínio totalmente qualificado) da VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Script de configuração do PowerShell 1](./media/powershell-file1.png)

    Esse script depende dos outros dois arquivos que estão na mesma pasta.


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o WinRM, você estará pronto para [implantar sua VM de seus VHDs constituintes](./cpp-deploy-vm-vhd.md).
