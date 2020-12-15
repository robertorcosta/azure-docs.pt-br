---
title: Solucionar problemas de recuperação de arquivos de VM do Azure
description: Solucionar problemas específicos da recuperação de arquivos e pastas de um backup de VM do Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513272"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Solucionando problemas na recuperação de arquivos do backup de VM do Azure

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver erros de backup do Azure relacionados a problemas específicos à recuperação de arquivos e pastas de um backup de VM do Azure. 

## <a name="exception-caught-while-connecting-to-target"></a>Exceção detectada ao conectar ao destino

Possível causa: o script não pode acessar a ação recomendada do ponto de recuperação: Verifique se o computador atende a todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>O destino já foi conectado por meio de uma sessão iSCSI

Causa possível: o script já foi executado no mesmo computador e as unidades foram anexadas.
Ação recomendada: os volumes do ponto de recuperação já foram anexados. Eles não podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no explorador de arquivos.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixar um novo script do portal

Causa possível: os discos foram desmontados do portal ou o limite de 12 horas foi excedido.
Ação recomendada: o script é inválido após 12 horas a partir da hora em que foi baixado e não pode ser executado. Visite o portal e baixe um novo script para prosseguir com a recuperação de arquivos.

## <a name="troubleshooting-common-issues"></a>Solução de problemas comuns

Esta seção fornece etapas para solucionar problemas encontrados durante o download e a execução do script para recuperação de arquivos.

## <a name="cannot-download-the-script"></a>Não é possível baixar o script

Ação recomendada:

