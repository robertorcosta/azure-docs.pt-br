---
title: Backup off-line da semente com o serviço de importação/exportação do Azure
description: Saiba como usar o Azure Backup para enviar dados da rede usando o serviço De importação/exportação do Azure. Este artigo explica a semeação off-line dos dados iniciais de backup usando o serviço Deimportação/Exportação do Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206751"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de backup off-line no Backup do Azure

O Backup do Azure tem vários mecanismos internos eficientes que reduzem os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. Durante o processo de propagação offline, o Backup do Azure pode usar discos para carregar os dados de backup offline no Azure.

O processo de semeadura offline do Azure Backup está fortemente integrado com o [serviço de importação/exportação do Azure.](../storage/common/storage-import-export-service.md) Você pode usar este serviço para transferir dados de backup iniciais para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup iniciais que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você pode usar o fluxo de trabalho de semeadura off-line para enviar a cópia inicial de backup, em um ou mais discos rígidos para um data center do Azure. A imagem a seguir fornece uma visão geral das etapas no fluxo de trabalho.

  ![Visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup off-line envolve essas etapas:

1. Em vez de enviar os dados de backup pela rede, grave os dados de backup em um local de preparo.
1. Use o *utilitário AzureOfflineBackupDiskPrep* para gravar os dados no local de preparação para um ou mais discos SATA.
1. Como parte do trabalho preparatório, o utilitário *AzureOfflineBackupDiskPrep* cria um trabalho de importação do Azure. Envie as unidades SATA para o datacenter do Azure mais próximo e referencie o trabalho de importação para conectar as atividades.
1. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
1. O Backup do Azure copia os dados de backup da conta de armazenamento para o cofre de Serviços de Recuperação, e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações com suporte

Os seguintes recursos de backup do Azure ou cargas de trabalho suportam o uso de backup off-line para:

> [!div class="checklist"]
>
> * Backup de arquivos e pastas com o Microsoft Azure Recovery Services (MARS) Agent, também referido como o Agente de Backup do Azure.
> * Backup de todas as cargas de trabalho e arquivos com o DPM (System Center Data Protection Manager, gerente de proteção de dados do centro do sistema).
> * Backup de todas as cargas de trabalho e arquivos com o Microsoft Azure Backup Server.
 
   > [!NOTE]
   > O backup off-line não é suportado para backups de estado do sistema feitos usando o Azure Backup Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os seguintes pré-requisitos e fluxo de trabalho aplicam-se apenas ao backup off-line de arquivos e pastas usando o [mais recente Azure Recovery Services Agent](https://aka.ms/azurebackup_agent). Para executar backups off-line para cargas de trabalho usando o System Center DPM ou o Azure Backup Server, consulte [Fluxo de trabalho de backup off-line para DPM e Azure Backup Server](backup-azure-backup-server-import-export-.md).

Antes de iniciar o fluxo de trabalho de backup off-line, complete os seguintes pré-requisitos:

* Crie um [cofre de serviços de recuperação](backup-azure-recovery-services-vault-overview.md). Para criar um cofre, siga as etapas em [Criar um cofre de serviços de recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Certifique-se de que apenas a [versão mais recente do Azure Backup Agent](https://aka.ms/azurebackup_agent) esteja instalada no cliente do Windows Server ou Windows, conforme aplicável, e que o computador esteja registrado no cofre dos Serviços de Recuperação.
* O Azure PowerShell 3.7.0 é necessário no computador executando o Azure Backup Agent. Baixe e [instale a versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o Azure Backup Agent, certifique-se de que o Microsoft Edge ou o Internet Explorer 11 esteja instalado e o JavaScript esteja habilitado.
* Crie uma conta de armazenamento Azure na mesma assinatura do cofre serviços de recuperação.
* Verifique se você tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar o aplicativo do Azure Active Directory. O fluxo de trabalho de backup off-line cria um aplicativo Do Azure Active Directory na assinatura associada à conta de armazenamento Do Zure. O objetivo do aplicativo é fornecer ao Azure Backup acesso seguro e escopo ao serviço Azure Import/Export, que é necessário para o fluxo de trabalho de backup off-line.
* Registre o provedor de recursos *Microsoft.ImportExport* com a assinatura que contém a conta de armazenamento do Azure. Para registrar o provedor de recursos:
    1. No menu principal, **selecione Assinaturas**.
    1. Se você estiver inscrito em várias assinaturas, selecione a assinatura que planeja usar para o backup offline. Se você usa apenas uma assinatura, essa assinatura será exibida.
    1. No menu de assinatura, selecione **provedores de recursos** para visualizar a lista de provedores.
    1. Na lista de provedores, desça até *Microsoft.ImportExport*. Se o **status** **não estiver registrado, selecione** **Registrar**.

        ![Registre o provedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)

* Um local de preparo, o que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criado. Por exemplo, se você quiser fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparação é de pelo menos 500 GB. (Um valor menor é usado devido à compactação).
* Quando você enviar discos para o Azure, use apenas discos SSD de 2,5 polegadas ou 2,5 polegadas ou 3,5 polegadas sata II/III discos rígidos internos. Você pode usar discos rígidos de até 10 TB. Confira a [documentação da Importação/Exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para saber o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como *computador de cópia*) de onde é realizada a cópia de dados de backup do local de preparo para as unidades SATA. Verifique se o BitLocker está habilitado no computador de cópia.

## <a name="workflow"></a>Fluxo de trabalho

Esta seção descreve o fluxo de trabalho de backup off-line para que seus dados possam ser entregues em um data center do Azure e carregados para o Azure Storage. Se você tiver dúvidas sobre o serviço de importação ou qualquer aspecto do processo, consulte a documentação geral do [serviço de importação/exportação do Azure](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar o backup offline

1. Quando você agenda um backup no Agente de Serviços de Recuperação, você vê esta página.

    ![Página de importação](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selecione a opção **Transferir usando meus próprios discos**.

    > [!NOTE]
    > Use a opção Azure Data Box para transferir dados iniciais de backup off-line. Essa opção salva o esforço necessário para adquirir seus próprios discos compatíveis com o Azure. Ele fornece dispositivos Azure Data Box proprietários, seguros e à prova de adulteração da Microsoft para os quais os dados de backup podem ser diretamente gravados pelo Agente de Serviços de Recuperação.

1. Selecione **Next**e preencha as caixas cuidadosamente.

    ![Digite os detalhes do disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   As caixas que você preenche são:

    * **Local de preparo**: o local de armazenamento temporário no qual a cópia de backup inicial é gravada. O local de encenação pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho completo da rede do local de preparação.
    * Conta de armazenamento do Gerenciador de **Recursos do Azure**: O nome da conta de armazenamento do tipo Resource Manager (objetivo geral v1 ou v2 de propósito geral) em qualquer assinatura do Azure.
    * **Contêiner de armazenamento Do Zure**: O nome do blob de armazenamento de destino na conta de armazenamento do Azure, onde os dados de backup são importados antes de serem copiados para o cofre dos Serviços de Recuperação.
    * **ID de assinatura do Azure**: O ID da assinatura do Azure onde a conta de armazenamento do Azure é criada.
    * **Nome de trabalho de importação do Azure**: O nome exclusivo pelo qual o serviço de importação/exportação do Azure e o Backup do Azure acompanham a transferência de dados enviados em discos para o Azure.
  
   Depois de preencher as caixas, selecione **Next**. Salve o **local de preparação** e as informações do nome do trabalho de importação do **Azure.** É necessário preparar os discos.

1. Quando solicitado, faça login na sua assinatura do Azure. Você deve fazer login para que o Azure Backup possa criar o aplicativo Azure Active Directory. Digite as permissões necessárias para acessar o serviço de importação/exportação do Azure.

    ![Página de login de assinatura do Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Termine o fluxo de trabalho. No console Azure Backup Agent, selecione **Backup Up Now**.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

1. Na página **Confirmação** do assistente, selecione **Backup**. O backup inicial é gravado na área de preparo como parte da instalação.

   ![Confirme que você está pronto para fazer backup agora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco.

   ![Página do Assistente de Backup Agora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o datacenter do Azure mais próximo. Este utilitário está disponível no diretório de instalação do Azure Backup Agent no seguinte caminho:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Vá para o diretório e copie o diretório *AzureOfflineBackupDiskPrep* para outro computador onde as unidades SATA estão conectadas. No computador com as unidades SATA conectadas, certifique-se de que:

   * O computador de cópia pode acessar o local de preparação para o fluxo de trabalho de semeamento offline usando o mesmo caminho de rede fornecido no fluxo de trabalho na seção "Iniciar backup off-line".
   * O BitLocker está habilitado no computador de cópia.
   * O Azure PowerShell 3.7.0 está instalado.
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está ativado.
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia também pode ser o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, o computador de cópia precisará ser um servidor ou um computador cliente físico do computador de origem.

1. Abra um prompt de comando elevado no computador de cópia com o diretório de *utilitárioS AzureOfflineBackupDiskPrep* como o diretório atual. Execute o comando a seguir:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*Caminho de localização de estadiamento*&gt; |Esta entrada obrigatória é usada para fornecer o caminho para o local de preparação que você inseriu no fluxo de trabalho na seção "Iniciar backup off-line". |
    | p:&lt;*Caminho para PublishSettingsFile*&gt; |Esta entrada opcional é usada para fornecer o caminho para o arquivo de configurações de publicação do Azure que você inseriu no fluxo de trabalho na seção "Iniciar backup off-line". |

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de encenação fornecido, você verá uma página como esta.

    ![Entrada da ferramenta de preparação do disco Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Insira a letra da unidade sem os dois pontos à direita do disco montado que você deseja preparar para a transferência para o Azure.
1. Confirme a formatação da unidade quando solicitado.
1. Você é solicitado a entrar na sua assinatura do Azure. Insira suas credenciais.

    ![Login de assinatura do Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    A ferramenta então começa a preparar o disco e copiar os dados de backup. Você pode precisar anexar discos adicionais quando solicitado pela ferramenta no caso de o disco fornecido não ter espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, o prompt de comando fornecerá três tipos de informação:

   1. Um ou mais discos que você forneceu estão preparados para o envio ao Azure.
   1. Você recebe a confirmação de que seu trabalho de importação foi criado. O trabalho de importação usa o nome fornecido.
   1. A ferramenta exibe o endereço para entrega do datacenter do Azure.

      ![Preparação do disco Azure concluída](./media/backup-azure-backup-import-export/console2.png)<br/>

1. No final da execução do comando, você pode atualizar as informações de envio.

1. Enviar os discos para o endereço que a ferramenta forneceu. Mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT]
   > Nenhum dos dois empregos de importação do Azure pode ter o mesmo número de rastreamento. Certifique-se de que as unidades preparadas pelo utilitário um único trabalho de importação do Azure sejam enviadas juntas em um único pacote e que haja um único número de rastreamento exclusivo para o pacote. Não combine unidades preparadas como parte de trabalhos de importação separados do Azure em um único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualize os detalhes do envio sobre o trabalho de importação do Azure

O procedimento a seguir atualiza os detalhes do envio de trabalho de importação do Azure. Essas informações incluem detalhes sobre:

* O nome da operadora que entrega os discos ao Azure.
* Devolva os detalhes do envio de seus discos.

1. Entre em sua assinatura do Azure.
1. No menu principal, selecione **Todos os serviços**. Na caixa de diálogo **Todos os serviços,** **digite Importar**. Quando você ver **trabalhos de importação/exportação,** selecione-o.

    ![Insira informações de envio](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    O menu **De importação/exportação de empregos** é aberto e a lista de todos os trabalhos de importação/exportação na assinatura selecionada é exibida.

1. Se você tiver várias assinaturas, selecione a assinatura usada para importar os dados de backup. Em seguida, selecione o trabalho de importação recém-criado para abrir seus detalhes.

    ![Revisar informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. No menu **Configurações** para o trabalho de importação, selecione **Gerenciar informações de envio**. Digite os detalhes de envio de devolução.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Quando você tiver o número de rastreamento da sua transportadora de transporte, selecione o banner na página de visão geral do trabalho de importação do Azure e digite os seguintes detalhes.

   > [!IMPORTANT]
   > Verifique se as informações da transportadora e o número de controle são atualizados em até duas semanas após a criação do trabalho de importação do Azure. A não verificação dessas informações dentro de duas semanas pode resultar na exclusão do trabalho e no processamento das unidades.

   ![Alerta de atualização de informações de rastreamento](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informações da transportadora e número de rastreamento](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades

O tempo necessário para processar um trabalho de importação do Azure varia. O tempo de processo é baseado em fatores como tempo de envio, tipo de trabalho, tipo e tamanho dos dados copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA. Depois que os discos são recebidos, o serviço se esforça para concluir a cópia de dados de backup em sua conta de armazenamento Do Zure em 7 a 10 dias.

### <a name="monitor-azure-import-job-status"></a>Monitore o status de trabalho de importação do Azure

Você pode monitorar o status do seu trabalho de importação a partir do portal Azure. Vá para a página **de empregos importação/exportação** e selecione seu trabalho. Para obter mais informações sobre o status dos empregos de importação, consulte [O que é o serviço de importação/exportação do Azure?](../storage/common/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Termine o fluxo de trabalho

Depois que o trabalho de importação for concluído com êxito, os dados de backup inicias estarão disponíveis em sua conta de armazenamento. No momento do próximo backup programado, o Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

   ![Copiar dados para o cofre dos Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No horário do próximo backup agendado, o Backup do Azure executará um backup incremental.

### <a name="clean-up-resources"></a>Limpar recursos

Depois que o backup inicial for concluído, você pode excluir com segurança os dados importados para o contêiner Azure Storage e os dados de backup no local de preparação.

## <a name="next-steps"></a>Próximas etapas

* Para obter quaisquer dúvidas sobre o fluxo de trabalho do serviço de importação/exportação do Azure, consulte [Use o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento Blob](../storage/common/storage-import-export-service.md).
