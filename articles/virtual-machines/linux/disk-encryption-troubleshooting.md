---
title: Solução de problemas criptografia de disco do Azure para VMs Linux
description: Este artigo fornece dicas de solução de problemas para o Microsoft Azure Disk Encryption para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970467"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de solução de problemas do Azure Disk Encryption

Este guia destina-se a profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam o Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

Antes de tomar qualquer uma das etapas abaixo, primeiro certifique-se de que as VMs que você está tentando criptografar estão entre os [tamanhos e sistemas operacionais de VM suportados,](disk-encryption-overview.md#supported-vms-and-operating-systems)e que você cumpriu todos os pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solução de problemas de criptografia de disco do SO Linux

A criptografia de disco do SO (sistema operacional) Linux deve desmontar a unidade do SO antes de executá-lo por meio do processo de criptografia de disco completo. Se não for possível desmontar a unidade, é provável que ocorra uma mensagem de erro de "falha ao desmontar após ...".

Esse erro pode ocorrer quando a criptografia de disco do SISTEMA OPERACIONAL é tentada em uma VM com um ambiente que foi alterado da imagem da galeria de estoque suportada. Desvios da imagem compatível podem interferir com a capacidade da extensão de desmontar a unidade do SO. Exemplos de desvios podem incluir os seguintes itens:
- Imagens personalizadas que não correspondem mais a um sistema de arquivos ou esquema de particionamento suportado.
- Não há suporte para aplicativos grandes, como SAP, MongoDB, Apache Cassandra e Docker, quando eles estão instalados e executados no sistema operacional antes da criptografia. A Criptografia de Disco do Azure não consegue encerrar esses processos com segurança, conforme necessário na preparação da unidade do sistema operacional para criptografia de disco. Se ainda houver processos ativos mantendo alças de arquivos abertos na unidade do sistema operacional, a unidade do sistema operacional não poderá ser desmontada, resultando em uma falha na criptografia da unidade do sistema operacional. 
- Scripts personalizados que são executados próximos à hora de encerramento da criptografia que está sendo habilitada ou se qualquer outra alteração estiver sendo feita na VM durante o processo de criptografia. Esse conflito pode acontecer quando um modelo do Azure Resource Manager define múltiplas extensões para executar simultaneamente ou quando uma extensão de script personalizada ou outra ação é executada simultaneamente com a criptografia de disco. Serializar e isolar tais etapas pode resolver o problema.
- O SELinux (Security Enhanced Linux) não foi desabilitado antes da habilitação da criptografia, fazendo com que a etapa de desmontagem falhe. O SELinux pode ser reabilitado após a conclusão da criptografia.
- O disco do SO usa um esquema LVM (Gerenciador de Volume Lógico). Embora o suporte ao disco de dados LVM limitado esteja disponível, um disco de SO LVM, não.
- Os requisitos mínimos de memória não são atendidos (7 GB é sugerido para criptografia de disco do SO).
- As unidades de dados são montadas recursivamente no diretório /mnt/ ou uma na outra (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a> Atualizar o kernel padrão para o Ubuntu 14.04 LTS

A imagem do Ubuntu 14.04 LTS é fornecida com uma versão padrão do kernel de 4.4. Esta versão do kernel tem um problema conhecido no qual o Encerrador de Memória Insuficiente encerra incorretamente o comando dd durante o processo de criptografia do sistema operacional. Esse bug foi corrigido no mais recente kernel do Linux ajustado para o Azure. Para evitar esse erro, antes de habilitar a criptografia na imagem, atualize para o [kernel ajustado para o Azure 4.15](https://packages.ubuntu.com/trusty/linux-azure) ou posterior usando os comandos a seguir:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Depois de reiniciar a VM para o novo kernel, a nova versão do kernel pode ser confirmada usando:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Atualize as versões de aplicativo e extensão do Azure Virtual Machine Agent

As operações de criptografia de disco do Azure podem falhar em imagens de máquinas virtuais usando versões não suportadas do Azure Virtual Machine Agent. As imagens linux com versões de agente anteriores a 2.2.38 devem ser atualizadas antes de habilitar a criptografia. Para obter mais informações, consulte [Como atualizar o Azure Linux Agent em uma](../extensions/update-linux-agent.md) versão VM e suporte à versão mínima para agentes de [máquinas virtuais no Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

A versão correta da extensão de agente convidado Microsoft.Azure.Security.AzureDiskEncryption ou Microsoft.Azure.Security.AzureDiskEncryptionForLinux também é necessária. As versões de extensão são mantidas e atualizadas automaticamente pela plataforma quando os pré-requisitos do agente azure Virtual Machine são satisfeitos e uma versão suportada do agente de máquina virtual é usada.

A extensão Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux foi depreciada e não é mais suportada.  

## <a name="unable-to-encrypt-linux-disks"></a>Não é possível criptografar discos do Linux

Em alguns casos, a criptografia de disco do Linux parece estar paralisada na "criptografia de disco do OS iniciada" e o SSH está desabilitado. O processo de criptografia pode levar entre 3 e 16 horas para ser concluído em uma imagem da galeria de estoque. Se discos de dados com diversos terabytes forem adicionados, o processo poderá levar dias.

A sequência de criptografia de disco do SO Linux desmonta a unidade do SO temporariamente. Em seguida, ela realiza a criptografia bloco a bloco de todo o disco do SO, antes de remontá-lo em seu estado criptografado. O Linux Disk Encryption não permite o uso simultâneo da VM enquanto a criptografia estiver em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para concluir a criptografia. Essas características incluem o tamanho do disco e se a conta de armazenamento é padrão ou premium (SSD).

Para verificar o status da criptografia, consulte o campo **ProgressMessage** retornado do comando [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Enquanto a unidade do SO está sendo criptografada, a VM entra em um estado de manutenção e desabilita o SSH para evitar qualquer interrupção no processo em andamento. A mensagem **EncryptionInProgress** será reportada a maior parte do tempo enquanto a criptografia estiver em andamento. Horas depois, uma mensagem **VMRestartPending** solicitará a reinicialização da VM. Por exemplo: 


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Depois que você for solicitado a reinicializar a VM e após a reinicialização da VM, deverá aguardar de 2 a 3 minutos pela reinicialização e pelas etapas finais a serem executadas no destino. A mensagem do status muda quando a criptografia é concluída. Depois que essa mensagem estiver disponível, espera-se que a unidade do SO criptografada esteja pronta para uso e que a VM esteja pronta para ser usada novamente.

Nos seguintes casos, recomendamos restaurar a VM de volta para o instantâneo ou backup feito logo antes da criptografia:
   - Se a sequência de reinicialização descrita anteriormente não acontecer.
   - Se as informações de inicialização, a mensagem de progresso ou outros indicadores de erro reportarem que a criptografia do SO falhou no meio desse processo. Um exemplo de mensagem é o erro "falha ao desmontar", que é descrito neste guia.

Antes da próxima tentativa, reavaliar as características da VM e garantir que todos os pré-requisitos sejam atendidos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solucionando problemas do Azure Disk Encryption por trás de um firewall

Consulte [criptografia de disco em uma rede isolada](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Solução de problemas de status de criptografia 

O portal pode exibir um disco como criptografado mesmo depois de não ter sido criptografado dentro da VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco de dentro da VM, em vez de usar os comandos de gerenciamento de criptografia de disco Azure de nível mais alto.  Os comandos de nível mais alto não apenas descriptografam o disco de dentro da VM, mas fora da VM eles também atualizam importantes configurações de criptografia de nível de plataforma e configurações de extensão associadas à VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de relatar o status da criptografia ou provisionar a VM corretamente.   

Para desativar a criptografia de disco do Azure com o PowerShell, use [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido de [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executando remove-AzVMDiskEncryptionExtensão antes que a criptografia seja desativada falhará.

Para desativar a criptografia de disco do Azure com cli, use [a criptografia az vm desativada](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucioná-los. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
