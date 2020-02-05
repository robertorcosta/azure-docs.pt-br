---
title: Backup offline usando o Azure Data Box
description: Saiba como você pode usar Azure Data Box para propagar grandes dados de backup inicial offline do agente MARS para um cofre dos serviços de recuperação do Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027024"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Backup do Azure offline-backup usando Azure Data Box

Você pode usar o serviço de [Azure data Box](https://docs.microsoft.com/azure/databox/data-box-overview) para propagar seus grandes backups Mars iniciais offline (sem usar a rede) para um cofre dos serviços de recuperação do Azure.  Isso economiza tempo e largura de banda de rede, que, de outra forma, seriam consumidos movendo grandes quantidades de dados de backup online em uma rede de alta latência. No momento, esse aprimoramento está em versão prévia. O backup offline baseado em Azure Data Box fornece duas vantagens distintas sobre o [backup offline baseado no serviço de importação/exportação do Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Não é necessário adquirir seus próprios discos e conectores compatíveis com o Azure. Azure Data Box serviço envia os discos associados à SKU de [Data Box](https://azure.microsoft.com/services/databox/data/) selecionada

2. O backup do Azure (agente MARS) pode gravar dados de backup diretamente nas SKUs com suporte do Azure Data Box. Isso elimina a necessidade de provisionar um local de preparo para seus dados de backup inicial e a necessidade de utilitários para formatar e copiar esses dados nos discos.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box com agente MARS

Este artigo explica como você pode usar Azure Data Box para propagar grandes dados de backup inicial offline do agente MARS para um cofre dos serviços de recuperação do Azure. O artigo está dividido nas seguintes partes:

* Plataformas com suporte
* SKUs Data Box com suporte
* Pré-requisitos
* Configurar agente MARS
* Azure Data Box de instalação
* Transferência de dados de backup para Azure Data Box
* Etapas após o backup

## <a name="supported-platforms"></a>Plataformas com suporte

O processo para propagar dados do agente MARS usando o Azure Data Box tem suporte nos seguintes SKUs do Windows:

| **SO**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Estação**                        |                                                              |
| Windows de 10 64 bits                     | Enterprise, pro, Home                                       |
| Windows 8.1 de 64 bits                    | Enterprise, pro                                             |
| Windows de 8 64 bits                      | Enterprise, pro                                             |
| Windows de 7 64 bits                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Servidor**                             |                                                              |
| Windows Server 2019 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 bits            | Datacenter, Fundação, Standard                            |
| Windows Storage Server 2016 64 bits    | Standard, grupo de trabalho                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, essencial                              |
| Windows Storage Server 2012 64 bits    | Standard, grupo de trabalho                                         |
| Windows Server 2008 R2 SP1 64 bits     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bits        | Standard, Enterprise e Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho dos dados de backup e SKUs de Data Box com suporte

| Tamanho dos dados de backup (compactação post por MARS) * por servidor | SKU Azure Data Box com suporte                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Disco do Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB e < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* As taxas de compactação típicas variam entre 10-20% <br>
\* * Alcance para [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) se você espera ter mais de 80 TB de dados de backup inicial para um único servidor Mars.

>[!IMPORTANT]
>Os dados de backup iniciais de um único servidor devem estar contidos em um único Azure Data Box ou Disco do Azure Data Box e não podem ser compartilhados entre vários dispositivos do mesmo ou de SKUs diferentes. No entanto, um dispositivo Azure Data Box pode conter backups iniciais de vários servidores.

## <a name="pre-requisites"></a>Pré-requisitos

### <a name="azure-subscription-and-required-permissions"></a>Assinatura do Azure e permissões necessárias

* O processo requer uma assinatura do Azure
* O processo requer que o usuário designado para executar a política de backup offline seja um "proprietário" da assinatura do Azure
* O trabalho de Data Box e o cofre dos serviços de recuperação (para o qual os dados precisam ser propagados) precisam estar nas mesmas assinaturas.
* É recomendável que a conta de armazenamento de destino associada ao trabalho de Azure Data Box e o cofre dos serviços de recuperação estejam na mesma região. No entanto, isso não é necessário.

### <a name="get-azure-powershell-370"></a>Obter Azure PowerShell 3.7.0

**Esse é o pré-requisito mais importante para o processo**. Antes de instalar o Azure PowerShell (ver. 3.7.0), execute as seguintes verificações * *

#### <a name="step-1-check-powershell-version"></a>Etapa 1: verificar a versão do PowerShell

* Abra PowerShell e execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Se a saída exibir uma versão superior a **3.7.0**, execute a etapa 2 abaixo. Caso contrário, pule para a Etapa 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Etapa 2: desinstalar a versão do PowerShell

Desinstale a versão atual do PowerShell fazendo as seguintes ações:

* Remova os módulos dependentes executando o seguinte comando no PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Execute o seguinte comando para garantir a exclusão bem-sucedida de todos os módulos dependentes:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Etapa 3: instalar o PowerShell versão 3.7.0

Depois de verificar se nenhum módulo AzureRM está presente, instale a versão 3.7.0 usando um dos seguintes métodos:

* No GitHub, [link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

OU

* Execute o seguinte comando na janela do PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell também pode ter sido instalado usando um arquivo MSI. Para removê-lo, desinstale-o usando a opção desinstalar programas no painel de controle.

### <a name="order-and-receive-the-data-box-device"></a>Solicitar e receber o dispositivo Data Box

O processo de backup offline usando MARS e Azure Data Box requer que os dispositivos Data Box necessários estejam no estado "entregue" antes de disparar o backup offline usando o agente MARS. Consulte o [tamanho dos dados de backup e os SKUs de data box com suporte](#backup-data-size-and-supported-data-box-skus) para solicitar o SKU mais adequado para seu requisito. Siga as etapas neste [artigo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) para solicitar e receber seus dispositivos data box.

>[!IMPORTANT]
>Não selecione BlobStorage para o tipo de conta. O agente MARS requer uma conta que dê suporte a blobs de página que não tem suporte quando BlobStorage está selecionado. É altamente recomendável selecionar *armazenamento v2* (*uso geral v2*) como o tipo de conta ao criar a conta de armazenamento de destino para seu trabalho de Azure data box.

![Escolher o tipo de conta nos detalhes da instância](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Instalar e configurar o agente MARS

* Certifique-se de desinstalar todas as instalações anteriores do agente MARS.

* Baixe o agente MARS mais recente [aqui](https://aka.ms/azurebackup_agent).

* Execute *MARSAgentInstaller. exe* e execute**apenas** as etapas para [instalar e registrar o agente](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) no cofre dos serviços de recuperação no qual você deseja que os backups sejam armazenados.

  >[!NOTE]
  > O cofre dos serviços de recuperação deve estar na mesma assinatura que o trabalho de Azure Data Box.

* Depois que o agente estiver registrado no cofre dos serviços de recuperação, siga as etapas nas seções abaixo.

## <a name="setup-azure-data-box-devices"></a>Configurar dispositivos Azure Data Box

Dependendo do SKU do Azure Data Box que você solicitou, execute as etapas abordadas nas seções apropriadas abaixo para configurar e preparar os dispositivos de Data Box para o agente MARS identificar e transferir os dados de backup iniciais.

### <a name="setup-azure-data-box-disk"></a>Disco do Azure Data Box de instalação

Se você solicitou um ou mais discos de Azure Data Box (até 8 TB cada), siga as etapas mencionadas aqui para [desempacotar, conectar e desbloquear seu disco do data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>É possível que o servidor com o agente MARS não tenha uma porta USB. Nessa situação, você pode conectar seu Disco do Azure Data Box a outro servidor/cliente e expor a raiz do dispositivo como um compartilhamento de rede.

### <a name="setup-azure-data-box"></a>Azure Data Box de instalação

Se você solicitou um Azure Data Box (até 100 TB), siga as etapas mencionadas aqui [para configurar seu data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Monte seu Azure Data Box como sistema local

O agente MARS opera no contexto do sistema local e, portanto, requer que o mesmo nível de privilégio seja fornecido ao caminho de montagem em que a Azure Data Box está conectada. Siga as etapas abaixo para garantir que você possa montar seu dispositivo de Data Box como sistema local usando o protocolo NFS:

* Habilite o recurso Client for NFS no Windows Server (que tem o agente MARS instalado).<br>
  Especificar origem alternativa: *wim: D: \sources\install.wim: 4*

* Baixe o PSExec do <https://download.sysinternals.com/files/PSTools.zip> para o servidor com o agente MARS instalado.

* Abra um prompt de comando com privilégios elevados e execute o seguinte comando com o diretório que contém o PSExec. exe como o diretório atual:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* A janela de comando que é aberta como resultado do comando acima está no contexto do sistema local. Use esta janela de comando para executar as etapas para montar o compartilhamento de blob de páginas do Azure como uma unidade de rede no seu Windows Server.

* Siga as etapas [aqui](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) para conectar o servidor com o agente Mars ao dispositivo de data Box via NFS e execute o seguinte comando no prompt de comando do sistema local para montar o compartilhamento de blobs de páginas do Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Depois de montado, verifique se você pode acessar o X: do seu servidor. Em caso afirmativo, continue com a próxima seção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados de backup inicial para Azure Data Box dispositivo (s)

* Abra o aplicativo **backup do Microsoft Azure** no servidor.

* Clique em **agendar backup** no painel **ações** .

    ![Clique em agendar backup](./media/offline-backup-azure-data-box/schedule-backup.png)

* Siga as etapas no **Assistente de agendamento de backup**

* **Adicione itens** de modo que o tamanho total dos itens esteja dentro dos [limites de tamanho com suporte pelo SKU Azure data Box](#backup-data-size-and-supported-data-box-skus) que você solicitou e recebeu.

    ![Adicionar itens ao backup](./media/offline-backup-azure-data-box/add-items.png)

* Selecione a agenda de backup e a política de retenção apropriadas para arquivos e pastas e estado do sistema (o estado do sistema é aplicável somente para servidores Windows e não para clientes Windows)

* Na tela **escolher tipo de backup inicial (arquivos e pastas)** do assistente, escolha a opção **transferir usando Microsoft Azure discos Data Box** e clique em **Avançar**

    ![Escolher tipo de backup inicial](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Entre no Azure quando solicitado usando as credenciais de usuário que têm acesso de proprietário na assinatura do Azure. Depois de fazer isso, você deverá ver uma tela semelhante à seguinte:

    ![Criando recursos e aplicando as permissões necessárias](./media/offline-backup-azure-data-box/creating-resources.png)

* O agente MARS irá buscar os trabalhos de Data Box na assinatura que estão no estado "entregue".

    ![Buscando trabalhos do data box para a ID da assinatura](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Selecione a ordem de caixa de dados correta para a qual você desempacotau, conectou e desbloqueou o disco Data Box. Clique em **Próximo**.

    ![Selecionar ordem (ões) Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

* Clique em **detectar dispositivo** na tela de **detecção de dispositivo data Box** . Isso faz com que o agente MARS Verifique os discos Azure Data Box conectados localmente e os detecte conforme mostrado abaixo:

    ![Detecção de dispositivo Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se você conectou o Azure Data Box como um compartilhamento de rede (devido à indisponibilidade de portas USB ou porque você solicitou e montou o dispositivo de 100 TB Data Box), a detecção falhará inicialmente, mas lhe dará a opção de inserir o caminho de rede para o dispositivo Data Box como s hown abaixo:

    ![Insira o caminho de rede](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do disco de Azure Data Box. Esse diretório deve conter um diretório com o nome *PageBlob* , como mostrado abaixo:
    >
    >![Diretório raiz de Azure Data Box disco](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Por exemplo, se o caminho do disco for `\\mydomain\myserver\disk1\` e *DISK1* contiver um diretório chamado *PageBlob*, o caminho a ser fornecido no assistente do agente Mars será `\\mydomain\myserver\disk1\`
    >
    >Se você [configurar um dispositivo Azure Data Box 100 TB](#setup-azure-data-box), forneça o seguinte como o caminho de rede para o dispositivo `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Clique em **Avançar** e em **concluir** na próxima tela para salvar a política de backup e retenção com a configuração de backup offline usando o Azure data box.

* A tela a seguir confirma que a política foi salva com êxito:

    ![A política foi salva com êxito](./media/offline-backup-azure-data-box/policy-saved.png)

* Clique em **fechar** na tela acima.

* Clique em ***fazer backup agora** no painel **ações** do console do agente Mars e clique em **fazer backup** na tela do assistente, conforme mostrado abaixo:

    ![Assistente de backup agora](./media/offline-backup-azure-data-box/backup-now.png)

* O agente MARS começará a fazer backup dos dados selecionados para o dispositivo Azure Data Box. Isso pode levar de várias horas a alguns dias, dependendo do número de arquivos e da velocidade de conexão entre o servidor com o agente MARS e o Disco do Azure Data Box.

* Depois que o backup dos dados for concluído, você verá uma tela no agente MARS semelhante à seguinte:

    ![Progresso do backup mostrado](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Etapas após o backup

Esta seção explica as etapas a serem executadas quando o backup dos dados para o Disco do Azure Data Box for bem-sucedido.

* Siga as etapas neste artigo para [enviar o disco Azure data box para o Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Se você usou um dispositivo Azure Data Box 100-TB, siga estas etapas para [enviar o Azure data box para o Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Monitore o trabalho de data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) no portal do Azure. Depois que o trabalho de Azure Data Box for "concluído", o agente MARS moverá automaticamente os dados da conta de armazenamento para o cofre dos serviços de recuperação no momento do próximo backup agendado. Em seguida, ele marcará o trabalho de backup como "trabalho concluído" se um ponto de recuperação for criado com êxito.

    >[!NOTE]
    >O agente MARS irá disparar backups nos horários agendados durante a criação da política. No entanto, esses trabalhos sinalizarão "aguardando Azure Data Box trabalho seja concluído" até a hora em que o trabalho for concluído.

* Depois que o agente MARS criar com êxito um ponto de recuperação correspondente ao backup inicial, você poderá excluir a conta de armazenamento (ou o conteúdo específico) associado ao trabalho de Azure Data Box.

## <a name="troubleshooting"></a>Solução de problemas

O agente de Backup do Microsoft Azure (MAB) cria um aplicativo do Azure AD para você em seu locatário. Este aplicativo requer um certificado para autenticação que é criado e carregado ao configurar a política de propagação offline. Usamos Azure PowerShell para criar e carregar o certificado para o aplicativo do Azure AD.

### <a name="issue"></a>Problema

No momento da configuração do backup offline, você pode enfrentar um problema, em que devido a um bug no cmdlet Azure PowerShell, não é possível adicionar vários certificados ao mesmo aplicativo do Azure AD criado pelo agente MAB. Isso afetará você se você tiver configurado a política de propagação offline para o mesmo servidor ou para outro.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Como verificar se o problema é causado por essa causa raiz específica

Para garantir que a falha seja devido ao problema acima, execute uma das seguintes etapas:

#### <a name="step-1"></a>Etapa 1

Verifique se você vê a seguinte mensagem de erro no console do MAB no momento da configuração do backup offline:

![Não é possível criar a política de backup offline para a conta atual do Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Etapa 2

* Abra a pasta **Temp** no caminho de instalação (o caminho padrão da pasta Temp é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Procure o arquivo **CBUICurr** e abra o arquivo.

* No arquivo **CBUICurr** , role até a última linha e verifique se a falha ocorre devido a `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Solução alternativa

Como solução alternativa para resolver esse problema, execute as etapas a seguir e repita a configuração da política.

#### <a name="first-step"></a>Primeira etapa

Entre no PowerShell que aparece na interface do usuário do amMAB usando uma conta diferente com acesso de administrador na assinatura que terá o trabalho de exportação de importação criado.

#### <a name="second-step"></a>Segunda etapa

Se nenhum outro servidor tiver a propagação offline configurada e nenhum outro servidor depender do aplicativo `AzureOfflineBackup_<Azure User Id>`, exclua esse aplicativo do **portal do Azure** > **Azure Active Directory** > registros de aplicativo **.**

>[!NOTE]
> Verifique se o aplicativo `AzureOfflineBackup_<Azure User Id>` não tem nenhuma outra propagação offline configurada e também nenhum outro servidor depende desse aplicativo. Vá para **configurações** > **chaves** na seção **chaves públicas** ela não deve ter outras chaves públicas adicionadas. Consulte a seguinte captura de tela para referência:
>
>![Chaves públicas](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Terceira etapa

No servidor em que você está tentando configurar o backup offline, execute as seguintes ações:

1. Abra a guia **gerenciar o aplicativo de certificado do computador** > **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Selecione o certificado acima, clique com o botão direito do mouse em **todas as tarefas** e **exporte** sem chave privada, no formato. cer.

3. Vá para o aplicativo de backup offline do Azure mencionado no **ponto 2**. Nas **configurações** > **chaves** > **carregar chave pública,** carregue o certificado exportado na etapa anterior.

    ![Carregar chave pública](./media/offline-backup-azure-data-box/upload-public-key.png)

4. No servidor, abra o registro digitando **regedit** na janela Executar.

5. Vá para o computador do registro *\ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Clique com o botão direito do mouse em **CloudBackupProvider** e adicione um novo valor de cadeia de caracteres com o nome `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Para obter a ID de usuário do Azure, execute uma das seguintes ações:
    >
    >1. No PowerShell conectado do Azure, execute o comando `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > 2. Navegue até o caminho do registro: *computador \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Nome: *CurrentUserId*

6. Clique com o botão direito do mouse na cadeia de caracteres adicionada na etapa acima e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou no **ponto 2** e clique em **OK**.

7. Para obter o valor de impressão digital, clique duas vezes no certificado e, em seguida, selecione a guia **detalhes** e role para baixo até ver o campo impressão digital. Clique em **impressão digital** e copie o valor.

    ![Campo de impressão digital do certificado](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Perguntas

Para quaisquer dúvidas ou esclarecimentos, em relação a problemas enfrentados, acesse [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
