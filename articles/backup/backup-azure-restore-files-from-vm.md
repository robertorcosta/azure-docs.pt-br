---
title: Recuperar arquivos e pastas de um backup de VM do Azure
description: Este artigo explica como recuperar arquivos e pastas de um ponto de recuperação de máquina virtual do Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: fd68c33e4425d717837923b90119d42569a1f003
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89178513"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar arquivos de um backup de máquina virtual do Azure

O Backup do Azure fornece a capacidade de restaurar [máquinas virtuais (VMs) do Azure e discos](./backup-azure-arm-restore-vms.md) de backups de máquina virtual do Azure, também conhecido como pontos de recuperação. Este artigo explica como recuperar arquivos e pastas de um backup de VM do Azure. A restauração de arquivos e pastas está disponível somente para VMs do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre dos serviços de recuperação.

> [!NOTE]
> Esse recurso está disponível para máquinas virtuais do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre dos Serviços de Recuperação.
> Não há suporte para a recuperação de arquivo de um backup de VM criptografado.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copiar arquivos

Para restaurar arquivos ou pastas no ponto de recuperação, vá para a máquina virtual e escolha o ponto de recuperação desejado.

1. Entre no [portal do Azure](https://portal.Azure.com) e no painel esquerdo, selecione **máquinas virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o painel da máquina virtual.

2. No menu da máquina virtual, selecione **backup** para abrir o painel de backup.

    ![Abra o item de backup do cofre de Serviços de Recuperação](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu painel de backup, selecione **recuperação de arquivo**.

    ![Selecionar recuperação de arquivo](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O menu **Recuperação de Arquivo** é aberto.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. No menu suspenso **Selecionar ponto de recuperação**, selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

5. Para baixar o software usado para copiar arquivos do ponto de recuperação, selecione **baixar executável** (para VMs do Windows Azure) ou **baixar script** (para VMs do Linux Azure, um script Python é gerado).

    ![Baixar executável](./media/backup-azure-restore-files-from-vm/download-executable.png)

    O Azure baixa o arquivo executável ou o script para o computador local.

    ![baixar mensagem para o arquivo executável ou o script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o arquivo executável ou o script como um administrador, recomendamos salvar o arquivo baixado no seu computador.

6. O arquivo executável ou o script é protegido por senha e requer uma senha. No menu **recuperação de arquivo** , selecione o botão Copiar para carregar a senha na memória.

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Verifique se [você tem o computador certo](#selecting-the-right-machine-to-run-the-script) para executar o script. Se o computador certo for o mesmo computador em que você baixou o script, você poderá continuar para a seção de download. No local do download (geralmente a pasta *Downloads*), clique com o botão direito do mouse no arquivo executável ou no script e execute-o com as credenciais de Administrador. Quando solicitado, digite a senha ou cole a senha a partir da memória e pressione **Enter**. Quando a senha válida é inserida, o script conecta-se ao ponto de recuperação.

    ![Saída executável](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Para computadores Linux, um script Python é gerado. É necessário baixar o script e copiá-lo para o servidor Linux relevante/compatível. Talvez seja necessário modificar as permissões para executá-lo com ```chmod +x <python file name>```. Em seguida, execute o arquivo Python com ```./<python file name>```.

Consulte a seção [Requisitos de acesso](#access-requirements) para verificar se o script foi executado com êxito.

### <a name="identifying-volumes"></a>Identificação de volumes

#### <a name="for-windows"></a>Para Windows

Quando você executa o arquivo executável, o sistema operacional monta os novos volumes e atribui letras de unidade. Você pode usar o Windows Explorer ou o Explorador de arquivos para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original. No entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original fosse “Disco de Dados (E:`\`)”, esse volume poderia ser anexado no computador local como “Disco de Dados (“Qualquer letra”:`\`). Busque em todos os volumes mencionados na saída do script até encontrar seus arquivos ou suas pastas.  

   ![Volumes de recuperação anexados](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

No Linux, os volumes do ponto de recuperação são montados para a pasta onde o script é executado. Os discos anexados, volumes e os caminhos de montagem correspondente são mostrados adequadamente. Esses caminhos de montagem ficam visíveis aos usuários que têm acesso ao nível raiz. Percorra os volumes mencionados na saída do script.

  ![Menu de recuperação de Arquivo do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Encerrando a conexão

Depois de identificar os arquivos e copiá-los para um local de armazenamento local, remova ou desmonte as unidades adicionais. Para desmontar as unidades, no menu **recuperação de arquivos** na portal do Azure, selecione **desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Quando os discos tiverem sido desmontados, você receberá uma mensagem. Pode levar alguns minutos até que a conexão seja atualizada para que você possa remover os discos.

No Linux, após a conexão com o ponto de recuperação ser interrompida, o sistema operacional não removerá automaticamente os caminhos de montagem correspondentes. Os caminhos de montagem existem como volumes "órfãos" e são visíveis, mas geram um erro ao acessar/gravar os arquivos. Eles podem ser removidos manualmente. O script, quando executado, identifica esses volumes existentes em qualquer ponto de recuperação anterior e limpa mediante consentimento.

> [!NOTE]
> Certifique-se de que a conexão seja fechada depois que os arquivos necessários forem restaurados. Isso é importante, especialmente no cenário em que o computador no qual o script é executado também é configurado para backup. Se a conexão ainda estiver aberta, o backup subsequente poderá falhar com o erro "UserErrorUnableToOpenMount". Isso acontece porque os volumes/unidades montadas devem estar disponíveis e, quando acessados, eles podem falhar porque o armazenamento subjacente, ou seja, o servidor de destino iSCSI pode não estar disponível. A limpeza da conexão removerá essas unidades/volumes e, portanto, elas não estarão disponíveis durante o backup.

## <a name="selecting-the-right-machine-to-run-the-script"></a>Seleção do computador certo para executar o script

Se o script for baixado com êxito, a próxima etapa será verificar se o computador no qual você planeja executar o script é o computador certo. A seguir estão os requisitos a serem atendidos no computador.

### <a name="original-backed-up-machine-versus-another-machine"></a>Computador de backup original versus outro computador

1. Se o computador com backup for uma VM de disco grande, ou seja, o número de discos for maior que 16 ou cada disco tiver mais que 4 TB, o script **deverá ser executado em outro computador** e [esses requisitos](#file-recovery-from-virtual-machine-backups-having-large-disks) precisarão ser atendidos.
1. Mesmo que o computador com backup não seja uma VM de disco grande, [nesses cenários](#special-configurations), o script não pode ser executado na mesma VM com backup.

### <a name="os-requirements-on-the-machine"></a>Requisitos de sistema operacional no computador

O computador em que o script precisa ser executado deve atender a [esses requisitos de sistema operacional](#system-requirements).

### <a name="access-requirements-for-the-machine"></a>Requisitos de acesso para o computador

O computador em que o script precisa ser executado deve atender [esses requisitos de acesso](#access-requirements).

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos dinâmicos

Se a VM do Azure protegida tiver volumes com uma ou ambas as seguintes características, não será possível executar o script executável na mesma VM.

- Volumes que abrangem vários discos (volumes distribuídos e estendidos)
- Volumes tolerantes a falhas (volumes RAID-5 e espelhados) em discos dinâmicos

Em vez disso, execute o script executável em qualquer outro computador com um sistema operacional compatível.

### <a name="windows-storage-spaces"></a>Espaços de Armazenamento do Windows

Os Espaços de Armazenamento do Windows é uma tecnologia Windows que permite virtualizar o armazenamento. Com os Espaços de Armazenamento do Windows é possível agrupar discos padrão do setor em pools de armazenamento. Dessa forma, é possível utilizar o espaço disponível nesses pools de armazenamento para criar discos virtuais, chamados espaços de armazenamento.

Se a VM do Azure protegida utilizar os Espaços de Armazenamento do Windows, não será possível executar o script executável na mesma VM. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operacional compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o LVM (Gerenciador de volumes lógicos) e/ou Matrizes RAID de software são usados para gerenciar volumes lógicos em vários discos. Se a VM do Linux protegida utilizar LVM e/ou Matrizes RAID, não será possível executar o script na mesma VM. Em vez disso, execute o script em qualquer outra máquina com um SO compatível e que forneça suporte ao sistema de arquivos da VM protegida.

A saída de script seguinte exibe os discos de Matrizes RAID e/ou LVM e os volumes com o tipo de partição.

   ![Menu de Saída de LVM do Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Para colocar essas partições online, execute os comandos nas seções a seguir.

#### <a name="for-lvm-partitions"></a>Para partições LVM

Depois que o script é executado, as partições LVM são montadas nos volumes físicos/Disk especificados na saída do script. O processo é para

1. Obter a lista exclusiva de nomes de grupos de volumes dos volumes físicos ou discos
2. Em seguida, liste os volumes lógicos nesses grupos de volumes
3. Em seguida, monte os volumes lógicos em um caminho desejado.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Listando nomes de grupos de volume de volumes físicos

Para listar os nomes de grupos de volumes:

```bash
pvs -o +vguuid
```

Esse comando listará todos os volumes físicos (incluindo os presentes antes de executar o script), seus nomes de grupos de volumes correspondentes e os UUIDs (IDs de usuário) exclusivos do grupo de volumes. Uma saída de exemplo do comando é mostrada abaixo.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

A primeira coluna (VP) mostra o volume físico, as colunas subsequentes mostram o nome do grupo de volumes relevante, o formato, os atributos, o tamanho, o espaço livre e a ID exclusiva do grupo de volumes. A saída do comando mostra todos os volumes físicos. Consulte a saída do script e identifique os volumes relacionados ao backup. No exemplo acima, a saída do script teria mostrado/dev/sdf e/dev/sdd. Portanto, o grupo de volume *datavg_db* pertence ao script e o grupo de volume *Appvg_new* pertence ao computador. A ideia final é verificar se um nome de grupo de volumes exclusivo deve ter uma ID exclusiva.

###### <a name="duplicate-volume-groups"></a>Grupos de volumes duplicados

Há cenários em que os nomes de grupos de volumes podem ter dois UUIDs após a execução do script. Isso significa que os nomes do grupo de volumes no computador em que o script é executado e na VM de backup são os mesmos. Em seguida, precisamos renomear os grupos de volumes das VMs com backup. Veja o exemplo abaixo.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

A saída do script teria mostrado/dev/SDG,/dev/SDH,/dev/sdm2 como anexado. Assim, os nomes de VG correspondentes são Appvg_new e rootvg. Mas os mesmos nomes também estão presentes na lista VG do computador. Podemos verificar se um nome de VG tem dois UUIDs.

Agora, precisamos renomear os nomes de VG para volumes baseados em script, por exemplo:/dev/SDG,/dev/SDH,/dev/sdm2. Para renomear o grupo de volumes, use o seguinte comando

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Agora, temos todos os nomes de VG com IDs exclusivos.

###### <a name="active-volume-groups"></a>Grupos de volume ativos

Verifique se os grupos de volumes correspondentes aos volumes do script estão ativos. O comando a seguir é usado para exibir grupos de volume ativos. Verifique se os grupos de volume relacionados do script estão presentes nesta lista.

```bash
vgdisplay -a
```  

Caso contrário, ative o grupo de volumes usando o comando a seguir.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Listando volumes lógicos em grupos de volumes

Depois de obtermos a lista exclusiva e ativa de VGs relacionados ao script, os volumes lógicos presentes nesses grupos de volumes poderão ser listados usando o comando a seguir.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Este comando exibe o caminho de cada volume lógico como ' LV path '.

##### <a name="mounting-logical-volumes"></a>Montando volumes lógicos

Para montar os volumes lógicos no caminho de sua escolha:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Não use ' mount-a '. Esse comando monta todos os dispositivos descritos em '/etc/fstab '. Isso pode significar que dispositivos duplicados podem ser montados. Os dados podem ser redirecionados para dispositivos criados por um script, que não persistem os dados e, portanto, podem resultar em perda de dados.

#### <a name="for-raid-arrays"></a>Para matrizes RAID

O comando a seguir exibe detalhes sobre todos os discos raid:

```bash
#!/bin/bash
mdadm –detail –scan
```

 O disco RAID relevante é exibido como `/dev/mdm/<RAID array name in the protected VM>`

Use o comando de montagem se o disco RAID tiver volumes físicos:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outra LVM configurada, utilize o procedimento anterior para partições de LVM, mas utilize o nome de volume no lugar do nome do RAID Disk.

## <a name="system-requirements"></a>Requisitos do sistema

### <a name="for-windows-os"></a>Para Windows 10

A tabela a seguir mostra a compatibilidade entre os sistemas operacionais de computador e servidor. Ao recuperar arquivos, você não poderá restaurar arquivos para uma versão anterior ou uma versão futura do sistema operacional. Por exemplo, você não pode restaurar um arquivo de uma VM do Windows Server 2016 para um computador Windows Server 2012 ou Windows 8. É possível restaurar arquivos de uma VM para o mesmo sistema operacional do servidor ou para o sistema operacional compatível do cliente.

|Sistema operacional de servidor | Sistema operacional de cliente compatível  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para Sistema operacional Linux

No Linux, o SO do computador utilizado para restaurar arquivos deve fornecer suporte para o sistema de arquivos da máquina virtual protegida. Ao selecionar um computador para executar o script, certifique-se de que o computador possui um SO compatível e usa uma das versões identificadas na tabela a seguir:

|Sistema operacional Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e acima |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e acima |
| SLES | 12 e acima |
| openSUSE | 42.2 e acima |

> [!NOTE]
> Encontramos alguns problemas na execução do script de recuperação de arquivo em computadores com o sistema operacional SLES 12 SP4 e estamos investigando com a equipe do SLES.
> Atualmente, a execução do script de recuperação de arquivo está funcionando em computadores com versões do sistema operacional SLES 12 SP2 e SP3.
>

O script também exige componentes Python e bash para executar e se conectar com segurança ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| bash | 4 e acima |
| python | 2.6.6 e acima  |
| TLS | 1.2 deve ser compatível  |

## <a name="access-requirements"></a>Requisitos de acesso

Se você executar o script em um computador com acesso restrito, verifique se há acesso a:

- `download.microsoft.com`
- URLs do serviço de recuperação (nome geográfico refere-se à região onde reside o cofre dos serviços de recuperação)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (Para regiões públicas do Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn` (Para Azure China 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (Para Governo dos EUA para Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (Para Azure Alemanha)
- Portas de saída 53 (DNS), 443, 3260

> [!NOTE]
>
> - O nome do arquivo de script baixado terá o **nome geográfico** a ser preenchido na URL. Por exemplo: o nome do script baixado começa com \' VMname \' \_ \' geoname \' _ \' GUID \' , como *ContosoVM_wcus_12345678*
> - A URL seria <https://pod01-rec2.wcus.backup.windowsazure.com>"
>

Para o Linux, o script exige os componentes 'open-iscsi' e 'lshw' para se conectar ao ponto de recuperação. Se os componentes não existem no computador onde o script é executado, o script solicita a permissão para instalar os componentes. Conceda para instalar os componentes necessários.

É necessário ter acesso a `download.microsoft.com` para fazer download dos componentes usados para criar um canal seguro entre o computador em que o script é executado e os dados no ponto de recuperação.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Recuperação de arquivos de backups de VM com discos grandes

Esta seção explica como executar a recuperação de arquivos de backups de máquinas virtuais do Azure com mais de 16 discos ou cada tamanho de disco é maior que 4 TB.

Como o processo de recuperação de arquivo anexa todos os discos do backup, quando um grande número de discos (>16) ou discos grandes (> 4 TB cada) são usados, os seguintes pontos de ação são recomendados:

- Mantenha um servidor de restauração separado (máquinas virtuais D2v3 de VM do Azure) para recuperação de arquivos. Você pode usá-lo apenas para recuperação de arquivos e, em seguida, desligá-lo quando não for necessário. A restauração no computador original não é recomendada, pois ela terá um impacto significativo na própria VM.
- Em seguida, execute o script uma vez para verificar se a operação de recuperação de arquivo foi concluída com sucesso.
- Se o processo de recuperação de arquivo travar (os discos nunca são montados ou se estiverem montados, mas os volumes não aparecerem), execute as etapas a seguir.
  - Se o servidor de restauração for uma VM do Windows:
    - Verifique se o sistema operacional é WS 2012 ou superior.
    - Verifique se as chaves do Registro estão definidas conforme sugerido abaixo no servidor de restauração e reinicialize o servidor. O número ao lado do GUID pode variar de 0001 a 0005. No exemplo a seguir, é 0004. Navegue pelo caminho da chave do registro até a seção de parâmetros.

    ![Alterações da chave do registro](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se o servidor de restauração for uma VM do Linux:
  - No arquivo /etc/iSCSI/iscsid.conf, altere a configuração de:
    - `node.conn[0].timeo.noop_out_timeout = 5`  Para `node.conn[0].timeo.noop_out_timeout = 30`
- Depois de fazer a alteração acima, execute o script novamente. Com essas alterações, a recuperação do arquivo provavelmente será bem-sucedida.
- Cada vez que o usuário faz download de um script, o Backup do Azure inicia o processo de preparação do ponto de recuperação para download. Com discos grandes, esse processo levará um tempo considerável. Se houver picos de solicitações sucessivas, a preparação de destino entrará em uma espiral de download. Portanto, é recomendável baixar um script do Portal/PowerShell/CLI, aguardar de 20 a 30 minutos (um heurístico) e, em seguida, executá-lo. Neste momento, espera-se que o destino esteja pronto para a conexão do script.
- Após a recuperação de arquivos, verifique se você retornou ao portal e selecione **desmontar discos** para pontos de recuperação em que você não conseguiu montar volumes. Essencialmente, essa etapa limpará todos os processos/sessões existentes e aumentará a chance de recuperação.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas durante a recuperação de arquivos de máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/Cenário | Causas prováveis | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *Exceção detectada ao conectar-se ao destino* | O script não consegue acessar o ponto de recuperação    | Verifique se o computador preenche os [requisitos de acesso anteriores](#access-requirements). |  
| Saída de exe: *o destino já foi acessado por meio de uma sessão iSCSI.* | O script já foi executado na mesma máquina e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Eles não podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no Explorer para encontrar o arquivo. |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixe um novo script a partir do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido | Esse exe agora é inválido e não pode ser executado. Se você desejar acessar os arquivos dessa recuperação pontual, visite o portal para obter um novo exe.|
| No computador em que o exe é executado: Os novos volumes não serão desmontados depois que o botão de desmontagem for clicado | O iniciador iSCSI no computador não está respondendo/atualizando sua conexão para o destino e manutenção do cache. |  Depois de clicar em **Desmontar**, aguarde alguns minutos. Se os novos volumes não estiverem desmontados, navegue por todos os volumes. Navegar por todos os volumes força o iniciador a atualizar a conexão, e o volume é desmontado com uma mensagem de erro de que o disco não está disponível.|
| Saída de exe: O script é executado com êxito, mas a mensagem “Novos volumes anexados” não é exibida na saída do script |    Esse é um problema temporário    | Os volumes já terão sido anexados. Abra o Explorer para navegar. Se você estiver usando sempre o mesmo computador para todas as execuções de scripts, considere reiniciar o computador, e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico do Linux: não é possível exibir os volumes desejados | O SO da máquina onde o script é executado pode não reconhecer o sistema de arquivos subjacente da VM protegida | Verifique se o ponto de recuperação é consistente com a falha ou com o arquivo. Se for consistente com o arquivo, execute o script em outro computador cujo sistema operacional reconheça o sistema de arquivos da VM protegida. |
| Específico para Windows: não é possível exibir os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | Na tela de gerenciamento de disco, identifique os discos adicionais relacionados ao ponto de recuperação. Se algum desses discos estiver em um estado offline, tente colocá-los online clicando com o botão direito do mouse no disco e selecione **online**.|

## <a name="security"></a>Segurança

Esta seção aborda várias medidas de segurança tomadas para a implementação da recuperação de arquivos de backups de VM do Azure.

### <a name="feature-flow"></a>Fluxo de recursos

Esse recurso foi criado para acessar os dados da VM sem a necessidade de restaurar toda a VM ou os discos de VM e com o número mínimo de etapas. O acesso aos dados da VM é fornecido por um script (que monta o volume de recuperação quando executado conforme mostrado abaixo) que forma a base de todas as implementações de segurança:

  ![Fluxo do recurso de segurança](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementações de segurança

#### <a name="select-recovery-point-who-can-generate-script"></a>Selecionar o ponto de recuperação (quem pode gerar script)

O script fornece acesso aos dados da VM, portanto, é importante regular quem pode gerá-lo. Você precisa entrar no portal do Azure e ser [autorizado pelo RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) para gerar o script.

A recuperação de arquivo precisa do mesmo nível de autorização necessário para restauração de VM e restauração de discos. Em outras palavras, somente usuários autorizados podem exibir os dados da VM e podem gerar o script.

O script gerado é assinado com o certificado oficial da Microsoft para o serviço de Backup do Azure. Qualquer violação no script significa que a assinatura foi violada e qualquer tentativa de executar o script é sinalizada como um risco potencial pelo sistema operacional.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montar o volume de recuperação (quem pode executar o script)

Somente um administrador pode executar o script que deve ser executado no modo elevado. O script executa apenas um conjunto de etapas gerado previamente e não aceita a entrada de qualquer fonte externa.

Para executar o script, é necessária uma senha que só é mostrada para o usuário autorizado no momento da geração de script no portal do Azure ou PowerShell/CLI. Isso é para garantir que o usuário autorizado que baixa o script também seja responsável por executar o script.

#### <a name="browse-files-and-folders"></a>Procurar arquivos e pastas

Para procurar arquivos e pastas, o script usa o iniciador iSCSI no computador e conecta-se ao ponto de recuperação configurado como um destino iSCSI. Aqui você pode imaginar cenários em que alguém está tentando imitar/falsificar um ou todos os componentes.

Usamos um mecanismo de autenticação CHAP mútuo para que cada componente autentique o outro. Isso significa que é extremamente difícil para um iniciador falso se conectar ao destino iSCSI e para que um destino falso seja conectado ao computador na qual o script é executado.

O fluxo de dados entre o serviço de recuperação e o computador é protegido por meio da criação de um túnel TLS seguro sobre TCP ([TLS 1.2 deve ter suporte](#system-requirements) no computador no qual o script é executado).

Qualquer ACL (lista de controle de acesso) de arquivo presente na VM pai/com backup também é preservada no sistema de arquivos montado.

O script fornece acesso somente leitura a um ponto de recuperação e é válido por apenas 12 horas. Se você quiser remover o acesso anteriormente, entre no portal do Azure/PowerShell/CLI e realize a **desmontagem de discos** para esse ponto de recuperação específico. O script será invalidado imediatamente.

## <a name="next-steps"></a>Próximas etapas

- Se enfrentar algum problema durante a restauração de arquivos, consulte a seção [Solução de problemas](#troubleshooting)
- Saiba como [restaurar arquivos por meio do PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Saiba como [restaurar arquivos por meio da CLI do Azure](./tutorial-restore-files.md)
- Depois que a VM for restaurada, saiba como [gerenciar backups](./backup-azure-manage-vms.md)
