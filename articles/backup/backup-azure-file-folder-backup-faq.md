---
title: Backup de arquivos e pastas - perguntas comuns
description: Aborda perguntas comuns sobre backup de arquivos e pastas com o Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 6e9f265672ff15e40444a46a3e440e73a0051a5b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254743"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Perguntas comuns sobre backup de arquivos e pastas

Este artigo responde a perguntas comuns que abundam em backup de arquivos e pastas com o Agente de Serviços de Recuperação (MARS) do Microsoft Azure no serviço de backup do [Azure.](backup-overview.md)

## <a name="configure-backups"></a>Configurar backups

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso baixar a versão mais recente do agente MARS?

O mais recente agente MARS usado ao fazer backup de máquinas do Windows Server, DPM do Centro de Sistema e servidor de backup do Microsoft Azure está disponível para [download](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Quanto tempo as credenciais do cofre são válidas?

As credenciais do cofre expiram após 10 dias. Se o arquivo de credenciais expirar, baixe o arquivo novamente do portal Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>A partir de quais drives posso fazer backup de arquivos e pastas?

Você não pode fazer backup dos seguintes tipos de drives e volumes:

* Mídia removível: Todas as fontes de itens de backup devem reportar como corrigidas.
* Volumes somente leitura: O volume deve ser escrito para que o serviço de cópia de sombra de volume (VSS) funcione.
* Volumes offline: O volume deve estar on-line para que o VSS funcione.
* Compartilhamentos de rede: O volume deve ser local para que o servidor seja feito backup usando backup on-line.
* Volumes protegidos pelo BitLocker: O volume deve ser desbloqueado antes que o backup possa ocorrer.
* Identificação do sistema de arquivos: NTFS é o único sistema de arquivos com suporte.

### <a name="what-file-and-folder-types-are-supported"></a>Quais tipos de arquivos e pastas são suportados?

[Saiba mais](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sobre os tipos de arquivos e pastas suportadas para backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Posso usar o agente MARS para fazer backup de arquivos e pastas em uma VM Azure?  

Sim. O Azure Backup fornece backup em nível VM para VMs do Azure usando a extensão VM para o agente Azure VM. Se você quiser fazer backup de arquivos e pastas no sistema operacional Windows convidado na VM, você pode instalar o agente MARS para fazer isso.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Posso usar o agente MARS para fazer backup de arquivos e pastas no armazenamento temporário para o Azure VM?

Sim. Instale o agente MARS e fazer backup de arquivos e pastas no sistema operacional Windows convidado para armazenamento temporário.

* Os trabalhos de backup falham quando os dados de armazenamento temporários são eliminados.
* Se os dados de armazenamento temporários forem excluídos, você só poderá restaurar o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como cadastrar um servidor em outra região?

Os dados de backup são enviados para o datacenter do cofre no qual o servidor está registrado. A maneira mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registrar a máquina em um novo cofre na região que você precisa.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O agente MARS suporta a deduplicação do Windows Server 2012?

Sim. O agente MARS converte os dados duplicados em dados normais quando prepara a operação de backup. Em seguida, otimiza os dados para backup, criptografa os dados e, em seguida, envia os dados criptografados para o cofre.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Preciso de permissões do administrador para instalar e configurar o agente MARS?

Sim, a instalação do Agente MARS e a configuração de backups usando o console MARS precisam que o usuário seja um administrador local no servidor protegido.

## <a name="manage-backups"></a>Gerenciar backups

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu renomear uma máquina Windows configurada para backup?

Quando você renomeia uma máquina Windows, todos os backups configurados no momento são interrompidos.

* Você precisa registrar o novo nome da máquina no cofre de backup.
* Quando você registra o novo nome no cofre, a primeira operação é um backup *completo.*
* Se você precisar recuperar dados em backup no cofre com o nome do servidor antigo, use a opção para restaurar um local alternativo no Assistente de dados de recuperação. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual é o comprimento máximo do caminho do arquivo para backup?

O agente MARS confia no NTFS e usa a especificação de comprimento do filepath limitada pela [API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)do Windows . Se os arquivos que você deseja proteger forem mais longos do que o valor permitido, faça backup da pasta-mãe ou da unidade de disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quais caracteres são permitidos em caminhos de arquivos?

O agente MARS conta com NTFS e permite [caracteres suportados](/windows/desktop/FileIO/naming-a-file#naming-conventions) em nomes/caminhos de arquivos.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>O aviso "Azure Backups" não foi configurado para este servidor" é exibido

Esse aviso pode aparecer mesmo que você tenha configurado uma diretiva de backup, quando as configurações de agendamento de backup armazenadas no servidor local não são as mesmas que as configurações armazenadas no cofre de backup.

* Quando o servidor ou as configurações forem recuperados para um estado bom conhecido, os horários de backup podem ser dessincronizados.
* Se você receber esse aviso, [configure](backup-azure-manage-windows-server.md) a diretiva de backup novamente e execute um backup sob demanda para ressincronizar o servidor local com o Azure.

## <a name="manage-the-backup-cache-folder"></a>Gerenciar a pasta de cache de backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta de cache?

O tamanho da pasta de cache determina a quantidade de dados submetida a backup.

* Os volumes da pasta de cache devem ter espaço livre que equivale a pelo menos 5-10% do tamanho total dos dados de backup.
* Se o volume tiver menos de 5% de espaço livre, aumente o tamanho do volume ou mova a pasta de cache para um volume com espaço suficiente seguindo [estas etapas](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Se você fizer backup do Windows System State, precisará de um espaço livre adicional de 30-35 GB no volume que contém a pasta cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Como verificar se a pasta scratch é válida e acessível?

1. Por padrão, a pasta de risco está localizada em`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Certifique-se de que o caminho do local da pasta de risco corresponda aos valores das entradas de chave de registro mostradas abaixo:

    | Caminho do registro | Chave do Registro | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Novo local da pasta de cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Novo local da pasta de cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como altero a localização do cache para o agente MARS?

1. Execute este comando em um prompt de comando elevado para parar o mecanismo de backup:

    ```Net stop obengine```
2. Se você tiver configurado backup do Estado do sistema, abra o gerenciamento `"CBSSBVol_<ID>"`de disco e desmonte o disco com nomes no formato .
3. Por padrão, a pasta de risco está localizada em`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Copie toda `\Scratch` a pasta para uma unidade diferente que tenha espaço suficiente. Certifique-se de que o conteúdo é copiado, não movido.
5. Atualize as seguintes entradas de registro com o caminho da pasta de risco recém-movida.

    | Caminho do registro | Chave do Registro | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Novo local da pasta de arranhão* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Novo local da pasta de arranhão* |

6. Reinicie o mecanismo de backup em um prompt de comando elevado:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Executar um backup sob demanda. Depois que o backup terminar com sucesso usando o novo local, você pode remover a pasta de cache original.

### <a name="where-should-the-cache-folder-be-located"></a>Onde a pasta de cache deve ser localizada?

Os seguintes locais para a pasta cache não são recomendados:

* Compartilhamento de rede/mídia removível: A pasta cache deve ser local para o servidor que precisa fazer backup usando backup on-line. Locais de rede ou mídia removível, como unidades USB, não são suportados.
* Volumes offline: A pasta de cache deve estar on-line para backup esperado usando o Azure Backup Agent

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existem atributos da pasta cache que não são suportados?

Os atributos a seguir, ou suas combinações, não têm suporte para a pasta de cache:

* Criptografado
* Eliminação de duplicação
* Compressed
* Esparsos
* Ponto de nova análise

A pasta de cache e o VHD de metadados não têm os atributos necessários para o agente de backup do Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existe uma maneira de ajustar a quantidade de largura de banda usada para backup?

Sim, você pode usar a opção **Alterar propriedades** no agente MARS para ajustar a largura de banda e o tempo. [Saiba mais](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Restaurar

### <a name="manage"></a>Gerenciar

**Posso me recuperar se esqueci minha senha?**
O agente de backup do Azure requer uma senha (que você forneceu durante o registro) para descriptografar os dados de backup durante a restauração. Reveja os cenários abaixo para entender suas opções para lidar com uma senha perdida:

| Máquina Original <br> *(máquina de origem para onde os backups foram feitos)* | Senha | Opções Disponíveis |
| --- | --- | --- |
| Disponível |Excluído |Se a sua máquina original (onde os backups foram feitos) estiver disponível e ainda estiver registrada no mesmo cofre dos Serviços de Recuperação, então você poderá regenerar a senha seguindo [estas etapas](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase).  |
| Excluído |Excluído |Não é possível recuperar os dados ou dados não estão disponíveis |

Considere as seguintes condições:

* Se você desinstalar e reregistrar o agente na mesma máquina original com
  * *Mesma senha,* então você poderá restaurar seus dados de backup.
  * *Frase-senha diferente,* então você não será capaz de restaurar seus dados de backup.
* Se você instalar o agente em uma *máquina diferente* com
  * *Mesma senha* (usada na máquina original), então você poderá restaurar seus dados de backup.
  * *Frase-senha diferente,* você não será capaz de restaurar seus dados de backup.
* Se a sua máquina original estiver corrompida (impedindo-o de regenerar a senha através do console MARS), mas você pode restaurar ou acessar a pasta de arranhão original usada pelo agente MARS, então você pode ser capaz de restaurar (se você esqueceu a senha). Para obter mais assistência, entre em contato com o Suporte ao Cliente.

**Como me recuperar se perdi minha máquina original (para onde foram feitos backups)?**

Se você tiver a mesma senha (que você forneceu durante o registro) da máquina original, então você pode restaurar os dados de backup para uma máquina alternativa. Reveja os cenários abaixo para entender suas opções de restauração.

| Máquina Original | Senha | Opções Disponíveis |
| --- | --- | --- |
| Excluído |Disponível |Você pode instalar e registrar o agente MARS em outra máquina com a mesma senha que você forneceu durante o registro da máquina original. Escolha **opção** > de recuperação**Outro local** para executar sua restauração. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Excluído |Excluído |Não é possível recuperar os dados ou dados não estão disponíveis |

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontecerá se eu cancelar um trabalho de restauração em andamento?

Se um trabalho de restauração em andamento for cancelado, o processo de restauração será cancelado. Todos os arquivos restaurados antes do cancelamento permanecem no destino configurado (local original ou alternativo), sem quaisquer reversões.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>O agente MARS faz backup e restaura ACLs definidos em arquivos, pastas e volumes?

* O agente MARS faz backup de ACLs definidos em arquivos, pastas e volumes
* Para a opção de recuperação de restauração de volume, o agente MARS oferece uma opção para pular as permissões de restauração da ACL para o arquivo ou pasta que está sendo recuperado
* Para a opção de recuperação de arquivos e pastas individuais, o agente MARS restaurará com permissões ACL (não há opção de pular a restauração da ACL).

## <a name="next-steps"></a>Próximas etapas

[Aprenda](tutorial-backup-windows-server-to-azure.md) a fazer backup de uma máquina windows.
