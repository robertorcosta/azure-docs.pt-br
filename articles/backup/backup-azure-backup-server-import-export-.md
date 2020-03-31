---
title: Backup offline do DPM e do Servidor de Backup do Azure
description: Com o Azure Backup, você pode enviar dados da rede usando o serviço Azure Import/Export. Este artigo explica o fluxo de trabalho de backup off-line para o DPM e o Azure Backup Server.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197025"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho de backup off-line para dpm e azure backup server

O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. O Backup do Azure compacta os backups inicias. O processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup iniciais compactados de forma offline no Azure.

O processo de semeadura off-offline do Azure Backup é fortemente integrado com o [serviço De importação/exportação Do Azure](../storage/common/storage-import-export-service.md). Você pode usar este serviço para transferir dados para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup iniciais que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você pode usar o fluxo de trabalho de semeadura off-line para enviar a cópia inicial de backup em um ou mais discos rígidos para um data center do Azure. Este artigo fornece uma visão geral e outras etapas que terminam esse fluxo de trabalho para o DPM (System Center Data Protection Manager) e o Microsoft Azure Backup Server (MABS).

> [!NOTE]
> O processo de backup off-line para o Microsoft Azure Recovery Services (MARS) Agent é distinto do DPM e do MABS. Para obter informações sobre o uso de backup off-line com o Agente MARS, consulte [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md). O backup off-line não é suportado para backups de estado do sistema feitos usando o Azure Backup Agent.
>

## <a name="overview"></a>Visão geral

Com a capacidade de semeamento offline do Azure Backup e do serviço De importação/exportação do Azure, é simples carregar os dados off-line para o Azure usando discos. O processo de backup offline envolve as seguintes etapas:

> [!div class="checklist"]
>
> * Os dados de backup são gravados em um local de encenação em vez de serem enviados pela rede.
> * Os dados sobre o local de preparação são então gravados em um ou mais discos SATA usando o utilitário *AzureOfflineBackupDiskPrep.*
> * Um trabalho de importação do Azure é criado automaticamente pelo utilitário.
> * As unidades SATA são então enviadas para o data center Azure mais próximo.
> * Depois que o upload dos dados de backup no Azure for concluído, o Backup do Azure copia os dados de backup para o cofre de backup e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações com suporte

O backup off-line é suportado para todos os modelos de implantação do Azure Backup que faz backup de dados de on-premises para a nuvem da Microsoft. Estes modelos incluem:

> [!div class="checklist"]
>
> * Backup de arquivos e pastas com o Agente MARS ou o Agente de Backup do Azure.
> * Backup de todas as cargas de trabalho e arquivos com DPM.
> * Backup de todas as cargas de trabalho e arquivos com MABS.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos sejam atendidos antes de iniciar o fluxo de trabalho de backup off-line:

