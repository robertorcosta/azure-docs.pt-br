---
title: Backup offline do DPM e do Servidor de Backup do Azure
description: Com o backup do Azure, você pode enviar dados da rede usando o serviço de importação/exportação do Azure. Este artigo explica o fluxo de trabalho de backup offline para o DPM e o Servidor de Backup do Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197025"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho de backup offline para o DPM e Servidor de Backup do Azure

O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. O Backup do Azure compacta os backups inicias. O processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup iniciais compactados de forma offline no Azure.

O processo de propagação offline do backup do Azure é totalmente integrado ao serviço de [importação/exportação do Azure](../storage/common/storage-import-export-service.md). Você pode usar esse serviço para transferir dados para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup inicial que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você poderá usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um datacenter do Azure. Este artigo fornece uma visão geral e etapas adicionais que concluem esse fluxo de trabalho para o System Center Data Protection Manager (DPM) e o Backup do Microsoft Azure Server (MABS).

> [!NOTE]
> O processo de backup offline para o agente de Serviços de Recuperação do Microsoft Azure (MARS) é diferente do DPM e do MABS. Para obter informações sobre como usar o backup offline com o agente MARS, consulte [fluxo de trabalho de backup offline no backup do Azure](backup-azure-backup-import-export.md). O backup offline não tem suporte para backups de estado do sistema feito usando o agente de backup do Azure.
>

## <a name="overview"></a>Visão geral

Com a capacidade de propagação offline do backup do Azure e o serviço de importação/exportação do Azure, é simples carregar os dados offline no Azure usando discos. O processo de backup offline envolve as seguintes etapas:

> [!div class="checklist"]
>
> * Os dados de backup são gravados em um local de preparo em vez de serem enviados pela rede.
> * Os dados no local de preparo são gravados em um ou mais discos SATA usando o utilitário *AzureOfflineBackupDiskPrep* .
> * Um trabalho de importação do Azure é criado automaticamente pelo utilitário.
> * As unidades SATA são então enviadas para o datacenter do Azure mais próximo.
> * Depois que o upload dos dados de backup no Azure for concluído, o Backup do Azure copia os dados de backup para o cofre de backup e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações com suporte

O backup offline tem suporte para todos os modelos de implantação do backup do Azure que fazem backup de dados do local para a nuvem da Microsoft. Esses modelos incluem:

> [!div class="checklist"]
>
> * Backup de arquivos e pastas com o agente MARS ou o agente de backup do Azure.
> * Backup de todas as cargas de trabalho e arquivos com o DPM.
> * Backup de todas as cargas de trabalho e arquivos com MABS.

## <a name="prerequisites"></a>Prerequisites

Verifique se os pré-requisitos a seguir foram atendidos antes de iniciar o fluxo de trabalho de backup offline:

