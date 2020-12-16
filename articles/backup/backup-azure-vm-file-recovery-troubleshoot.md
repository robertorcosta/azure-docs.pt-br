---
title: Solucionar problemas de recuperação de arquivos de VM do Azure
description: Solucionar problemas ao recuperar arquivos e pastas de um backup de VM do Azure.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605285"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Solucionando problemas na recuperação de arquivos do backup de VM do Azure

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver erros de backup do Azure relacionados a problemas ao recuperar arquivos e pastas de um backup de VM do Azure.

## <a name="common-error-messages"></a>Mensagens de erro comuns

### <a name="exception-caught-while-connecting-to-target"></a>Exceção detectada ao conectar ao destino

**Causa possível**: o script não pode acessar o ponto de recuperação.

**Ação recomendada**: Verifique se o computador atende a todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>O destino já foi conectado por meio de uma sessão iSCSI

**Causa possível**: o script já foi executado no mesmo computador e as unidades foram anexadas.

**Ação recomendada**: os volumes do ponto de recuperação já foram anexados. Eles não podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no explorador de arquivos.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixar um novo script do portal

**Causa possível**: os discos foram desmontados do portal ou o limite de 12 horas foi excedido.

**Ação recomendada**: o script é inválido após 12 horas a partir da hora em que foi baixado e não pode ser executado. Visite o portal e baixe um novo script para continuar com a recuperação de arquivos.

## <a name="troubleshooting-common-scenarios"></a>Solução de problemas de cenários comuns

Esta seção fornece etapas para solucionar problemas que podem surgir durante o download e a execução do script para recuperação de arquivos.

### <a name="cant-download-the-script"></a>Não é possível baixar o script

Ação recomendada:

