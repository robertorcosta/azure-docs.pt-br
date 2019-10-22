---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35814de74fa03f9969cdd48882a5f672cfe306a1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67171783"
---
Você pode se conectar a uma VM que é implantada em sua VNet criando um Conexão de Área de Trabalho Remota para sua VM. A melhor maneira de verificar inicialmente se você pode se conectar à sua VM é conectar-se usando seu endereço IP privado, em vez de nome do computador. Dessa forma, você está testando para ver se você pode se conectar, não se a resolução de nomes está configurada corretamente.

1. Localize o endereço IP privado. Você pode encontrar o endereço IP privado de uma VM examinando as propriedades da VM na portal do Azure ou usando o PowerShell.

   - Portal do Azure-localize sua máquina virtual na portal do Azure. Exiba as propriedades da VM. O endereço IP privado é listado.

   - PowerShell-use o exemplo para exibir uma lista de VMs e endereços IP privados de seus grupos de recursos. Você não precisa modificar este exemplo antes de usá-lo.

     ```powershell
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Verifique se você está conectado à sua VNet usando a conexão VPN ponto a site.
3. Abra **conexão de área de trabalho remota** digitando "RDP" ou "conexão de área de trabalho remota" na caixa de pesquisa na barra de tarefas e, em seguida, selecione conexão de área de trabalho remota. Você também pode abrir Conexão de Área de Trabalho Remota usando o comando ' mstsc ' no PowerShell. 
4. Em Conexão de Área de Trabalho Remota, insira o endereço IP privado da VM. Você pode clicar em "Mostrar opções" para ajustar as configurações adicionais e, em seguida, conectar.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para solucionar problemas de conexão RDP com uma VM

Se você estiver tendo problemas para se conectar a uma máquina virtual em sua conexão VPN, verifique o seguinte:

- Verifique se a conexão VPN foi bem-sucedida.
- Verifique se você está se conectando ao endereço IP privado da VM.
- Use ' ipconfig ' para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual você está se conectando. Se o endereço IP estiver dentro do intervalo de endereços da VNet à qual você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso será chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure, permanece na rede local.
- Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como a resolução de nomes funciona para VMs, consulte [resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Verifique se o pacote de configuração do cliente VPN foi gerado depois que os endereços IP do servidor DNS foram especificados para a VNet. Se você atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.
- Para obter mais informações sobre conexões RDP, consulte [solucionar problemas de conexões área de trabalho remota a uma VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).