* Um [cofre de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) foi criado. Para criar um, siga as etapas em [criar um cofre dos serviços de recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Um agente de backup do Azure ou MABS ou DPM foi instalado no Windows Server ou em um cliente Windows, conforme aplicável, e o computador está registrado no cofre dos serviços de recuperação. Verifique se apenas a [versão mais recente do Backup do Azure](https://go.microsoft.com/fwlink/?linkid=229525) é usada.
* [Baixe o arquivo de configurações de publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) no computador do qual você planeja fazer backup dos dados. A assinatura da qual você baixa o arquivo de configurações de publicação pode ser diferente da assinatura que contém o cofre dos serviços de recuperação. Se sua assinatura estiver em nuvens soberanas Azure, use os links a seguir, conforme apropriado, para baixar o arquivo de configurações de publicação do Azure.

    | Região da nuvem soberana | Link do arquivo de configurações de publicação do Azure |
    | --- | --- |
    | Estados Unidos | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Uma conta de armazenamento do Azure com o modelo de implantação do Gerenciador de recursos foi criada na assinatura da qual você baixou o arquivo de configurações de publicação.

  ![Criar uma conta de armazenamento com o desenvolvimento do Gerenciador de recursos](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Um local de preparo, o que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criado. Por exemplo, se você quiser fazer backup de um servidor de arquivos de 500 GB, verifique se a área de preparação tem pelo menos 500 GB. (Um valor menor é usado devido à compactação).
* Para discos enviados para o Azure, certifique-se de que apenas discos rígidos internos de SSD de 2,5 polegadas ou de 2,5 polegadas ou 3,5 pol. SATA II/III sejam usados. Você pode usar discos rígidos de até 10 TB. Confira a [documentação da Importação/Exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para saber o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA devem ser conectadas a um computador (conhecido como um *computador de cópia*) de onde a cópia dos dados de backup do local de preparo para as unidades SATA é feita. Verifique se o BitLocker está habilitado no computador de cópia.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Preparar o servidor para o processo de backup offline

>[!NOTE]
> Se você não encontrar os utilitários listados, como *AzureOfflineBackupCertGen. exe*, na instalação do agente Mars, grave no AskAzureBackupTeam@microsoft.com para obter acesso a eles.

* Abra um prompt de comando com privilégios elevados no servidor e execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    A ferramenta cria um aplicativo de Active Directory de backup offline do Azure se ele não existir.

    Se um aplicativo já existir, esse executável solicitará que você carregue o certificado manualmente no aplicativo no locatário. Siga as etapas nesta [seção](#manually-upload-an-offline-backup-certificate) para carregar o certificado manualmente no aplicativo.

* A ferramenta *AzureOfflineBackup. exe* gera um arquivo *OfflineApplicationParams. xml* . Copie esse arquivo para o servidor com o MABS ou o DPM.
* Instale o [agente Mars mais recente](https://aka.ms/azurebackup_agent) na instância do DPM ou no servidor de backup do Azure.
* Registre o servidor no Azure.
* Execute o comando a seguir:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* O comando anterior cria o arquivo `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`.

## <a name="manually-upload-an-offline-backup-certificate"></a>Carregar manualmente um certificado de backup offline

Siga estas etapas para carregar manualmente o certificado de backup offline para um aplicativo de Azure Active Directory criado anteriormente destinado ao backup offline.

1. Entre no portal do Azure.
1. Vá para **Azure Active Directory** > **registros de aplicativo**.
1. Na guia **aplicativos de propriedade** , localize um aplicativo com o formato de nome de exibição `AzureOfflineBackup _<Azure User Id`.

    ![Guia Localizar aplicativo em aplicativos de propriedade](./media/backup-azure-backup-import-export/owned-applications.png)

1. Selecione o aplicativo. Em **gerenciar** no painel esquerdo, vá para **certificados & segredos**.
1. Verifique se há certificados ou chaves públicas preexistentes. Se não houver nenhum, você poderá excluir com segurança o aplicativo selecionando o botão **excluir** na página **visão geral** do aplicativo. Em seguida, você pode repetir as etapas para [preparar o servidor para o processo de backup offline](#prepare-the-server-for-the-offline-backup-process) e ignorar as etapas a seguir. Caso contrário, continue seguindo estas etapas da instância do DPM ou do servidor de backup do Azure em que você deseja configurar o backup offline.
1. Selecione a guia **gerenciar o aplicativo de certificado do computador** > **pessoal** . procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
1. Selecione o certificado, clique com o botão direito do mouse em **todas as tarefas**e selecione **Exportar**, sem uma chave privada, no formato. cer.
1. Vá para o aplicativo de backup offline do Azure na portal do Azure.
1. Selecione **gerenciar** certificados de >  **& segredos** > **carregar certificado**. Carregue o certificado exportado na etapa anterior.

    ![Carregar o certificado](./media/backup-azure-backup-import-export/upload-certificate.png)

1. No servidor, abra o registro digitando **regedit** na janela Executar.
1. Vá para o computador de entrada do registro *\ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider*.
1. Clique com o botão direito do mouse em **CloudBackupProvider**e adicione um novo valor de cadeia de caracteres com o nome `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Para localizar a ID de usuário do Azure, execute uma das seguintes etapas:
    >
    >* No PowerShell conectado ao Azure, execute o comando `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”`.
    >* Vá para o caminho do registro `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`.

1. Clique com o botão direito do mouse na cadeia de caracteres adicionada na etapa anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou na etapa 7. Depois, selecione **OK**.
1. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione a guia **detalhes** e role para baixo até ver o campo impressão digital. Selecione **impressão digital**e copie o valor.

    ![Copiar valor do campo impressão digital](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Continue na seção de [fluxo de trabalho](#workflow) para continuar com o processo de backup offline.

## <a name="workflow"></a>Fluxo de trabalho

As informações nesta seção ajudam a concluir o fluxo de trabalho de backup offline para que seus dados possam ser entregues a um datacenter do Azure e carregados no armazenamento do Azure. Se você tiver dúvidas sobre o serviço de importação ou qualquer aspecto do processo, consulte a [documentação de visão geral do serviço de importação](../storage/common/storage-import-export-service.md) referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar o backup offline

1. Ao agendar um backup, você verá a seguinte página no Windows Server, um cliente do Windows ou o DPM.

    ![Importar página](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aqui está a página correspondente no DPM. <br/>
    
    ![Página de importação do DPM e Servidor de Backup do Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    As caixas que você preenche são:

   * **Local de preparo**: o local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho de rede completo do local de preparo.
   * **Configurações de publicação do Azure**: o caminho local para o arquivo de configurações de publicação.
   * **Nome do trabalho de importação do Azure**: o nome exclusivo pelo qual o serviço de importação/exportação do Azure e o backup do Azure acompanham a transferência de dados enviados em discos para o Azure.
   * **ID de assinatura do Azure**: a ID de assinatura do Azure para a assinatura de onde você baixou o arquivo de configurações de publicação do Azure.
   * **Conta de armazenamento do Azure**: o nome da conta de armazenamento na assinatura do Azure associada ao arquivo de configurações de publicação do Azure.
   * **Contêiner de Armazenamento do Microsoft Azure**: o nome do blob de armazenamento de destino na conta de armazenamento do Azure onde os dados de backup são importados.

   Salve o **local de preparo** e as informações de **nome do trabalho de importação do Azure** que você forneceu. É necessário preparar os discos.

1. Conclua o fluxo de trabalho. Para iniciar a cópia de backup offline, selecione **fazer backup agora** no console de gerenciamento do agente de backup do Azure. O backup inicial é gravado na área de preparo como parte dessa etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no DPM ou Servidor de Backup do Azure, clique com o botão direito do mouse no **grupo de proteção**. Selecione a opção **criar ponto de recuperação** . Em seguida, selecione a opção **proteção online** .

    ![Fazer backup do DPM e do MABS agora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco.

    ![Andamento do backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure

O utilitário *AzureOfflineBackupDiskPrep* é usado para preparar as unidades SATA que são enviadas para o datacenter do Azure mais próximo. Esse utilitário está disponível no diretório de instalação do agente dos serviços de recuperação no seguinte caminho:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Vá até o diretório e copie o diretório *AzureOfflineBackupDiskPrep* para um computador de cópia no qual as unidades SATA a serem preparadas estejam conectadas. Verifique se:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na seção "Iniciar backup offline".
   * O BitLocker está habilitado no computador de cópia.
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia também pode ser o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, será obrigatório usar um servidor físico ou computador cliente diferente como o computador de cópia.

1. Abra um prompt de comando com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual. Execute o comando a seguir:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | s:&lt;*Caminho do Local de Preparo*&gt; |Essa entrada obrigatória é usada para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho na seção "Iniciar backup offline". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |Essa entrada opcional é usada para fornecer o caminho para o arquivo de configurações de publicação do Azure que você inseriu no fluxo de trabalho na seção "Iniciar backup offline". |

    > [!NOTE]
    > O valor &lt;Caminho para AzurePublishSettingFile&gt; será obrigatório quando o computador de cópia e o computador de origem forem diferentes.
    >
    >

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de preparo fornecido, você verá uma página como esta.

    ![Entrada da ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Insira a letra da unidade sem os dois pontos à direita do disco montado que você deseja preparar para a transferência para o Azure. Quando solicitado, forneça a confirmação para a formatação da unidade.

    Em seguida, a ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar discos adicionais quando solicitado pela ferramenta caso o disco fornecido não tenha espaço suficiente para os dados de backup. <br/>

    Depois que a ferramenta for concluída com êxito, um ou mais discos fornecidos serão preparados para envio ao Azure. Um trabalho de importação com o nome que você forneceu durante o workflow na seção "Iniciar backup offline" também é criado no Azure. Por fim, a ferramenta exibe o endereço de entrega para o datacenter do Azure para onde os discos precisam ser enviados.

    ![Preparação de disco do Azure concluída](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. No final da execução do comando, você também verá a opção para atualizar as informações de envio.

    ![Atualizar opção de informações de remessa](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. Você pode inserir os detalhes imediatamente. A ferramenta orienta você pelo processo que envolve uma série de entradas. Se você não tiver informações como o número de controle ou outros detalhes relacionados ao envio, você poderá encerrar a sessão. As etapas para atualizar os detalhes do envio posteriormente são fornecidas neste artigo.

1. Envie os discos para o endereço fornecido pela ferramenta. Mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT]
   > Dois trabalhos de importação do Azure não podem ter o mesmo número de acompanhamento. Verifique se as unidades preparadas pelo utilitário em um único trabalho de importação do Azure são enviadas juntas em um único pacote e se há um único número de controle exclusivo para o pacote. Não combine unidades preparadas como parte de diferentes trabalhos de importação do Azure em um único pacote.

1. Quando você tiver as informações de número de controle, vá para o computador de origem, que está aguardando a conclusão do trabalho de importação. Execute o comando a seguir em um prompt de comando com privilégios elevados com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcionalmente, você pode executar o comando a seguir em um computador diferente, como o computador de cópia, com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | u: | Essa entrada obrigatória é usada para atualizar os detalhes de envio para um trabalho de importação do Azure. |
    | s:&lt;*Caminho do Local de Preparo*&gt; | Essa entrada obrigatória é usada quando o comando não é executado no computador de origem. Ele é usado para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho na seção "Iniciar backup offline". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; | Essa entrada obrigatória é usada quando o comando não é executado no computador de origem. Ele é usado para fornecer o caminho para o arquivo de configurações de publicação do Azure que você inseriu no fluxo de trabalho na seção "Iniciar backup offline". |

    O utilitário detecta automaticamente o trabalho de importação que o computador de origem está aguardando ou os trabalhos de importação associados ao local de preparo quando o comando é executado em um computador diferente. Em seguida, ele fornece a opção de atualizar as informações de envio por meio de uma série de entradas.

    ![Inserir informações de remessa](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Depois que todas as entradas forem fornecidas, examine os detalhes cuidadosamente e confirme as informações de envio fornecidas inserindo **Sim**.

    ![Examinar informações de envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Depois que as informações de envio forem atualizadas com êxito, o utilitário fornecerá um local local onde os detalhes de envio inseridos serão armazenados.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Verifique se as unidades atingem o datacenter do Azure dentro de duas semanas de fornecimento das informações de envio usando o utilitário *AzureOfflineBackupDiskPrep* . A falha em fazer isso pode resultar no não processamento das unidades. 

Depois de concluir as etapas anteriores, o datacenter do Azure está pronto para receber as unidades e processá-las ainda mais para transferir os dados de backup das unidades para a conta de armazenamento do Azure do tipo clássico que você criou.

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades

A quantidade de tempo que leva para processar um trabalho de importação do Azure varia. O tempo de processo depende de fatores como tempo de envio, tipo de trabalho, tipo e tamanho dos dados que estão sendo copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA. Depois que os discos são recebidos, o serviço se esforça para concluir a cópia dos dados de backup em sua conta de armazenamento do Azure em 7 a 10 dias. A próxima seção descreve como você pode monitorar o status do trabalho de importação do Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorar o status do trabalho de importação do Azure

Enquanto as unidades estiverem em trânsito ou no datacenter do Azure para serem copiadas na conta de armazenamento, o agente de backup do Azure ou o DPM ou o console do MABS no computador de origem mostrará o seguinte status de trabalho para os backups agendados:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Para verificar o status do trabalho de importação:

1. Abra um prompt de comando com privilégios elevados no computador de origem e execute o seguinte comando:

     `AzureOfflineBackupDiskPrep.exe u:`

1. A saída mostra o status atual do trabalho de importação.

    ![Verificar o status do trabalho de importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os vários Estados do trabalho de importação do Azure, consulte [Exibir o status dos trabalhos de importação/exportação do Azure](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Concluir o fluxo de trabalho

Após a conclusão da importação, os dados de backup inicias estarão disponíveis na sua conta de armazenamento. No momento do próximo backup agendado, o backup do Azure copia o conteúdo dos dados da conta de armazenamento para o cofre dos serviços de recuperação.

   ![Copiar dados para o cofre dos serviços de recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No horário do próximo backup agendado, o Backup do Azure executa o backup incremental sobre a cópia de backup inicial.

## <a name="next-steps"></a>Próximas etapas

* Para dúvidas sobre o fluxo de trabalho do serviço de importação/exportação do Azure, consulte [usar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../storage/common/storage-import-export-service.md).
