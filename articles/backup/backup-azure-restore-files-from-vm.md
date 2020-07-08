---
title: Recuperar arquivos e pastas de um backup de VM do Azure
description: Este artigo explica como recuperar arquivos e pastas de um ponto de recuperação de máquina virtual do Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: ded26718f176629f6c53ae90abf3c7e69b4df893
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027157"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar arquivos de um backup de máquina virtual do Azure

O Backup do Azure fornece a capacidade de restaurar [máquinas virtuais (VMs) do Azure e discos](./backup-azure-arm-restore-vms.md) de backups de máquina virtual do Azure, também conhecido como pontos de recuperação. Este artigo explica como recuperar arquivos e pastas de um backup de VM do Azure. A restauração de arquivos e pastas está disponível somente para VMs do Azure implantadas usando o modelo Gerenciador de Recursos e protegidas para um cofre de serviços de Recuperação.

> [!NOTE]
> Esse recurso está disponível para máquinas virtuais do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre dos Serviços de Recuperação.
> Não há suporte para a recuperação de arquivos de um backup criptografado de VM.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copiar arquivos

Para restaurar arquivos ou pastas no ponto de recuperação, vá para a máquina virtual e escolha o ponto de recuperação desejado.

1. Faça logon no [portal do Azure](https://portal.Azure.com) e no painel esquerdo, clique em **Máquinas Virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o painel da máquina virtual.

2. No menu da máquina virtual, clique em **Backup** para abrir o painel de Backup.

    ![Abra o item de backup do cofre de Serviços de Recuperação](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu do painel de Backup, clique em **Recuperação de Arquivo**.

    ![Botão de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O menu **Recuperação de Arquivo** é aberto.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. No menu suspenso **Selecionar ponto de recuperação**, selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

5. Para baixar o software utilizado para copiar arquivos do ponto de recuperação, clique em **Baixar executável** (para VMs do Windows Azure) ou **Baixar script** (para VMs do Linux Azure, um script Python é gerado).

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    O Azure baixa o arquivo executável ou o script para o computador local.

    ![baixar mensagem para o arquivo executável ou o script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o arquivo executável ou o script como um administrador, recomendamos salvar o arquivo baixado no seu computador.

6. O arquivo executável ou o script é protegido por senha e requer uma senha. No menu **Recuperação de Arquivo**, clique no botão de cópia para carregar a senha na memória.

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Verifique se [você tem o computador certo](#selecting-the-right-machine-to-run-the-script) para executar o script. Se o computador certo for o mesmo computador em que você baixou o script, você poderá continuar para a seção de download. No local do download (geralmente a pasta *Downloads*), clique com o botão direito do mouse no arquivo executável ou no script e execute-o com as credenciais de Administrador. Quando solicitado, digite a senha ou cole a senha a partir da memória e pressione **Enter**. Quando a senha válida é inserida, o script conecta-se ao ponto de recuperação.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Para computadores Linux, um script Python é gerado. É necessário baixar o script e copiá-lo para o servidor Linux relevante/compatível. Talvez seja necessário modificar as permissões para executá-lo com ```chmod +x <python file name>```. Em seguida, execute o arquivo Python com ```./<python file name>```.

Consulte a seção [Requisitos de acesso](#access-requirements) para verificar se o script foi executado com êxito.

### <a name="identifying-volumes"></a>Identificação de volumes

#### <a name="for-windows"></a>Para Windows

Quando você executa o arquivo executável, o sistema operacional monta os novos volumes e atribui letras de unidade. Você pode usar o Windows Explorer ou o Explorador de arquivos para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original. No entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original fosse “Disco de Dados (E:`\`)”, esse volume poderia ser anexado no computador local como “Disco de Dados (“Qualquer letra”:`\`). Busque em todos os volumes mencionados na saída do script até encontrar seus arquivos ou suas pastas.  

   ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

No Linux, os volumes do ponto de recuperação são montados para a pasta onde o script é executado. Os discos anexados, volumes e os caminhos de montagem correspondente são mostrados adequadamente. Esses caminhos de montagem ficam visíveis aos usuários que têm acesso ao nível raiz. Percorra os volumes mencionados na saída do script.

  ![Menu de recuperação de Arquivo do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Encerrando a conexão

Depois de identificar os arquivos e copiá-los para um local de armazenamento local, remova ou desmonte as unidades adicionais. Para desmontar as unidades no menu de **Recuperação de Arquivos** no Portal do Azure, clique em **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Quando os discos tiverem sido desmontados, você receberá uma mensagem. Pode levar alguns minutos até que a conexão seja atualizada para que você possa remover os discos.

No Linux, após a conexão com o ponto de recuperação ser interrompida, o sistema operacional não removerá automaticamente os caminhos de montagem correspondentes. Os caminhos de montagem existem como volumes "órfãos" e são visíveis, mas geram um erro ao acessar/gravar os arquivos. Eles podem ser removidos manualmente. O script, quando executado, identifica esses volumes existentes em qualquer ponto de recuperação anterior e limpa mediante consentimento.

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

Para listar os nomes de grupo de volumes em um volume físico:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Para listar todos os volumes lógicos, nomes e seus caminhos em um grupo de volumes:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs commands results>
```

O comando ```lvdisplay``` também mostra se os grupos de volumes estão ativos ou não. Se o grupo de volumes estiver marcado como inativo, ele precisará ser ativado novamente para ser montado. Se o grupo de volumes for mostrado como inativo, use o comando a seguir para ativá-lo.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name from the pvs commands results>
```

Depois que o nome do grupo de volumes estiver ativo, execute o comando ```lvdisplay``` mais uma vez para ver todos os atributos relevantes.

Para montar os volumes lógicos no caminho de sua escolha:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

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
- URLs de serviço de recuperação (geo-name refere-se para a região onde reside o cofre dos serviços de recuperação)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(Para regiões públicas do Azure)
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

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Se o servidor de restauração for uma VM do Linux:
  - No arquivo /etc/iSCSI/iscsid.conf, altere a configuração de:
    - node.conn[0].timeo.noop_out_timeout = 5 para node.conn[0].timeo.noop_out_timeout = 30
- Depois de fazer a alteração acima, execute o script novamente. Com essas alterações, a recuperação do arquivo provavelmente será bem-sucedida.
- Cada vez que o usuário faz download de um script, o Backup do Azure inicia o processo de preparação do ponto de recuperação para download. Com discos grandes, esse processo levará um tempo considerável. Se houver picos de solicitações sucessivas, a preparação de destino entrará em uma espiral de download. Portanto, é recomendável baixar um script do Portal/PowerShell/CLI, aguardar de 20 a 30 minutos (um heurístico) e, em seguida, executá-lo. Neste momento, espera-se que o destino esteja pronto para a conexão do script.
- Após a recuperação de arquivos, verifique se você retornou ao portal e clique em **Desmontar discos** para pontos de recuperação em que você não conseguiu montar volumes. Essencialmente, essa etapa limpará todos os processos/sessões existentes e aumentará a chance de recuperação.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas durante a recuperação de arquivos de máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/Cenário | Causas prováveis | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *Exceção detectada ao conectar-se ao destino* | O script não consegue acessar o ponto de recuperação    | Verifique se o computador preenche os [requisitos de acesso anteriores](#access-requirements). |  
| Saída de exe: *o destino já foi acessado por meio de uma sessão iSCSI.* | O script já foi executado na mesma máquina e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Eles NÃO podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no Explorer para encontrar o arquivo. |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixe um novo script a partir do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido | Esse exe agora é inválido e não pode ser executado. Se você desejar acessar os arquivos dessa recuperação pontual, visite o portal para obter um novo exe.|
| No computador em que o exe é executado: Os novos volumes não serão desmontados depois que o botão de desmontagem for clicado | O iniciador iSCSI no computador não está respondendo/atualizando sua conexão para o destino e manutenção do cache. |  Depois de clicar em **Desmontar**, aguarde alguns minutos. Se os novos volumes não estiverem desmontados, navegue por todos os volumes. Navegar por todos os volumes força o iniciador a atualizar a conexão, e o volume é desmontado com uma mensagem de erro de que o disco não está disponível.|
| Saída de exe: O script é executado com êxito, mas a mensagem “Novos volumes anexados” não é exibida na saída do script |    Esse é um problema temporário    | Os volumes já terão sido anexados. Abra o Explorer para navegar. Se você estiver usando sempre o mesmo computador para todas as execuções de scripts, considere reiniciar o computador, e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico do Linux: não é possível exibir os volumes desejados | O SO da máquina onde o script é executado pode não reconhecer o sistema de arquivos subjacente da VM protegida | Verifique se o ponto de recuperação é consistente com a falha ou com o arquivo. Se for consistente com o arquivo, execute o script em outro computador cujo sistema operacional reconheça o sistema de arquivos da VM protegida. |
| Específico para Windows: não é possível exibir os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | Na tela de gerenciamento de disco, identifique os discos adicionais relacionados ao ponto de recuperação. Se algum desses discos estiver no estado offline, tente deixá-los online clicando com o botão direito do mouse no disco e, em seguida, em **Online**.|

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

Para executar o script, é necessária uma senha que é mostrada apenas para o usuário autorizado no momento da geração de script no portal do Azure ou PowerShell/CLI. Isso é para garantir que o usuário autorizado que baixa o script também seja responsável por sua execução.

#### <a name="browse-files-and-folders"></a>Procurar arquivos e pastas

Para procurar arquivos e pastas, o script usa o iniciador iSCSI no computador e se conecta ao ponto de recuperação configurado como um destino iSCSI. Aqui você pode imaginar cenários em que alguém está tentando imitar/falsificar um ou todos os componentes.

Usamos um mecanismo de autenticação CHAP mútuo para que cada componente autentique o outro. Isso significa que é extremamente difícil para um iniciador falso se conectar ao destino iSCSI e para que um destino falso seja conectado ao computador na qual o script é executado.

O fluxo de dados entre o serviço de recuperação e o computador é protegido por meio da criação de um túnel TLS seguro sobre TCP ([TLS 1.2 deve ter suporte](#system-requirements) no computador no qual o script é executado).

Qualquer ACL (lista de controle de acesso) de arquivo presente na VM pai/com backup também é preservada no sistema de arquivos montado.

O script fornece acesso somente leitura a um ponto de recuperação e é válido por apenas 12 horas. Se você quiser remover o acesso antes disso, entre no portal do Azure/PowerShell/CLI e execute **desmontar discos** para esse ponto de recuperação específico. O script será invalidado imediatamente.

## <a name="next-steps"></a>Próximas etapas

- Se enfrentar algum problema durante a restauração de arquivos, consulte a seção [Solução de problemas](#troubleshooting)
- Saiba como [restaurar arquivos por meio do PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Saiba como [restaurar arquivos por meio da CLI do Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- Depois que a VM for restaurada, saiba como [gerenciar backups](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