* Um [cofre de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) foi criado. Para criar um, siga as etapas em [Criar um cofre de serviços de recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Um Agente de Backup do Azure ou MABS ou DPM foi instalado no Windows Server ou em um cliente Windows, conforme aplicável, e o computador está registrado no cofre dos Serviços de Recuperação. Verifique se apenas a [versão mais recente do Backup do Azure](https://go.microsoft.com/fwlink/?linkid=229525) é usada.
* [Baixe o arquivo de configurações de publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) no computador do qual você planeja fazer backup de seus dados. A assinatura a partir da qual você baixa o arquivo de configurações de publicação pode ser diferente da assinatura que contém o cofre dos Serviços de Recuperação. Se sua assinatura estiver em nuvens soberanas do Azure, use os seguintes links conforme apropriado para baixar o arquivo de configurações de publicação do Azure.

    | Região da nuvem soberana | Link de arquivo de configurações de publicação do Azure |
    | --- | --- |
    | Estados Unidos | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Uma conta de armazenamento do Azure com o modelo de implantação do Gerenciador de recursos foi criada na assinatura a partir da qual você baixou o arquivo de configurações de publicação.

  ![Crie uma conta de armazenamento com o desenvolvimento do Resource Manager](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Um local de preparo, o que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criado. Por exemplo, se você quiser fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparação é de pelo menos 500 GB. (Um valor menor é usado devido à compactação).
* Para discos enviados ao Azure, certifique-se de que apenas os discos sata/iii de 2,5 polegadas ou 2,5 polegadas ou 3,5 polegadas sejam usados. Você pode usar discos rígidos de até 10 TB. Confira a [documentação da Importação/Exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para saber o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como *computador de cópia*) de onde é realizada a cópia de dados de backup do local de preparo para as unidades SATA. Verifique se o BitLocker está habilitado no computador de cópia.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Prepare o servidor para o processo de backup off-line

>[!NOTE]
> Se você não conseguir encontrar os utilitários listados, como *AzureOfflineBackupCertGen.exe*, na AskAzureBackupTeam@microsoft.com instalação do AGENTE MARS, escreva para ter acesso a eles.

* Abra um prompt de comando elevado no servidor e execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    A ferramenta cria um aplicativo de diretório ativo de backup offline do Azure se não existir.

    Se um aplicativo já existe, este executável pede que você carregue manualmente o certificado para o aplicativo no inquilino. Siga as etapas [nesta seção](#manually-upload-an-offline-backup-certificate) para carregar o certificado manualmente para o aplicativo.

* A ferramenta *AzureOfflineBackup.exe* gera um arquivo *OfflineApplicationParams.xml.* Copie este arquivo para o servidor com MABS ou DPM.
* Instale o [agente MARS mais recente](https://aka.ms/azurebackup_agent) na instância DPM ou no servidor de backup do Azure.
* Registre o servidor no Azure.
* Execute o comando a seguir:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* O comando anterior `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`cria o arquivo .

## <a name="manually-upload-an-offline-backup-certificate"></a>Faça upload manual de um certificado de backup off-line

Siga essas etapas para carregar manualmente o certificado de backup off-line para um aplicativo azure Active Directory criado anteriormente destinado ao backup off-line.

1. Entre no portal do Azure.
1. Vá para as inscrições **do Azure Active Directory** > **App**.
1. Na guia **Aplicativos De propriedade,** localize um `AzureOfflineBackup _<Azure User Id`aplicativo com o formato de nome de exibição .

    ![Localizar aplicativo na guia Aplicativos Próprios](./media/backup-azure-backup-import-export/owned-applications.png)

1. Selecione o aplicativo. Em **Gerenciar** no painel esquerdo, vá para **Certificados & segredos**.
1. Verifique se há certificados pré-existentes ou chaves públicas. Se não houver nenhum, você pode excluir o aplicativo com segurança selecionando o botão **Excluir** na página **Visão Geral** do aplicativo. Em seguida, você pode tentar novamente as etapas para preparar o servidor para o processo [de backup off-line](#prepare-the-server-for-the-offline-backup-process) e pular as etapas a seguir. Caso contrário, continue a seguir essas etapas a partir da instância DPM ou do servidor Azure Backup onde você deseja configurar backup off-line.
1. Selecione a **guia Gerenciar certificado** > de computador Guia `CB_AzureADCertforOfflineSeeding_<ResourceId>`**pessoal.** Procure o certificado com o nome .
1. Selecione o certificado, clique com o botão direito do mouse em **Todas as tarefas**e, em seguida, **selecione Exportar**, sem uma chave privada, no formato .cer.
1. Vá para o aplicativo de backup off-line do Azure no portal Azure.
1. Selecione **Gerenciar** > **certificados & segredos** > **Certificado de upload**. Faça o upload do certificado exportado na etapa anterior.

    ![Carregar o certificado](./media/backup-azure-backup-import-export/upload-certificate.png)

1. No servidor, abra o registro inserindo **regedit** na janela de execução.
1. Ir para a entrada de registro *Computador\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Clique com o botão direito do mouse no `AzureADAppCertThumbprint_<Azure User Id>` **CloudBackupProvider**e adicione um novo valor de string com o nome .

    >[!NOTE]
    > Para encontrar o ID do usuário do Azure, faça uma das seguintes etapas:
    >
    >* A partir do PowerShell conectado ao `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` Azure, execute o comando.
    >* Vá para o `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`caminho do registro .

1. Clique com o botão direito do mouse na seqüência adicionada na etapa anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou na etapa 7. Em seguida, selecione **OK**.
1. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione a guia **Detalhes** e role para baixo até ver o campo de impressão digital. Selecione **Impressão digital**e copie o valor.

    ![Copiar valor do campo de impressão digital](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Continue até a seção [Fluxo de trabalho](#workflow) para prosseguir com o processo de backup off-line.

## <a name="workflow"></a>Fluxo de trabalho

As informações nesta seção ajudam você a concluir o fluxo de trabalho de backup off-line para que seus dados possam ser entregues em um data center do Azure e carregados para o Azure Storage. Se você tiver dúvidas sobre o serviço de importação ou qualquer aspecto do processo, consulte a [documentação de visão geral](../storage/common/storage-import-export-service.md) do serviço de importação referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar o backup offline

1. Quando você agenda um backup, você vê a página seguinte no Windows Server, um cliente do Windows ou DPM.

    ![Página de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aqui está a página correspondente no DPM. <br/>
    
    ![Página de importação do DPM e do Azure Backup Server](./media/backup-azure-backup-import-export/dpmoffline.png)

    As caixas que você preenche são:

   * **Local de preparo**: o local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de encenação pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho completo da rede do local de preparação.
   * **Configurações de publicação do Azure**: O caminho local para o arquivo de configurações de publicação.
   * **Nome de trabalho de importação do Azure**: O nome exclusivo pelo qual o serviço de importação/exportação do Azure e o Backup do Azure acompanham a transferência de dados enviados em discos para o Azure.
   * **ID de assinatura do Azure**: O ID de assinatura do Azure para a assinatura de onde você baixou o arquivo de configurações de publicação do Azure.
   * **Azure Storage Account**: O nome da conta de armazenamento na assinatura do Azure associada ao arquivo de configurações de publicação do Azure.
   * **Contêiner de Armazenamento do Microsoft Azure**: o nome do blob de armazenamento de destino na conta de armazenamento do Azure onde os dados de backup são importados.

   Salve o **local de preparação** e as informações do Nome do Trabalho de Importação do **Azure** fornecidos. É necessário preparar os discos.

1. Termine o fluxo de trabalho. Para iniciar a cópia de backup offline, selecione **Fazer backup agora** no console de gerenciamento do Azure Backup Agent. O backup inicial é gravado na área de preparo como parte dessa etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no DPM ou no Azure Backup Server, clique com o botão direito do mouse no **Grupo de proteção**. Selecione a opção **Criar ponto de recuperação.** Em seguida, selecione a opção **Proteção Online.**

    ![DPM e MABS backup agora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco.

    ![Andamento do backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure

O *utilitário AzureOfflineBackupDiskPrep* é usado para preparar as unidades SATA enviadas para o data center Azure mais próximo. Este utilitário está disponível no diretório de instalação do Agente de Serviços de Recuperação no seguinte caminho:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Vá até o diretório e copie o diretório *AzureOfflineBackupDiskPrep* para um computador de cópia no qual as unidades SATA a serem preparadas estejam conectadas. Verifique se:

   * O computador de cópia pode acessar o local de preparação para o fluxo de trabalho de semeamento offline usando o mesmo caminho de rede fornecido no fluxo de trabalho na seção "Iniciar backup off-line".
   * O BitLocker está habilitado no computador de cópia.
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia também pode ser o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem é uma máquina virtual, então é obrigatório usar um servidor físico diferente ou uma máquina cliente como o computador de cópia.

1. Abra um prompt de comando elevado no computador de cópia com o diretório de *utilitárioS AzureOfflineBackupDiskPrep* como o diretório atual. Execute o comando a seguir:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*Caminho de localização de estadiamento*&gt; |Esta entrada obrigatória é usada para fornecer o caminho para o local de preparação que você inseriu no fluxo de trabalho na seção "Iniciar backup off-line". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |Esta entrada opcional é usada para fornecer o caminho para o arquivo de configurações de publicação do Azure que você inseriu no fluxo de trabalho na seção "Iniciar backup off-line". |

    > [!NOTE]
    > O valor &lt;Caminho para AzurePublishSettingFile&gt; será obrigatório quando o computador de cópia e o computador de origem forem diferentes.
    >
    >

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de encenação fornecido, você verá uma página como esta.

    ![Entrada da ferramenta de preparação do disco Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Insira a letra da unidade sem os dois pontos à direita do disco montado que você deseja preparar para a transferência para o Azure. Quando solicitado, forneça confirmação para a formatação da unidade.

    A ferramenta então começa a preparar o disco e copiar os dados de backup. Você pode precisar anexar discos adicionais quando solicitado pela ferramenta no caso de o disco fornecido não ter espaço suficiente para os dados de backup. <br/>

    Depois que a ferramenta terminar com sucesso, um ou mais discos fornecidos estão preparados para envio ao Azure. Um trabalho de importação com o nome fornecido durante o fluxo de trabalho na seção "Iniciar backup off-line" também é criado no Azure. Por fim, a ferramenta exibe o endereço de entrega para o datacenter do Azure para onde os discos precisam ser enviados.

    ![Preparação do disco Azure concluída](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. No final da execução do comando, você também vê a opção de atualizar as informações de envio.

    ![Atualizar opção de informações de envio](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. Você pode inserir os detalhes imediatamente. A ferramenta orienta você através do processo que envolve uma série de entradas. Se você não tiver informações como o número de rastreamento ou outros detalhes relacionados ao envio, você pode terminar a sessão. As etapas para atualizar os detalhes do envio posteriormente são fornecidas neste artigo.

1. Enviar os discos para o endereço que a ferramenta forneceu. Mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT]
   > Nenhum dos dois empregos de importação do Azure pode ter o mesmo número de rastreamento. Certifique-se de que as unidades preparadas pelo utilitário um único trabalho de importação do Azure sejam enviadas juntas em um único pacote e que haja um único número de rastreamento exclusivo para o pacote. Não combine unidades preparadas como parte de diferentes trabalhos de importação do Azure em um único pacote.

1. Quando você tiver as informações do número de rastreamento, vá para o computador de origem, que está aguardando a conclusão do trabalho de importação. Execute o seguinte comando em um prompt de comando elevado com o diretório de *utilitárioS AzureOfflineBackupDiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Você pode, opcionalmente, executar o seguinte comando de um computador diferente, como o computador de cópia, com o diretório utilitário *AzureOfflineBackupDiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | Descrição |
    | --- | --- |
    | u: | Esta entrada obrigatória é usada para atualizar os detalhes de envio para um trabalho de importação do Azure. |
    | s:&lt;*Caminho de localização de estadiamento*&gt; | Esta entrada obrigatória é usada quando o comando não é executado no computador de origem. Ele é usado para fornecer o caminho para o local de preparação que você inseriu no fluxo de trabalho na seção "Iniciar backup off-line". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; | Esta entrada obrigatória é usada quando o comando não é executado no computador de origem. Ele é usado para fornecer o caminho para o arquivo de configurações de publicação do Azure que você inseriu no fluxo de trabalho na seção "Iniciar backup off-line". |

    O utilitário detecta automaticamente o trabalho de importação que o computador de origem está esperando ou os trabalhos de importação associados ao local de preparação quando o comando é executado em um computador diferente. Em seguida, ele fornece a opção de atualizar informações de envio através de uma série de entradas.

    ![Insira informações de envio](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Depois que todas as entradas forem fornecidas, revise os detalhes cuidadosamente e comprometa as informações de envio fornecidas inserindo **sim**.

    ![Revisar informações de envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Depois que as informações de envio são atualizadas com sucesso, o utilitário fornece um local onde os detalhes de envio inseridos são armazenados.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Certifique-se de que as unidades cheguem ao data center do Azure dentro de duas semanas após fornecer as informações de envio usando o utilitário *AzureOfflineBackupDiskPrep.* A falha em fazer isso pode resultar no não processamento das unidades. 

Depois de concluir as etapas anteriores, o datacenter do Azure está pronto para receber as unidades e processá-las ainda mais para transferir os dados de backup das unidades para a conta de armazenamento Azure do tipo clássico que você criou.

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades

O tempo necessário para processar um trabalho de importação do Azure varia. O tempo do processo depende de fatores como tempo de envio, tipo de trabalho, tipo e tamanho dos dados copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA. Depois que os discos são recebidos, o serviço se esforça para concluir a cópia de dados de backup na sua conta de armazenamento Do Zure em 7 a 10 dias. A próxima seção descreve como você pode monitorar o status do trabalho de importação do Azure.

### <a name="monitor-azure-import-job-status"></a>Monitore o status de trabalho de importação do Azure

Enquanto suas unidades estão em trânsito ou no datacenter do Azure para serem copiadas para a conta de armazenamento, o Azure Backup Agent ou DPM ou o console MABS no computador de origem mostram o seguinte status de trabalho para seus backups agendados:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Para verificar o status do trabalho de importação:

1. Abra um prompt de comando elevado no computador de origem e execute o seguinte comando:

     `AzureOfflineBackupDiskPrep.exe u:`

1. A saída mostra o status atual do trabalho de importação.

    ![Verifique o status do trabalho de importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os vários estados do trabalho de importação do Azure, consulte [Exibir o status dos empregos de importação/exportação do Azure](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Termine o fluxo de trabalho

Após a conclusão da importação, os dados de backup inicias estarão disponíveis na sua conta de armazenamento. No momento do próximo backup programado, o Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

   ![Copiar dados para o cofre dos Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No horário do próximo backup agendado, o Backup do Azure executa o backup incremental sobre a cópia de backup inicial.

## <a name="next-steps"></a>Próximas etapas

* Para obter quaisquer dúvidas sobre o fluxo de trabalho do serviço de importação/exportação do Azure, consulte [Use o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento Blob](../storage/common/storage-import-export-service.md).
