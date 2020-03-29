---
title: Backup do estado do sistema de solução de problemas
description: Neste artigo, saiba como solucionar problemas no Backup do Estado do Sistema para servidores Windows no local.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969567"
---
# <a name="troubleshoot-system-state-backup"></a>Backup do estado do sistema de solução de problemas

Este artigo descreve soluções para problemas que você pode encontrar ao usar o Backup do Estado do Sistema.

## <a name="basic-troubleshooting"></a>Solução básica de problemas

Recomendamos que você execute a validação abaixo, antes de iniciar a solução de problemas do backup do Estado do Sistema:

- [Certifique-se de que o Agente mars (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que há conectividade de rede entre o agente MARS e o Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (no console de Serviço). Se necessário, reinicie e tente novamente a operação
- [Certifique-se de que há de 5 a 10% de espaço de volume livre disponível no local da pasta temporária](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Verifique se outro processo ou software antivírus está interferindo com o Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Falha no backup agendado, mas o backup manual funciona](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Certifique-se de que o SO possui as atualizações mais recentes
- [Certifique-se de que unidades e arquivos sem suporte com atributos não suportados sejam excluídos do backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Certifique-se de que o **Relógio do sistema** no sistema protegido está configurado com o fuso horário correto <br>
- [Certifique-se de que o servidor tenha pelo menos o .Net Framework versão 4.5.2 e superior](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se você está tentando **reregistrar seu servidor** em um cofre, então: <br>
  - Certifique-se de que o agente está desinstalado no servidor e ele é excluído do portal <br>
  - Use a mesma senha que foi inicialmente usada para registrar o servidor <br>
- Se este for um backup off-line, certifique-se de que a versão 3.7.0 do Azure PowerShell esteja instalada no computador de origem e cópia antes de iniciar a operação de backup off-line
- [Consideração quando o agente de backup está sendo executado em uma máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Limitações

- Fazer a recuperação em hardware diferente usando a recuperação do Estado do Sistema não é recomendado pela Microsoft
- O backup do Estado do Sistema atualmente suporta servidores Windows "on-premises". Esta funcionalidade não está disponível para VMs Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de solucionar o backup do estado do sistema com o backup do Azure, execute a verificação de pré-requisitos abaixo.  

### <a name="verify-windows-server-backup-is-installed"></a>Verifique se o backup do Windows Server está instalado

Certifique-se de que o backup do Windows Server esteja instalado e habilitado no servidor. Para verificar o status da instalação, execute este comando PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se a saída exibir o **Estado de Instalação** como **disponível,** significa que o recurso de backup do Windows Server está disponível para a instalação, mas não está instalado no servidor. No entanto, se o Backup do Windows Server não estiver instalado, use um dos métodos abaixo para instalá-lo.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Método 1: Instalar o backup do Windows Server usando o PowerShell

Para instalar o Backup do Windows Server usando o PowerShell, execute o comando abaixo:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Método 2: Instalar o backup do Servidor Do Windows usando o Server Manager

Para instalar o Backup do Windows Server usando o Server Manager, execute as etapas abaixo:

1. Em **Server Manger,** clique **em Adicionar funções e recursos**. O **assistente Adicionar funções e recursos** é exibido.

    ![Painel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecione **Tipo de instalação** e clique em **Next**.

    ![Tipo de Instalação](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecione um servidor no pool de servidores e clique **em Next**. Na função servidor, deixe a seleção padrão e clique **em Seguir**.
4. Selecione **o backup do Servidor do Windows** na guia **Recursos** e clique **em Next**.

    ![recursos](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na guia **Confirmação,** clique em **Instalar** para iniciar o processo de instalação.
6. Na guia **Resultados,** ele exibirá que o recurso de backup do Servidor Do Windows é instalado com sucesso no seu Servidor Windows.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Permissão de informações do volume do sistema

Certifique-se de que o SISTEMA Local tenha controle total na pasta **Informações de Volume do Sistema** localizada no volume onde o Windows está instalado. Normalmente, isso é **C:\Informações de volume do sistema**. O backup do Windows Server pode falhar se as permissões acima não forem definidas corretamente

### <a name="dependent-services"></a>Serviços dependentes

Certifique-se de que os serviços abaixo estão em estado de execução:

**Nome do serviço** | **Tipo de inicialização**
--- | ---
Chamada de procedimento remoto (RPC) | Automático
Sistema de eventos COM+ (EventSystem) | Automático
Serviço de notificação de eventos do sistema (SENS) | Automático
Cópia de sombra de volume(VSS) | Manual
Provedor de cópia de sombra de software da Microsoft (SWPRV) | Manual

### <a name="validate-windows-server-backup-status"></a>Validar o status de backup do Windows Server

Para validar o status de backup do Windows Server, execute as seguintes etapas:

- Certifique-se de que o WSB PowerShell está sendo executado

  - Execute `Get-WBJob` a partir de um PowerShell elevado e certifique-se de que ele não retorne o seguinte erro:

    > [!WARNING]
    > Get-WBJob: O termo 'Get-WBJob' não é reconhecido como o nome de um cmdlet, função, arquivo de script ou programa operável. Verifique a ortografia do nome ou se um caminho foi incluído, verifique se ele está correto e tente novamente.

    - Se falhar com esse erro, reinstale o recurso de backup do Servidor Do Windows na máquina do servidor, conforme mencionado na etapa 1 dos pré-requisitos.

  - Certifique-se de que o backup wsb está funcionando corretamente, executando o comando abaixo a partir de um prompt de comando elevado:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Substitua X pela letra de unidade do volume onde deseja armazenar a imagem de backup do estado do sistema.

    - Verifique periodicamente o status do `Get-WBJob` trabalho executando o comando do PowerShell elevado
    - Após o trabalho de backup, verifique o `Get-WBJob -Previous 1` status final do trabalho executando o comando

Se o trabalho falhar, ele indica um problema wsb que resultaria em falha no sistema de backups do sistema de agente MARS.

## <a name="common-errors"></a>Erros comuns

### <a name="vss-writer-timeout-error"></a>Erro de tempo do escritor VSS

| Sintoma | Causa | Resolução
| -- | -- | --
| - Agente MARS falha com mensagem de erro: "O trabalho wsb falhou com erros de VSS. Verifique os registros de eventos VSS para resolver a falha"<br/><br/> - O registro de erros a seguir está presente nos registros de eventos do VSS Application: "Um escritor VSS rejeitou um evento com erro 0x800423f2, o tempo de tempo do escritor expirou entre os eventos Freeze e Thaw."| O escritor VSS não consegue ser concluído a tempo devido à falta de recursos de CPU e memória na máquina <br/><br/> Outro software de backup já está usando o escritor VSS, como resultado, a operação de snapshot não poderia ser concluída para este backup | Aguarde que a CPU/memória seja liberada no sistema ou aborte os processos que tomam muita memória/CPU e tente a operação novamente. <br/><br/>  Aguarde que o backup em andamento seja concluído e tente a operação em um momento posterior, quando não houver backups na máquina.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espaço em disco insuficiente para cultivar cópias de sombra

| Sintoma | Resolução
| -- | --
| - Agente MARS falha com mensagem de erro: Falha no backup, pois o volume da cópia de sombra não pôde crescer devido ao espaço insuficiente do disco em volumes que contêm arquivos do sistema <br/><br/> - O seguinte registro de erro/aviso está presente nos registros de eventos do sistema volsnap: "Não havia espaço suficiente em disco no volume C: para aumentar o armazenamento de cópia de sombra para cópias de sombra de C: devido a esta falha todas as cópias de sombra do volume C: correm o risco de serem excluídas" | - Liberar espaço no volume destacado no registro de eventos para que haja espaço suficiente para que as cópias de sombra cresçam enquanto o backup está em andamento <br/><br/> - Durante a configuração do espaço de cópia de sombra, podemos restringir a quantidade de espaço usado para cópia de sombra. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Partição EFI bloqueada

| Sintoma | Resolução
| -- | --
| O agente MARS falha com a mensagem de erro: "Falha no backup do estado do sistema, pois a partição do sistema EFI está bloqueada. Isso pode ser devido ao acesso à partição do sistema por um software de segurança de terceiros ou backup" | - Se o problema for devido a um software de segurança de terceiros, então você precisa entrar em contato com o fornecedor antivírus para que eles possam permitir que o agente MARS <br/><br/> - Se um software de backup de terceiros estiver sendo executado, então espere que ele termine e, em seguida, tente novamente

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o estado do sistema Windows na implantação do Resource Manager, consulte [Backup do Estado do sistema do Windows Server](backup-azure-system-state.md)
