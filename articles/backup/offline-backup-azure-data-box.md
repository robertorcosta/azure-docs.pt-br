---
title: Backup off-line usando a Caixa de Dados do Azure
description: Saiba como usar o Azure Data Box para semear grandes dados iniciais de backup off-line do Agente MARS para um cofre de Serviços de Recuperação.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672959"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup off-line do Azure usando a caixa de dados do Azure

Você pode usar [a Caixa de Dados Do Azure](https://docs.microsoft.com/azure/databox/data-box-overview) para semear seus backups iniciais do Microsoft Azure Recovery Services (MARS) offline (sem usar a rede) em um cofre de Serviços de Recuperação. Esse processo economiza tempo e largura de banda de rede que, de outra forma, seriam consumidos movendo grandes quantidades de dados de backup on-line em uma rede de alta latência. Este aprimoramento está atualmente em pré-visualização. O backup off-line baseado no Azure Data Box oferece duas vantagens distintas sobre [backup off-line com base no serviço De importação/exportação do Azure:](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

* Não há necessidade de adquirir seus próprios discos e conectores compatíveis com o Azure. A Azure Data Box envia os discos associados ao [SKU](https://azure.microsoft.com/services/databox/data/)da caixa de dados selecionada .
* O Azure Backup (Agente MARS) pode gravar diretamente dados de backup nas SKUs suportadas da Caixa de Dados Azure. Esse recurso elimina a necessidade de você prover um local de preparação para seus dados iniciais de backup. Você também não precisa de utilitários para formatar e copiar esses dados nos discos.

## <a name="azure-data-box-with-the-mars-agent"></a>Caixa de dados do Azure com o agente MARS

Este artigo explica como você pode usar o Azure Data Box para semear grandes dados iniciais de backup off-line do Agente MARS para um cofre de Serviços de Recuperação.

## <a name="supported-platforms"></a>Plataformas compatíveis

O processo de semeação de dados do Agente MARS usando o Azure Data Box é suportado nas Seguintes SKUs do Windows.

| **OS**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Workstation**                        |                                                              |
| Windows 10 de 64 bits                     | Enterprise, Pro, Home                                       |
| Windows 8.1 de 64 bits                    | Enterprise, Pro                                             |
| Windows 8 de 64 bits                      | Enterprise, Pro                                             |
| Windows 7 de 64 bits                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Servidor**                             |                                                              |
| Windows Server 2019 de 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 de 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 de 64 bits         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 de 64 bits            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 de 64 bits    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 de 64 bits | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 de 64 bits    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 de 64 bits     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bits        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho dos dados de backup e SKUs da caixa de dados suportadas

| Tamanho dos dados de backup (pós-compactação por MARS)* por servidor | SKU da caixa de dados azure suportada                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Disco azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB e <=80 TB**                                      | [Caixa de dados Azure (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*As taxas típicas de compressão variam entre 10% e 20%. <br>
**Se você espera ter mais de 80 TB de dados [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)iniciais de backup para um único servidor MARS, entre em contato .

>[!IMPORTANT]
>Os dados iniciais de backup de um único servidor devem ser contidos em uma única instância da Caixa de Dados Do Azure ou do disco Azure Data Box e não podem ser compartilhados entre vários dispositivos das mesmas Ou diferentes SKUs. Mas um dispositivo Azure Data Box pode conter backups iniciais de vários servidores.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription-and-required-permissions"></a>Assinatura do Azure e permissões necessárias

* O processo requer uma assinatura do Azure.
* O processo exige que o usuário designado para executar a política de backup offline seja um proprietário da assinatura do Azure.
* O trabalho da Data Box e o cofre dos Serviços de Recuperação (para os quais os dados precisam ser semeados) são necessários para estar nas mesmas assinaturas.
* Recomendamos que a conta de armazenamento de destino associada ao trabalho do Azure Data Box e ao cofre dos Serviços de Recuperação estejam na mesma região. No entanto, isso não é necessário.

### <a name="get-azure-powershell-370"></a>Obtenha a Azure PowerShell 3.7.0

*Este é o pré-requisito mais importante para o processo.* Antes de instalar o Azure PowerShell, versão 3.7.0, execute as seguintes verificações.

#### <a name="step-1-check-the-powershell-version"></a>Passo 1: Verifique a versão do PowerShell

1. Abra o Windows PowerShell e execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Se a saída exibir uma versão superior a 3.7.0, faça "Passo 2". Caso contrário, pule para o "Passo 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Passo 2: Desinstale a versão do PowerShell

Desinstale a versão atual do PowerShell.

1. Remova os módulos dependentes executando o seguinte comando no PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Para garantir a exclusão bem-sucedida de todos os módulos dependentes, execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passo 3: Instale o PowerShell versão 3.7.0

Depois de verificar se não há módulos AzureRM, instale a versão 3.7.0 usando um dos seguintes métodos:

* No GitHub, use [este link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Ou você pode:

* Execute o seguinte comando na janela PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

O Azure PowerShell também poderia ter sido instalado usando um arquivo msi. Para removê-lo, desinstale-o usando a opção **Desinstalar programas** no Painel de Controle.

### <a name="order-and-receive-the-data-box-device"></a>Peça e receba o dispositivo Data Box

O processo de backup off-line usando mars e Azure Data Box requer que os dispositivos Data Box estejam em um estado de entrega antes de acionar o backup off-line usando o Agente MARS. Para encomendar o SKU mais adequado para sua exigência, consulte [Tamanho de dados de backup e SKUs da caixa de dados suportada](#backup-data-size-and-supported-data-box-skus). Siga as etapas do [Tutorial: Solicite um disco da Caixa de Dados Do Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) para encomendar e receber seus dispositivos Data Box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo Conta**. O Agente MARS requer uma conta que suporte blobs de página, que não é suportada quando *o BlobStorage* é selecionado. Selecione **O armazenamento V2 (objetivo geral v2)** como o **tipo Conta** quando você criar a conta de armazenamento de destino para o trabalho da Caixa de Dados do Azure.

![Escolha o tipo de conta em detalhes de instância](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instale e configure o Agente MARS

1. Certifique-se de desinstalar quaisquer instalações anteriores do Agente MARS.
1. Baixe o mais recente AGENTE MARS [deste site](https://aka.ms/azurebackup_agent).
1. Execute *MARSAgentInstaller.exe*e faça *apenas* as etapas para instalar e registrar [o agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) no cofre dos Serviços de Recuperação onde deseja que seus backups sejam armazenados.

   > [!NOTE]
   > O cofre serviços de recuperação deve estar na mesma assinatura do trabalho da Caixa de Dados do Azure.

   Depois que o agente estiver registrado no cofre dos Serviços de Recuperação, siga os passos nas próximas seções.

## <a name="set-up-azure-data-box-devices"></a>Configure dispositivos Azure Data Box

Dependendo do SKU da Caixa de Dados Azure que você encomendou, faça as etapas cobertas nas seções apropriadas a seguir. As etapas mostram como configurar e preparar os dispositivos Data Box para que o Agente MARS identifique e transfira os dados iniciais de backup.

### <a name="set-up-azure-data-box-disks"></a>Configure discos azure Data Box

Se você encomendou um ou mais discos do Azure Data Box (até 8 TB cada), siga as etapas mencionadas aqui para [desempacotar, conectar e desbloquear seu disco Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>É possível que o servidor com o Agente MARS não tenha uma porta USB. Nessa situação, você pode conectar seu disco Azure Data Box a outro servidor ou cliente e expor a raiz do dispositivo como um compartilhamento de rede.

### <a name="set-up-azure-data-box"></a>Configurar a caixa de dados do Azure

Se você ordenou uma instância da Caixa de Dados Do Azure (até 100 TB), siga as etapas aqui [para configurar sua instância data box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Monte a instância da caixa de dados do Azure como um sistema local

O Agente MARS opera no contexto do Sistema Local, por isso requer o mesmo nível de privilégio a ser fornecido ao caminho de montagem onde a instância da Caixa de Dados Do Azure está conectada. 

Para garantir que você possa montar seu dispositivo Data Box como um sistema local usando o protocolo NFS:

1. Habilite o cliente para o recurso NFS no servidor Windows que tem o Agente MARS instalado. Especifique a fonte alternativa *WIM:D:\Sources\Install.wim:4*.
1. Baixe PSExec <https://download.sysinternals.com/files/PSTools.zip> do servidor com o Agente MARS instalado.
1. Abra um prompt de comando elevado e execute o seguinte comando com o diretório que contém *PSExec.exe* como o diretório atual.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   A janela de comando que se abre como resultado do comando anterior está no contexto do Sistema Local. Use esta janela de comando para executar as etapas para montar o compartilhamento de blob de página do Azure como uma unidade de rede no servidor Windows.
1. Siga as etapas em [Conecte-se à Caixa de Dados](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) para conectar seu servidor com o Agente MARS ao dispositivo Data Box via NFS. Execute o seguinte comando no prompt de comando do Sistema Local para montar o compartilhamento de blobs de página do Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Depois que o compartilhamento for montado, verifique se você pode acessar X: a partir do seu servidor. Se puder, continue com a próxima seção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transfira dados iniciais de backup para dispositivos Azure Data Box

1. Abra o aplicativo **Microsoft Azure Backup** em seu servidor.
1. No painel **Ações,** selecione **Agendamento backup**.

    ![Selecione backup de agendamento](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Siga as etapas no **Assistente de Backup de Agendamento**.

1. Adicione itens selecionando o botão **Adicionar itens.** Mantenha o tamanho total dos itens dentro dos limites de [tamanho suportados pelo Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) que você encomendou e recebeu.

    ![Adicionar itens ao backup](./media/offline-backup-azure-data-box/add-items.png)

1. Selecione o cronograma de backup e a política de retenção apropriados para **arquivos e pastas** e **estado do sistema**. O estado do sistema é aplicável apenas para servidores Windows e não para clientes Windows.
1. Na página **Escolher tipo inicial de backup (arquivos e pastas)** do assistente, selecione a opção Transferir usando discos da Caixa de Dados do Microsoft **Azure** e selecione **Next**.

    ![Escolha o tipo inicial de backup](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Faça login no Azure quando solicitado usando as credenciais do usuário que têm acesso ao Proprietário na assinatura do Azure. Depois de conseguir fazer isso, você deve ver uma página que se assemelha a esta.

    ![Crie recursos e aplique permissões necessárias](./media/offline-backup-azure-data-box/creating-resources.png)

   O Agente MARS, então, busca os empregos da Data Box na assinatura que estão no estado entregue.

    ![Buscar empregos na Data Box para id de assinatura](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecione o pedido correto da Caixa de Dados para o qual você desempacotou, conectou e desbloqueou o disco data box. Selecione **Avançar**.

    ![Selecione pedidos de caixa de dados](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecione **'Detectar dispositivo'** na página **Detecção de dispositivo da caixa de dados.** Essa ação faz com que o Mars Agent escaneie os discos da Caixa de Dados Azure anexados localmente e detecte-os.

    ![Detecção de dispositivo da caixa de dados](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se você conectou a instância azure Data Box como um compartilhamento de rede (devido à indisponibilidade de portas USB ou porque você encomendou e montou o dispositivo Data Box de 100 TB), a detecção falhará inicialmente. Você tem a opção de inserir o caminho da rede para o dispositivo Data Box.

    ![Digite o caminho da rede](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do disco Azure Data Box. Este diretório deve conter um diretório com o nome *PageBlob*.
    >
    >![Diretório raiz do disco Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Por exemplo, se o caminho `\\mydomain\myserver\disk1\` do disco for e o *disco1* contiver um diretório chamado `\\mydomain\myserver\disk1\` *PageBlob,* o caminho digitado na página do assistente do Agente MARS é .
    >
    >Se você [configurar um dispositivo Azure Data Box de 100 TB,](#set-up-azure-data-box-devices)digite `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` como o caminho da rede para o dispositivo.

1. Selecione **Next**e **selecione Concluir** na próxima página para salvar a política de backup e retenção com a configuração de backup off-line usando a Caixa de Dados Do Azure.

   A página seguinte confirma que a diretiva foi salva com sucesso.

    ![A política é salva com sucesso](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selecione **Fechar** na página anterior.

1. Selecione **Fazer backup agora** no painel **Ações** do console AGENTE MARS. Selecione **Fazer backup** na página do assistente.

    ![Backup agora assistente](./media/offline-backup-azure-data-box/backup-now.png)

O Agente MARS inicia o backup dos dados selecionados para o dispositivo Azure Data Box. Esse processo pode levar de várias horas a alguns dias. A quantidade de tempo depende do número de arquivos e da velocidade de conexão entre o servidor com o Agente MARS e o disco Azure Data Box.

Depois que o backup dos dados estiver concluído, você verá uma página no Agente MARS que se assemelha a este.

![Progresso de backup mostrado](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Etapas pós-backup

Esta seção explica as etapas a serem tomadas após o backup dos dados no Azure Data Box Disk for bem-sucedido.

* Siga os passos deste artigo para [enviar o disco Azure Data Box para o Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Se você usou um dispositivo Azure Data Box de 100 TB, siga estas etapas para [enviar o dispositivo Azure Data Box para o Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Monitore o trabalho da Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) no portal Azure. Depois que o trabalho da Caixa de Dados do Azure é concluído, o Agente MARS move automaticamente os dados da conta de armazenamento para o cofre dos Serviços de Recuperação no momento do próximo backup programado. Em seguida, ele marca o trabalho de backup como *Trabalho Concluído* se um ponto de recuperação for criado com sucesso.

    >[!NOTE]
    >O Agente MARS aciona backups nos horários programados durante a criação da diretiva. Esses trabalhos sinalizam "Esperando que o trabalho da Caixa de Dados do Azure seja concluído" até o momento em que o trabalho estiver concluído.

* Depois que o Agente MARS criar com sucesso um ponto de recuperação que corresponda ao backup inicial, você pode excluir a conta de armazenamento ou conteúdo específico associado ao trabalho da Caixa de Dados do Azure.

## <a name="troubleshooting"></a>Solução de problemas

O Microsoft Azure Backup (MAB) Agent cria um aplicativo Azure Active Directory (Azure AD) para você em seu inquilino. Este aplicativo requer um certificado de autenticação criado e carregado quando você configura uma política de semeação off-line. Usamos o Azure PowerShell para criar e carregar o certificado para o aplicativo Azure AD.

### <a name="problem"></a>Problema

Quando você configura o backup off-line, você pode enfrentar um problema por causa de um bug no cmdlet Do Azure PowerShell. Você pode não conseguir adicionar vários certificados ao mesmo aplicativo Azure AD criado pelo MAB Agent. Esse problema irá afetá-lo se você configurou uma política de semeamento off-line para o mesmo ou um servidor diferente.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verifique se o problema é causado por essa causa raiz específica

Para ver se o seu problema é o mesmo descrito anteriormente, faça um dos seguintes passos.

#### <a name="step-1"></a>Etapa 1

Verifique se a seguinte mensagem de erro aparece no console MAB quando você configurou backup off-line.

![Não é possível criar a política de backup offline para a conta azure atual](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Etapa 2

1. Abra a **pasta Temp** no caminho de instalação. O caminho da pasta temporária padrão é *C:\Arquivos do programa\Microsoft Azure Recovery Services Agent\Temp*. Procure o arquivo *CBUICurr* e abra o arquivo.

1. No arquivo *CBUICurr,* role até a última linha e verifique se o problema é `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`o mesmo que o da mensagem de erro: .

### <a name="workaround"></a>Solução alternativa

Como solução para resolver esse problema, faça as seguintes etapas e tente novamente a configuração da diretiva.

#### <a name="step-1"></a>Etapa 1

Faça login no PowerShell que aparece no MAB UI usando uma conta diferente com acesso de administração na assinatura que terá o trabalho de importação ou exportação criado.

#### <a name="step-2"></a>Etapa 2

Se nenhum outro servidor tiver a semeada offline `AzureOfflineBackup_<Azure User Id>` configurada e nenhum outro servidor depender do aplicativo, exclua este aplicativo. Selecione as inscrições do**Aplicativo**de Diretório > Ativo **do Azure** > **Azure**.

>[!NOTE]
> Verifique se o `AzureOfflineBackup_<Azure User Id>` aplicativo não tem nenhuma outra semeada offline configurada e também se nenhum outro servidor depende desse aplicativo. Vá para **As teclas de** > **configuração** na seção **Chaves Públicas.** Não deveria ter nenhuma outra chave pública adicionada. Veja a captura de tela a seguir para referência.
>
>![Chaves públicas](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Etapa 3

A partir do servidor que você está tentando configurar para backup off-line, execute as seguintes ações.

1. Vá para a **guia Gerenciar certificado** > de computador A `CB_AzureADCertforOfflineSeeding_<ResourceId>`guia**pessoal** e procure o certificado com o nome .

2. Selecione o certificado, clique com o botão direito do mouse em **Todas as tarefas**e selecione **Exportar** sem uma chave privada no formato .cer.

3. Vá para o aplicativo de backup off-line do Azure mencionado na etapa 2. Selecione **Teclas de** > **configuração** > **Upload chave pública**. Faça o upload do certificado que você exportou na etapa anterior.

    ![Carregar a chave pública](./media/offline-backup-azure-data-box/upload-public-key.png)

4. No servidor, abra o registro inserindo **regedit** na janela de execução.

5. Ir para o registro *Computador\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Clique com o botão direito do mouse no `AzureADAppCertThumbprint_<Azure User Id>` **CloudBackupProvider**e adicione um novo valor de string com o nome .

    >[!NOTE]
    > Para obter o ID do usuário do Azure, execute uma dessas ações:
    >
    >* A partir do PowerShell conectado ao `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` Azure, execute o comando.
    > * Ir para o caminho do registro *Computador\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* com o nome *CurrentUserId*.

6. Clique com o botão direito do mouse na seqüência adicionada na etapa anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou na etapa 2. Selecione **OK**.

7. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione a guia **Detalhes** e role para baixo até ver o campo de impressão digital. Selecione **Impressão digital**e copie o valor.

    ![Campo de impressão digital do certificado](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Perguntas

Para quaisquer dúvidas ou esclarecimentos sobre [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)quaisquer problemas que você enfrentou, entre em contato.