1. Verifique se você tem todas as [permissões necessárias para baixar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Verifique se há conectividade com os IPs de destino do Azure

Para verificar a conexão, execute um dos comandos a seguir em um prompt de comandos com privilégios elevados.

`nslookup download.microsoft.com`

ou

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>O script é baixado com êxito, mas não é executado

#### <a name="for-sles-12-sp4-os-linux"></a>Para o sistema operacional SLES 12 SP4 (Linux)

**Erro**: iscsi_tcp_module não encontrado

**Possível causa**: ao executar o script Python para ILR (recuperação em nível de item) no SUSE Linux so versão 12sp4, ele falha com o erro **_iscsi_tcp módulo não pode ser carregado_* _.

O módulo ILR usa _ *iscsi_tcp** para estabelecer uma conexão TCP com o serviço de backup. Como parte da versão 12SP4, o SUSE removeu **iscsi_tcp** do pacote Open-iSCSI, de modo que a operação ILR falha.

**Ação recomendada**: a execução do script de recuperação de arquivo não tem suporte em VMs do SUSE 12SP4. Tente a operação de restauração em uma versão mais antiga do SUSE 12SP4.

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>O script é executado, mas a conexão com o destino iSCSI falhou

**Erro**: exceção detectada ao conectar ao destino

1. Verifique se o computador onde o script é executado atende a todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Verifique a conectividade com os IPs de destino do Azure.
Para verificar a conexão, execute um dos comandos a seguir em um prompt de comandos com privilégios elevados.

   `nslookup download.microsoft.com`

   ou

   `ping download.microsoft.com`
1. Verifique se há acesso à porta de saída iSCSI 3260.
1. Verifique se não há firewall ou NSG que esteja bloqueando o tráfego para IPs de destino do Azure ou URLs do serviço de recuperação.
1. Verifique se o antivírus está bloqueando a execução do script.

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>Conectado ao ponto de recuperação, mas os discos não foram anexados

Verifique se você tem o [computador certo para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>Em uma VM do Windows

O **pool de armazenamento na VM é anexado no modo somente leitura**: no Windows 2012 R2 e no Windows 2016 (com pools de armazenamento), ao executar o script pela primeira vez, o pool de armazenamento anexado à VM pode entrar em um estado somente leitura.

Para resolver esse problema, precisamos definir manualmente o Read-Write acesso ao pool de armazenamento pela primeira vez e anexar os discos virtuais. Siga as etapas abaixo:

1. Definir acesso Read-Write.

   Navegue até **Gerenciador do servidor**  >  **arquivos e serviços de armazenamento**  >  **volumes**  >  **pools de armazenamento**.

   ![Armazenamento do Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Na janela **pool de armazenamento** , clique com o botão direito do mouse no pool de armazenamento disponível e selecione a opção **set Read-Write Access** .

   ![Gravação de leitura do armazenamento do Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Depois que o pool de armazenamento for atribuído com acesso de leitura/gravação, anexe o disco virtual.

   Navegue até **Gerenciador do servidor interface do usuário**. Na seção **discos virtuais** > clique com o botão direito do mouse para selecionar a opção **anexar disco virtual** .

   ![Disco virtual do Gerenciador do servidor](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>Em uma VM Linux

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>A recuperação de arquivo falha na montagem automática porque o disco não contém volumes

Ao executar a recuperação de arquivos, o serviço de backup detecta volumes e montagens automáticas. No entanto, se os discos de backup tiverem partições brutas, esses discos não serão montados de forma automática e você não poderá ver o disco de dados para recuperação.

Para resolver esse problema, siga as etapas documentadas neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>O sistema operacional não pôde identificar o sistema de arquivos que está causando a falha na recuperação de arquivos do Linux durante a montagem de discos

Ao executar o script de recuperação de arquivo, o disco de dados falhou ao anexar com o seguinte erro:

"Falha na montagem das seguintes partições, pois o sistema operacional não pôde identificar o sistema de arquivos"

Para resolver esse problema, verifique se o volume está criptografado com um aplicativo de terceiros. Se ele estiver criptografado, o disco ou a VM não aparecerá como criptografado no Portal.

1. Entre na VM de backup e execute o comando:

   `*lsblk -f*`

   ![Disco sem volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verifique o sistema de arquivos e a criptografia.
3. Se o volume for criptografado, a recuperação de arquivo não terá suporte. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Os discos estão anexados, mas não é possível montar volumes

- Verifique se você tem o [computador certo para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

#### <a name="on-windows-vms"></a>Em VMs do Windows

Ao executar o script de recuperação de arquivo para Windows, há uma mensagem que ***0 volumes são anexados** _. No entanto, os discos são descobertos no console de gerenciamento de disco. Ao anexar volumes por meio de iSCSI, alguns volumes que foram detectados vão para um estado offline. Quando o canal iSCSI se comunica entre a VM e o serviço, ele detecta esses volumes e os coloca online, mas eles não são montados.

   ![Disco não anexado](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Para identificar e resolver esse problema, execute as seguintes etapas:

1. Vá para _ *Gerenciamento de disco** executando o comando **diskmgmt** na janela cmd.
1. Verifique se discos adicionais são exibidos. No exemplo a seguir, o disco 2 é um disco adicional.

   ![Management0 de disco](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Clique com o botão direito do mouse no **novo volume** e selecione **alterar a letra da unidade e os caminhos**.

   ![Management1 de disco](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Na janela **Adicionar letra da unidade ou caminho** , selecione **atribuir a letra da unidade a seguir** e atribua uma unidade disponível e selecione **OK**.

   ![Management2 de disco](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. No explorador de arquivos, exiba a letra da unidade escolhida e explore os arquivos.

#### <a name="on-linux-vms"></a>Em VMs do Linux

- Verifique se você tem o [computador certo para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Se a VM do Linux protegida usar LVM ou matrizes RAID, siga as etapas listadas neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="cant-copy-the-files-from-mounted-volumes"></a>Não é possível copiar os arquivos dos volumes montados

A cópia pode falhar com o erro **0x80070780: o arquivo não pode ser acessado pelo sistema.** Nesse caso, verifique se o servidor de origem tem eliminação de duplicação de disco habilitada. Em seguida, certifique-se de que o servidor de restauração também tenha a eliminação de duplicação habilitada nas unidades. Você pode deixar a função de eliminação de duplicação desconfigurada para não duplicar as unidades no servidor de restauração.

## <a name="next-steps"></a>Próximas etapas

- [Recuperar arquivos e pastas do backup de máquina virtual do Azure](backup-azure-restore-files-from-vm.md)