1. Verifique se você tem todas as [permissões necessárias para baixar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Verifique se há conectividade com os IPs de destino do Azure.
Para verificar a conexão, execute o comando a seguir em um prompt de comando elevado. 
    - *nslookup download.Microsoft.com* ou
    - *download.microsoft.com ping*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>O script é baixado com êxito, mas não é executado

### <a name="for-sles-12-sp4-os-linux"></a>Para o sistema operacional SLES 12 SP4 (Linux)

Erro: iscsi_tcp_module não encontrado

Causa possível: ao executar o script Python para a recuperação em nível de item no SUSE Linux so versão 12sp4, ele falha com um erro ***iscsi_tcp módulo não pode ser carregado** _. O módulo ILR usa ' iscsi_tcp ' para estabelecer a conexão TCP com o serviço de backup, SUSE como parte da versão 12SP4 removida iscsi_tcp do pacote Open-iSCSI, portanto, a operação ILR falhará.

Ação recomendada: a execução do script de recuperação de arquivo não é suportada em VMs do SUSE 12SP4, tente a operação de restauração em uma versão mais antiga do SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>O script é executado, mas a conexão com o destino iSCSI falhou

Erro: exceção detectada ao conectar ao destino

1. Verifique se o computador onde o script é executado atende a todos os [requisitos de acesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Verifique a conectividade com os IP (s) de destino do Azure.
Para verificar a conexão, execute o comando a seguir em um prompt de comando elevado. 
    - _nslookup download.microsoft.com * ou<br>
    - *download.microsoft.com ping*
3. Verifique se há acesso à porta de saída iSCSI 3260.
4. Verifique se não há firewall/NSG bloqueando o tráfego para as URLs de serviço de recuperação ou IP de destino do Azure.
5. Verifique se o antivírus está bloqueando a execução do script.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Conectado ao ponto de recuperação, mas os discos não foram anexados

Verifique se você tem o [computador certo para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>Na VM do Windows

O **pool de armazenamento na VM é anexado no modo somente leitura** Para o Windows 2012 R2 e o Windows 2016 (com o pool de armazenamento), ao executar o script pela primeira vez, o pool de armazenamento anexado à VM pode entrar no estado somente leitura.

Para resolver esse problema, precisamos definir manualmente o Read-Write acesso ao pool de armazenamento pela primeira vez e anexar os discos virtuais, seguindo as etapas abaixo:

1. Definir acesso Read-Write.
Navegue até Gerenciador do Servidor > serviços de arquivo e armazenamento > volumes > pools de armazenamento.

   ![Armazenamento do Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. Na janela **pool de armazenamento** , clique com o botão direito do mouse no pool de armazenamento disponível e selecione a opção **definir Read-Write acesso** .

   ![Gravação de leitura do armazenamento do Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Depois que o pool de armazenamento é atribuído com acesso de leitura/gravação, precisamos anexar o disco virtual.
Navegue até **Gerenciador do servidor interface do usuário**, na seção discos virtuais > clique com o botão direito do mouse para selecionar a opção **anexar disco virtual** .

   ![Disco virtual do Gerenciador do servidor](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Na VM do Linux

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>A recuperação de arquivo falha na montagem automática porque o disco não contém volumes

Ao executar a recuperação de arquivos, o serviço de backup detecta volumes e montagens automáticas. No entanto, se os discos com backup tiverem partições brutas, esses discos não serão montados automaticamente e não será possível ver o disco de dados para recuperação.

Para resolver esse problema, siga as etapas documentadas neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>O sistema operacional não pôde identificar o sistema de arquivos que está causando a falha na recuperação de arquivos do Linux durante a montagem de discos

A execução do disco de dados do script de recuperação de arquivo falhou ao anexar com o erro abaixo: *as partições a seguir falharam ao montar, pois o sistema operacional não pôde identificar o sistema de arquivos*

- Para resolver esse problema, verifique se o volume está criptografado com um aplicativo de terceiros. Se criptografado, o disco ou a VM não aparecerá como criptografado no Portal.
1. Faça logon na VM de backup e execute o comando: *lsblk-f*<br>

   ![Disco sem volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verifique o sistema de arquivos e a criptografia.
3. Se o volume for criptografado, a recuperação de arquivo não terá suporte. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Os discos estão anexados, mas não é possível montar volumes

- Verifique se você tem o [computador certo para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Na VM do Windows

Ao executar o script de recuperação de arquivo para o Windows, há uma mensagem que ***0 volumes são anexados** _, mas os discos são descobertos no console de gerenciamento de disco. Ao anexar volumes por meio de iSCSI, alguns volumes que são detectados vão para o estado offline. Quando o canal iSCSI se comunica entre a VM e o serviço, ele detecta esses volumes e os coloca online, mas eles não são montados.

   ![Disco não anexado](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Para identificar e resolver esse problema, execute as seguintes etapas:

1. Vá para _ *Gerenciamento de disco** executando o comando **diskmgmt** na janela cmd.
2. Verifique se discos adicionais são exibidos. No exemplo abaixo, o disco 2 é um disco adicional.

   ![Management0 de disco](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Clique com o botão direito do mouse no **volume** e selecione **alterar a letra da unidade e os caminhos**.

   ![Management1 de disco](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. Na janela **Adicionar letra da unidade ou caminho** , selecione **atribuir a seguinte letra de unidade** e atribua uma unidade disponível e clique em **OK**. 

   ![Management2 de disco](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. No explorador de arquivos, você pode exibir a unidade.

   ![Management3 de disco](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. Os novos volumes devem ser anexados.  

   ![Disco não montado](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. No explorador de arquivos, os novos volumes ficarão visíveis após a reexecução do script.

   ![Disco não mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Disco não mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Em VMs do Linux 

- Verifique se você tem o [computador certo para executar o script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Se a VM do Linux protegida usar o LVM e/ou matrizes RAID, siga as etapas listadas neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Não é possível copiar os arquivos de volumes montados

A cópia pode falhar com um erro 0x80070780: o arquivo não pode ser acessado pelo sistema. Nesse caso, verifique se o servidor de origem tem eliminação de duplicação de disco habilitada. Em seguida, certifique-se de que o servidor de restauração também tenha a eliminação de duplicação habilitada nas unidades. Você pode deixar a função de eliminação de duplicação desconfigurada para não duplicar as unidades no servidor de restauração.
