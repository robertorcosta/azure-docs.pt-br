---
title: Solucionar problemas do agente de backup do Azure
description: Neste artigo, saiba como solucionar problemas na instalação e registro do agente de backup do Azure.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 24169356600c25e664221af397051bb0fec3e459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673075"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Solucionar problemas do agente Microsoft Azure Recovery Services (MARS)

Este artigo descreve como resolver erros que você pode ver durante a configuração, o registro, o backup e a restauração.

## <a name="basic-troubleshooting"></a>Solução básica de problemas

Recomendamos que você verifique o seguinte antes de começar a solucionar problemas do Microsoft o agente Mars (Azure Recovery Services, serviços de recuperação do Azure):

- [Certifique-se de que o agente MARS está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Certifique-se de ter conectividade de rede entre o agente MARS e o Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Certifique-se de que a MARS está em execução (no console de serviço). Se precisar, reinicie e tente novamente a operação.
- [Certifique-se de que 5% a 10% do espaço de volume livre esteja disponível no local da pasta de arranhões](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Verifique se outro processo ou software antivírus está interferindo no Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Se o backup programado falhar, mas o backup manual funcionar, consulte [Backups não serão executados de acordo com o cronograma](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule).
- Certifique-se de que seu Sistema Operacional tenha as últimas atualizações.
- [Certifique-se de que unidades e arquivos sem suporte com atributos não suportados sejam excluídos do backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Certifique-se de que o relógio do sistema protegido está configurado para o fuso horário correto.
- [Certifique-se de que o Framework .NET 4.5.2 ou posterior está instalado no servidor](https://www.microsoft.com/download/details.aspx?id=30653).
- Se você está tentando reregistrar seu servidor em um cofre:
  - Certifique-se de que o agente está desinstalado no servidor e que ele é excluído do portal.
  - Use a mesma senha que foi usada inicialmente para registrar o servidor.
- Para backups off-line, certifique-se de que o Azure PowerShell 3.7.0 esteja instalado tanto na origem quanto no computador de cópia antes de iniciar o backup.
- Se o agente de backup estiver sendo executado em uma máquina virtual do Azure, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Credenciais do cofre fornecidas inválidas

**Mensagem de erro**: Credenciais de cofre inválidas fornecidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)

| Causa | Ações recomendadas |
| ---     | ---    |
| **As credenciais do cofre não são válidas** <br/> <br/> Os arquivos de credencial do cofre podem estar corrompidos ou podem ter expirado. (Por exemplo, eles podem ter sido baixados mais de 48 horas antes do momento do registro.)| Baixe novas credenciais do cofre dos Serviços de Recuperação no portal Azure. (Veja o passo 6 na [seção Baixar o agente MARS.)](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) Em seguida, tome essas medidas, conforme apropriado: <ul><li> Se você já instalou e registrou mars, abra o console MMC do Agente de Backup do Microsoft Azure e selecione **O Servidor de Registro** no painel **Ações** para concluir o registro com as novas credenciais. <br/> <li> Se a nova instalação falhar, tente reinstalar com as novas credenciais.</ul> **Nota:** Se vários arquivos de credenciais do cofre tiverem sido baixados, apenas o arquivo mais recente será válido pelas próximas 48 horas. Recomendamos que você baixe um novo arquivo de credencial do cofre.
| **Servidor/firewall proxy está bloqueando o registro** <br/>ou <br/>**Sem conectividade com a internet** <br/><br/> Se sua máquina ou servidor proxy tiver conectividade limitada com a internet e você não garantir acesso às URLs necessárias, o registro falhará.| Siga estas etapas:<br/> <ul><li> Trabalhe com sua equipe de TI para garantir que o sistema tenha conectividade com a internet.<li> Se você não tiver um servidor proxy, certifique-se de que a opção proxy não seja selecionada quando você registrar o agente. [Verifique as configurações do proxy](#verifying-proxy-settings-for-windows).<li> Se você tiver um servidor de firewall/proxy, trabalhe com sua equipe de rede para garantir que esses URLs e endereços IP tenham acesso:<br/> <br> **Urls**<br> `www.msftncsi.com` <br> . Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Endereços IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Tente registrar novamente depois de concluir as etapas anteriores de solução de problemas.<br></br> Se a conexão for via Azure ExpressRoute, certifique-se de que as configurações estão configuradas conforme descrito no [suporte ao Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Software antivírus está bloqueando o registro** | Se você tiver um software antivírus instalado no servidor, adicione as regras de exclusão necessárias à varredura antivírus para esses arquivos e pastas: <br/><ul> <li> CBengine.exe <li> Csc.exe<li> A pasta do arranhão. Sua localização padrão é C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Scratch. <li> A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recomendações adicionais

- Vá para C:/Windows/Temp e verifique se há mais de 60.000 ou 65.000 arquivos com a extensão .tmp. Se houver, exclua esses arquivos.
- Certifique-se de que a data e a hora da máquina correspondem ao fuso horário local.
- Certifique-se de que [esses sites](install-mars-agent.md#verify-internet-access) sejam adicionados aos seus sites confiáveis no Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verificando configurações de proxy para Windows

1. Baixe PsExec na página [Sysinternals.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Corra `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` de um comando elevado.

   Este comando abrirá o Internet Explorer.
1. Vá para **ferramentas** > **opções de Internet Conexões** > **configurações** > **lan**.
1. Verifique as configurações de proxy para a conta do sistema.
1. Se nenhum proxy estiver configurado e os detalhes do proxy forem fornecidos, remova os detalhes.
1. Se um proxy estiver configurado e os detalhes do proxy estiverem incorretos, certifique-se de que os detalhes **do PROXY IP** e da **Porta** estão corretos.
1. Feche o Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Não é possível baixar o arquivo de credenciais do cofre

| Erro   | Ações recomendadas |
| ---     | ---    |
|Falha ao baixar o arquivo de credenciais do cofre. (ID: 403) | <ul><li> Tente baixar as credenciais do cofre usando um navegador diferente ou tome essas medidas: <ul><li> Inicie o Internet Explorer. Selecione F12. </li><li> Vá para a guia **Rede** e limpe o cache e os cookies. </li> <li> Atualize a página.<br></li></ul> <li> Verifique se a assinatura está desativada/expirada.<br></li> <li> Verifique se alguma regra de firewall está bloqueando o download. <br></li> <li> Certifique-se de que não esgotou o limite do cofre (50 máquinas por cofre).<br></li>  <li> Certifique-se de que o usuário tenha as permissões de backup do Azure que são necessárias para baixar as credenciais do cofre e registrar um servidor no cofre. Consulte [Usar controle de acesso baseado em função para gerenciar pontos de recuperação de backup do Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure

| Erro  | Possível causa | Ações recomendadas |
| ---     | ---     | ---    |
| <br /><ul><li>O Microsoft Azure Recovery Service Agent não pôde se conectar ao Microsoft Azure Backup. (ID: 100050) Verifique as configurações da rede e certifique-se de que você é capaz de se conectar à internet.<li>(407) Autenticação de Proxy Necessária. |Um proxy está bloqueando a conexão. |  <ul><li>No Internet Explorer, vá para **ferramentas** > **de internet opções** > **de Segurança** > **Internet**. Selecione **Nível personalizado** e desça até a seção **Desbaixar arquivos.** Selecione **Habilitar**.<p>Você também pode ter que adicionar [URLs e endereços IP](install-mars-agent.md#verify-internet-access) aos seus sites confiáveis no Internet Explorer.<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<li> Se a máquina tiver acesso limitado à Internet, certifique-se de que as configurações de firewall na máquina ou proxy permitam que essas [URLs e endereços IP](install-mars-agent.md#verify-internet-access). <li>Se você tiver um software antivírus instalado no servidor, exclua esses arquivos da varredura antivírus: <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionado ao .NET Framework). Há um CSC.exe para cada versão do .NET Framework instalada no servidor. Exclua os arquivos CSC.exe para todas as versões do .NET Framework no servidor afetado. <li>A pasta de risco ou o local do cache. <br>O local padrão para a pasta de risco ou o caminho do cache é C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Scratch.<li>A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao configurar a chave de criptografia para backups seguros

| Erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| <br />Falha ao definir a chave de criptografia para backups seguros. A ativação não teve sucesso total, mas a senha de criptografia foi salva no arquivo a seguir. |<li>O servidor já está registrado com outro cofre.<li>Durante a configuração, a frase secreta foi corrompida.| Desregistre o servidor no cofre e registre-o novamente com uma nova senha.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não foi concluída com êxito

| Erro  | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
|<br />A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft.     | <li> A pasta de risco está localizada em um volume que não tem espaço suficiente. <li> A pasta de arranhão foi movida incorretamente. <li> O arquivo OnlineBackup.KEK está ausente.         | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do agente MARS.<li>Mova a pasta de risco ou o local de cache para um volume com espaço livre entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas comuns sobre backup de arquivos e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de risco ou o caminho do cache é C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A frase secreta de criptografia não está configurada corretamente

| Erro  | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
| <br />Error 34506. A senha de criptografia armazenada neste computador não está configurada corretamente.    | <li> A pasta de risco está localizada em um volume que não tem espaço suficiente. <li> A pasta de arranhão foi movida incorretamente. <li> O arquivo OnlineBackup.KEK está ausente.        | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta de risco ou o local de cache para um volume com espaço livre entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas comuns sobre backup de arquivos e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de risco ou o caminho do cache é C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Backups não funcionam de acordo com o cronograma

Se os backups agendados não forem acionados automaticamente, mas os backups manuais funcionarem corretamente, tente as seguintes ações:

- Certifique-se de que o cronograma de backup do Windows Server não entre em conflito com o cronograma de backup de arquivos e pastas do Azure.

- Certifique-se de que o status de backup on-line está definido **como Habilitar**. Para verificar o status, tome estas etapas:

  1. No Task Scheduler, expanda a **Microsoft** e selecione **Backup On-line**.
  1. Clique duas vezes em **Microsoft-OnlineBackup** e vá para a guia **Gatilhos.**
  1. Verifique se o status está definido **como Ativado**. Se não for, selecione **Editar,** selecione **Ativado**e selecione **OK**.

- Certifique-se de que a conta de usuário selecionada para executar a tarefa seja **do SISTEMA** ou do grupo **de administradores locais** no servidor. Para verificar a conta do usuário, vá até a guia **Geral** e verifique as opções **de Segurança.**

- Certifique-se de que o PowerShell 3.0 ou posterior está instalado no servidor. Para verificar a versão do PowerShell, `Major` execute este comando e verifique se o número da versão é 3 ou posterior:

  `$PSVersionTable.PSVersion`

- Certifique-se de que `PSMODULEPATH` esse caminho faz parte da variável ambiente:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a diretiva de `LocalMachine` execução `restricted`do PowerShell estiver definida como , o cmdlet PowerShell que aciona a tarefa de backup pode falhar. Execute esses comandos no modo elevado para verificar `Unrestricted` e `RemoteSigned`definir a política de execução para ou:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Certifique-se de que não faltam ou corrompe arquivos mSOnlineBackup do módulo PowerShell. Se houver arquivos ausentes ou corrompidos, tome estas medidas:

  1. A partir de qualquer máquina que tenha um agente MARS que esteja funcionando corretamente, copie a pasta MSOnlineBackup de C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na máquina problemática, cole os arquivos copiados no mesmo local da pasta (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se já houver uma pasta MSOnlineBackup na máquina, cole os arquivos nela ou substitua quaisquer arquivos existentes.

> [!TIP]
> Para garantir que as alterações sejam aplicadas de forma consistente, reinicie o servidor após a execução das etapas anteriores.

## <a name="troubleshoot-restore-problems"></a>Problemas de restauração de solução de problemas

O Backup do Azure pode não montar com êxito o volume de recuperação, mesmo depois de vários minutos. E você pode receber mensagens de erro durante o processo. Para começar a se recuperar normalmente, tome estas medidas:

1. Cancele o processo de montagem se estiver funcionando por vários minutos.

2. Verifique se você tem a versão mais recente do agente de backup. Para verificar a versão, no painel **Ações** do console MARS, selecione **Sobre o Microsoft Azure Recovery Services Agent**. Confirme se o número de **versão** é igual ou maior do que a versão mencionada [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Selecione este link para [baixar a versão mais recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Vá para**os controladores de armazenamento** do **Gerenciador** > de dispositivos e **localize o iniciador microsoft iSCSI**. Se você localizá-lo, vá diretamente para o passo 7.

4. Se você não conseguir localizar o serviço de iniciação Microsoft iSCSI, tente encontrar uma entrada em**controladores** de armazenamento **do Gerenciador** > de Dispositivos chamado Dispositivo Desconhecido com **ID** de hardware **ROOT\ISCSIPRT**.

5. Clique com o botão direito do **mouse dispositivo desconhecido** e selecione Update Driver **Software**.

6. Atualize o driver ao selecionar a opção para **Pesquisar automaticamente software de driver atualizado**. Esta atualização deve alterar **dispositivo desconhecido** para **o iniciador microsoft iSCSI**:

    ![Captura de tela do Gerenciador de Dispositivos do Backup do Azure, com controladores de Armazenamento realçados](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Vá para o Serviço de Iniciação do **Gerenciador** > de**Tarefas (Local)** > **Microsoft iSCSI :**

    ![Captura de tela do Gerenciador de Tarefas do Backup do Azure, com Serviços (Local) realçado](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Reinicie o serviço Iniciador do Microsoft iSCSI. Para isso, clique com o botão direito do mouse no serviço e selecione **Stop**. Em seguida, clique com o botão direito do mouse novamente e selecione **Iniciar**.

9. Repita a recuperação usando a [Restauração Instantânea](backup-instant-restore-capability.md).

Se a recuperação ainda falhar, reinicie seu servidor ou cliente. Se você não quiser reiniciar, ou se a recuperação ainda falhar mesmo depois de reiniciar o servidor, tente [se recuperar de outra máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Solucionar problemas de cache

A operação de backup pode falhar se a pasta cache (também chamada de pasta de risco) estiver configurada incorretamente, faltando pré-requisitos ou tiver acesso restrito.

### <a name="prerequisites"></a>Pré-requisitos

Para que as operações do agente MARS tenham sucesso, a pasta de cache precisa seguir os requisitos abaixo:

- [Certifique-se de que 5% a 10% do espaço de volume livre está disponível no local da pasta de risco](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Certifique-se de que a localização da pasta de arranhões é válida e acessível](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Certifique-se de que os atributos de arquivo na pasta cache sejam suportados](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Certifique-se de que o espaço de armazenamento da cópia de sombra alocado é suficiente para o processo de backup](#increase-shadow-copy-storage)
- [Certifique-se de que não há outros processos (ex. software antivírus) restringindo o acesso à pasta de cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumentar o armazenamento de cópias de sombra

As operações de backup podem falhar se houver espaço de armazenamento de cópia de sombra insuficiente necessário para proteger a fonte de dados. Para resolver esse problema, aumente o espaço de armazenamento de cópia de sombra no volume protegido usando vssadmin, conforme mostrado abaixo:

- Verifique o espaço de armazenamento da sombra atual a partir do prompt de comando elevado:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumente o espaço de armazenamento de sombras usando o comando abaixo:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Outro processo ou software antivírus bloqueando o acesso à pasta de cache

Se você tiver um software antivírus instalado no servidor, adicione as regras de exclusão necessárias à varredura antivírus para esses arquivos e pastas:  

- A pasta do arranhão. Sua localização padrão é C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Scratch
- A pasta bin em C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- Csc.exe

## <a name="common-issues"></a>Problemas comuns

Esta seção abrange os erros comuns que você encontra ao usar o agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Mensagem de erro | Ação recomendada |
-- | --
O agente de Serviços de Recuperação do Microsoft Azure não pôde acessar a soma de verificação de backup armazenada no local de rascunho | Para resolver esse problema, execute o abaixo e reinicie o servidor <br/> - [Verifique se há um antivírus ou outros processos bloqueando os arquivos de localização do arranhão](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se o local do arranhão é válido e acessível ao agente de Marte.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Mensagem de erro | Ação recomendada |
-- | --
O agente de Serviços de Recuperação do Microsoft Azure não pôde acessar a localização de rascunho para inicializar o VHD | Para resolver esse problema, execute o abaixo e reinicie o servidor <br/> - [Verifique se há um antivírus ou outros processos bloqueando os arquivos de localização do arranhão](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se o local do arranhão é válido e acessível ao agente de Marte.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Mensagem de erro | Ação recomendada |
-- | --
O backup falhou devido ao armazenamento insuficiente no volume onde a pasta de risco está localizada | Para resolver esse problema, verifique as etapas abaixo e tente novamente a operação:<br/>- [Certifique-se de que o agente MARS é mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verificar e resolver problemas de armazenamento que impactam o espaço de risco de backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Mensagem de erro | Ação recomendada |
-- | --
Não é possível encontrar as alterações em um arquivo. Isso pode ocorrer por vários motivos. Tente executar a operação novamente | Para resolver esse problema, verifique as etapas abaixo e tente novamente a operação:<br/> - [Certifique-se de que o agente MARS é mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verificar e resolver problemas de armazenamento que impactam o espaço de risco de backup](#prerequisites)

## <a name="next-steps"></a>Próximas etapas

- Obtenha mais detalhes sobre [como fazer backup do Windows Server com o agente de backup do Azure](tutorial-backup-windows-server-to-azure.md).
- Se você precisar restaurar um backup, consulte [restaurar arquivos em uma máquina Windows](backup-azure-restore-windows-server.md).
