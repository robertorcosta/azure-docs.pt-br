---
title: Habilitar o Azure Disk Encryption para VMs do Linux
description: Este artigo fornece instruções sobre como ativar a criptografia de disco do Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff1b37c3053ffa91dcb432cd97a7dd6fd71dad1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250420"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Criptografia de disco azure para VMs Linux 

O Azure Disk Encryption ajuda a proteger seus dados e a atender aos compromissos de conformidade e segurança de sua organização. Ele usa o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o Sistema Operacional e discos de dados de máquinas virtuais (VMs) do Azure, e é integrado com [o Azure Key Vault](../../key-vault/index.yml) para ajudá-lo a controlar e gerenciar as chaves e segredos de criptografia de disco. 

Se você usar [o Azure Security Center,](../../security-center/index.yml)você será alertado se você tiver VMs que não estão criptografadas. Esses alertas são mostrados como Alta Gravidade e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco na Central de Segurança do Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se você já usou a Criptografia de Disco Do Azure com o Azure AD para criptografar uma VM, você deve continuar a usar essa opção para criptografar sua VM. Consulte [Azure Disk Encryption com Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter detalhes. 
> - Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição. Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.
> - Atualmente, as VMs da Geração 2 não suportam criptografia de disco Azure. Consulte [suporte para VMs de geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) para obter detalhes.

Você pode aprender os fundamentos do Azure Disk Encryption para Linux em apenas alguns minutos com o [Create e criptografar um VM Linux com o Azure CLI quickstart](disk-encryption-cli-quickstart.md) ou o [Create e criptografar um Linux VM com o Azure Powershell quickstart](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operacionais suportados

### <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

Os VMs Linux estão disponíveis em uma [variedade de tamanhos](sizes.md). A Criptografia de disco Do Zure não está disponível em [VMs básicas, série A,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não atendam a esses requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs Linux ao criptografar apenas volumes de dados| 2 GB |
| VMs Linux ao criptografar os volumes de dados e sO, e onde o uso do sistema de arquivos raiz (/) é de 4GB ou menos | 8 GB |
| VMs Linux ao criptografar os volumes de dados e sO, e onde o uso do sistema de arquivos raiz (/) é maior que 4GB | O uso do sistema de arquivos raiz * 2. Por exemplo, um uso de 16 GB de sistema de arquivos raiz requer pelo menos 32GB de RAM |

Uma vez que o processo de criptografia de disco do SO esteja completo em máquinas virtuais Linux, a VM pode ser configurada para ser executada com menos memória. 

A Azure Disk Encryption também está disponível para VMs com armazenamento premium. 

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

O Azure Disk Encryption é suportado em um subconjunto das [distribuições Linux endossadas pelo Azure,](endorsed-distros.md)que é em si um subconjunto de todas as distribuições possíveis do servidor Linux.

![Diagrama venn de distribuições de servidores Linux que suportam criptografia de disco Azure](./media/disk-encryption/ade-supported-distros.png)

As distribuições de servidores Linux que não são endossadas pelo Azure não suportam criptografia de disco do Azure; das que são endossadas, apenas as seguintes distribuições e versões suportam a Criptografia de Disco do Azure:

| Distribuição do Linux | Versão | Tipo de volume suportado para criptografia|
| --- | --- |--- |
| Ubuntu | 18.04| SO e disco de dados |
| Ubuntu | 16.04| SO e disco de dados |
| Ubuntu | 14.04.5</br>[com kernel ajustado para Azure atualizado para 4.15 ou posterior](disk-encryption-troubleshooting.md) | SO e disco de dados |
| RHEL | 7.7 | Sistema operacional e disco de dados (veja nota abaixo) |
| RHEL | 7.6 | Sistema operacional e disco de dados (veja nota abaixo) |
| RHEL | 7.5 | Sistema operacional e disco de dados (veja nota abaixo) |
| RHEL | 7.4 | Sistema operacional e disco de dados (veja nota abaixo) |
| RHEL | 7.3 | Sistema operacional e disco de dados (veja nota abaixo) |
| RHEL | 7.2 | Sistema operacional e disco de dados (veja nota abaixo) |
| RHEL | 6,8 | Disco de dados (veja nota abaixo) |
| RHEL | 6.7 | Disco de dados (veja nota abaixo) |
| CentOS | 7.7 | SO e disco de dados |
| CentOS | 7.6 | SO e disco de dados |
| CentOS | 7.5 | SO e disco de dados |
| CentOS | 7.4 | SO e disco de dados |
| CentOS | 7.3 | SO e disco de dados |
| CentOS | 7.2n | SO e disco de dados |
| CentOS | 6,8 | Disco de dados |
| openSUSE | 42.3 | Disco de dados |
| SLES | 12-SP4 | Disco de dados |
| SLES | 12-SP3 | Disco de dados |

> [!NOTE]
> A nova implementação de criptografia de disco Do Azure é suportada para rhel os e disco de dados para imagens RHEL7 Pay-As-You-Go.  
>
> O ADE também é suportado para RHEL Bring-Your-Own-Subscription Gold Images, mas somente **após** o registro da assinatura . Para obter mais informações, consulte [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisitos adicionais de VM

A Criptografia de Disco Do Zure requer que os módulos dm-crypt e vfat estejam presentes no sistema. A remoção ou desativação do vfat da imagem padrão impedirá que o sistema leia o volume da chave e obtenha a chave necessária para desbloquear os discos em reinicializações subseqüentes. As etapas de endurecimento do sistema que removem o módulo vfat do sistema não são compatíveis com a Criptografia de Disco Do Azure. 

Antes de habilitar a criptografia, os discos de dados a serem criptografados devem ser devidamente listados em /etc/fstab. Use um nome de dispositivo de bloco persistente para essa entrada, pois nomes de dispositivo no formato "/dev/sdX" não podem ser considerados para serem associados com o mesmo disco entre as reinicializações, principalmente depois que a criptografia é aplicada. Para obter mais detalhes sobre esse comportamento, consulte: [alterações de nome de dispositivo de solucionar problemas de VM do Linux](troubleshoot-device-names-problems.md)

Verifique se as configurações de /etc/fstab estão definidas corretamente para a montagem. Para definir essas configurações, execute o comando mount - a ou reinicie a VM e disparar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar se a unidade ainda está montada. 
- Se o arquivo /etc/fstab não montar a unidade corretamente antes de habilitar a criptografia, o Azure Disk Encryption não será capaz de montá-la corretamente.
- O processo de criptografia de disco do Azure se moverá as informações de montagem fora /etc/fstab e em seu próprio arquivo de configuração como parte do processo de criptografia. Não se assuste para ver a entrada ausente do /etc/fstab após a criptografia de unidade de dados é concluída.
- Antes de iniciar a criptografia, certifique-se de interromper todos os serviços e processos que possam estar sendo gravados em discos de dados montados e desativá-los, para que eles não sejam reiniciados automaticamente após uma reinicialização. Estes poderiam manter os arquivos abertos nessas partições, impedindo que o procedimento de criptografia os remonte, causando falha na criptografia. 
- Após a reinicialização, ela levará tempo para o processo de criptografia de disco do Azure montar os discos criptografados recentemente. Eles não estarão disponíveis imediatamente após uma reinicialização. O processo precisa de tempo para iniciar, desbloquear e, em seguida, montar as unidades criptografadas antes de estar disponível para ser acessado por outros processos. Esse processo pode levar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar as entradas necessárias /etc/fstab pode ser encontrado no [pré-requisito sinuoso de criptografia de disco do Azure (linhas](https://github.com/ejarvi/ade-cli-getting-started) 244-248) e no [pré-requisito powerShell de criptografia de disco do Azure.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts) 

## <a name="networking-requirements"></a>Requisitos de rede

Para habilitar o recurso Azure Disk Encryption, as VMs Linux devem atender aos seguintes requisitos de configuração de ponto final de rede:
  - Para obter um token para se conectar ao seu cofre principal, o Linux VM deve \[\]ser capaz de se conectar a um ponto final do Azure Active Directory, login.microsoftonline.com .
  - Para escrever as chaves de criptografia do cofre principal, o Linux VM deve ser capaz de se conectar ao ponto final do cofre chave.
  - O Linux VM deve ser capaz de se conectar a um ponto final de armazenamento do Azure que hospeda o repositório de extensão Azure e uma conta de armazenamento Azure que hospeda os arquivos VHD.
  -  Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de criptografia  

A Criptografia de disco do Azure requer um Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. Seu cofre-chave e VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [Criar e configurar um cofre de chaves para criptografia de disco Do Zure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Cofre de Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [a criação e configuração de um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md). |
| CLI do Azure | [O Azure CLI](/cli/azure/install-azure-cli) é otimizado para gerenciar e administrar os recursos do Azure a partir da linha de comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de criptografia de disco transparente baseado em Linux que é usado para permitir a criptografia de disco em VMs Linux. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou envolver o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [a criação e configuração de um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, confira [cmdlets do Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Próximas etapas

- [Quickstart - Crie e criptografe um VM Linux com a Cli do Azure](disk-encryption-cli-quickstart.md)
- [Quickstart - Crie e criptografe um VM Linux com o Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Cenários de Azure Disk Encryption em VMs Linux](disk-encryption-linux.md)
- [Pré-requisitos de criptografia de disco azure cli script](https://github.com/ejarvi/ade-cli-getting-started)
- [Pré-requisitos de criptografia de disco do Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)


