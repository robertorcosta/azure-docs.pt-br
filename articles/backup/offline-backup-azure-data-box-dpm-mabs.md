---
title: Backup offline com Azure Data Box para o DPM e o MABS
description: Você pode usar Azure Data Box para propagar dados de backup inicial offline do DPM e do MABS.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 8b585dc46eb2bdd54e48950ca861f0edc8f0a7ed
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186869"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Propagação offline usando Azure Data Box para o DPM e MABS (visualização)

> [!NOTE]
> Esse recurso é aplicável para Data Protection Manager (DPM) 2019 UR2 e posterior.<br><br>
> Este recurso está atualmente em visualização para o Backup do Microsoft Azure Server (MABS). Se você estiver interessado em usar Azure Data Box para propagação offline com MABS, entre em contato conosco em [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Este artigo explica como você pode usar Azure Data Box para propagar dados de backup inicial offline do DPM e MABS para um cofre dos serviços de recuperação do Azure.

Você pode usar [Azure data Box](https://docs.microsoft.com/azure/databox/data-box-overview) para propagar seus grandes backups do DPM/mAbs inicial offline (sem usar a rede) para um cofre dos serviços de recuperação. Esse processo poupa tempo e largura de banda de rede que, de outra forma, seriam consumidos movendo grandes quantidades de dados de backup online em uma rede de alta latência. Esse recurso está atualmente na visualização.

O backup offline baseado em Azure Data Box fornece duas vantagens distintas sobre [o backup offline com base no serviço de importação/exportação do Azure](backup-azure-backup-server-import-export.md):

- Você não precisa adquirir seus próprios discos e conectores compatíveis com o Azure. Azure Data Box envia os discos associados à SKU de [Data Box](https://azure.microsoft.com/services/databox/data/)selecionada.

- O backup do Azure (agente MARS) pode gravar dados de backup diretamente nas SKUs com suporte do Azure Data Box. Esse recurso elimina a necessidade de provisionar um local de preparo para os dados de backup iniciais. Você também não precisa de utilitários para formatar e copiar esses dados nos discos.

## <a name="supported-platforms"></a>Plataformas com suporte

Há suporte para as seguintes plataformas:

- Windows Server 2019 64 bit (Standard, Datacenter, Essentials)
- Windows Server 2016 64 bit (Standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho dos dados de backup e SKUs de Data Box com suporte

Há suporte para os seguintes Data Box SKUs:

| Tamanho dos dados de backup (após a compactação por MARS) \* por servidor | SKU Azure Data Box com suporte |
| --- | --- |
| \<= 7,2 TB | [Disco do Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB e <= 80 TB\*\* | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*As taxas de compactação típicas variam entre 10-20% <br>
\*\*Atinja- [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) se se você espera ter mais de 80 TB de dados de backup inicial para uma única fonte de dados.

> [!IMPORTANT]
> Os dados de backup iniciais de uma única fonte de dados devem estar contidos em um único Azure Data Box ou disco de Azure Data Box e não podem ser compartilhados entre vários dispositivos do mesmo ou de SKUs diferentes. No entanto, um Azure Data Box pode conter backups iniciais de várias fontes de dados.

## <a name="before-you-begin"></a>Antes de começar

O agente MARS em execução no DPM/MABS deve ser atualizado para a [versão mais recente](https://aka.ms/azurebackup_agent) (2.0.9171.0 ou posterior).

Verifique o seguinte:

### <a name="azure-subscription-and-required-permissions"></a>Assinatura do Azure e permissões necessárias

- Uma assinatura válida do Azure.
- O usuário destinado a executar a política de backup offline deve ser um proprietário da assinatura do Azure.
- O trabalho de Data Box e o cofre dos serviços de recuperação nos quais os dados precisam ser propagados devem estar disponíveis nas mesmas assinaturas.
    > [!NOTE]
    > É recomendável que a conta de armazenamento de destino e o cofre dos serviços de recuperação estejam na mesma região. No entanto, isso não é obrigatório.

### <a name="order-and-receive-the-data-box-device"></a>Solicitar e receber o dispositivo Data Box

Verifique se os dispositivos de Data Box necessários estão no estado *entregue* antes de disparar o backup offline. Consulte [backup data size e data Box SKUs com suporte](#backup-data-size-and-supported-data-box-skus) para solicitar o SKU mais adequado para seu requisito. Siga as etapas neste [artigo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) para solicitar e receber seus dispositivos data box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo de conta**. O servidor DPM/MABS requer uma conta que dê suporte a blobs de página que não tem suporte quando *BlobStorage* está selecionado. Selecione  **armazenamento v2 (uso geral v2)** como o **tipo de conta** ao criar a conta de armazenamento de destino para seu trabalho de Azure data box.

![Configurar Data box do Azure](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Configurar dispositivos Azure Data Box

Depois de receber o dispositivo de Azure Data Box, dependendo do SKU de Azure Data Box que você solicitou, execute as etapas nas seções apropriadas abaixo para configurar e preparar os dispositivos Data Box para o servidor DPM/MABS para identificar e transferir os dados de backup inicial.

### <a name="setup-azure-data-box-disk"></a>Instalar Azure Data Box disco

Se você solicitou um ou mais discos de Azure Data Box (até 8 TB cada), siga as etapas mencionadas [aqui](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up) para desempacotar, conectar e desbloquear seu disco de data box.

> [!NOTE]
> É possível que o servidor DPM/MABS não tenha uma porta USB. Nesse cenário, você pode conectar o disco Azure Data Box a outro servidor/cliente e expor a raiz do dispositivo como um compartilhamento de rede.

## <a name="setup-azure-data-box"></a>Azure Data Box de instalação

Se você solicitou um Azure Data Box (até 100 TB), siga as etapas mencionadas [aqui](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) para configurar sua data box.

### <a name="mount-your-azure-data-box-as-local-system"></a>Monte seu Azure Data Box como sistema local

O servidor DPM/MABS opera no contexto do sistema e, portanto, requer que o mesmo nível de privilégio seja fornecido ao caminho de montagem em que o Azure Data Box está conectado. Siga as etapas abaixo para garantir que você possa montar seu dispositivo de Data Box como sistema local usando o protocolo NFS.

1. Habilite o recurso Client for NFS no servidor DPM/MABS.
Especificar origem alternativa: *wim: D: \sources\install.wim: 4*
2. Baixe o **PsExec** de [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) para o servidor DPM/mAbs.
3. Abra um prompt de comando com privilégios elevados e execute o comando a seguir com o diretório que contém *PSExec.exe* como diretório atual.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. A janela de comando que é aberta como resultado do comando acima está no contexto do sistema local. Use esta janela de comando para executar etapas para montar o compartilhamento de blob de páginas do Azure como uma unidade de rede no seu Windows Server.
5. Siga as etapas [aqui](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) para conectar seu servidor DPM/mAbs ao dispositivo de data Box via NFS e execute o seguinte comando no prompt de comando do sistema local para montar o compartilhamento de blobs de páginas do Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Depois de montado, verifique se você pode acessar o X: do seu servidor. Se você puder, continue com a próxima seção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados de backup inicial para dispositivos Azure Data Box

1. No servidor DPM/MABS, siga as etapas para [criar um novo grupo de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019). Se você estiver adicionando uma proteção online ao grupo de proteção existente, clique com o botão direito do mouse no grupo de proteção existente e selecione **Adicionar proteção online** e iniciar na **etapa 8**.
2. Na página **selecionar membros do grupo** , especifique os computadores e as fontes que você deseja fazer backup.
3. Na página **selecionar método de proteção de dados** , especifique como deseja lidar com o backup de curto e longo prazo. Certifique-se de selecionar **desejo proteção online.**

   ![Criar novo grupo de proteção](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Na página **Selecionar objetivos de curto prazo** , especifique como você deseja fazer backup em um armazenamento de curto prazo em disco.
5. Na página **rever alocação do disco** , examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.
6. Na página **escolher método de criação de réplica** , selecione **automaticamente pela rede.**
7. Na página **escolher opções de verificação de consistência** , selecione como você deseja automatizar as verificações de consistência.
8. Na página **especificar dados de proteção online** , selecione o membro que você deseja habilitar a proteção online.

    ![Especificar dados de proteção online](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Na página **especificar agendamento de backup online** , especifique com que frequência os backups incrementais no Azure devem ocorrer.
10. Na página **especificar política de retenção online** , especifique como os pontos de recuperação criados a partir dos backups diários/semanais/mensais/anuais são mantidos no Azure.
11. Na tela **escolher replicação online** do assistente, escolha a opção **transferir usando os discos de propriedade da Microsoft** e selecione **Avançar**.

    ![Escolher a replicação online inicial](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > A opção de selecionar **transferir usando os discos de propriedade da Microsoft** não está disponível para o mAbs v3, já que o recurso está em versão prévia. Entre em contato conosco em [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) se quiser usar esse recurso para mAbs v3.

12. Entre no Azure quando solicitado, usando as credenciais de usuário que têm acesso de proprietário na assinatura do Azure. Após uma entrada bem-sucedida, a tela a seguir será exibida:

    ![Após o logon bem-sucedido](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     O servidor DPM/MABS irá então buscar os trabalhos de Data Box na assinatura que estão no estado *entregue* .

     > [!NOTE]
     > A primeira entrada leva mais tempo do que o normal. O módulo Azure PowerShell é instalado em segundo plano e também o aplicativo Azure AD é registrado.
     >
     >  - Os seguintes módulos do PowerShell estão instalados:<br>
          -AzureRM. perfil     *5.8.3*<br>
          -AzureRM. Resources   *6.7.3*<br>
          -AzureRM. armazenamento     *5.2.0*<br>
          -Azure. Storage       *4.6.1*<br>
     >  - O aplicativo do Azure AD é registrado *como \<object GUID of the user> AzureOfflineBackup_*.

13. Selecione a ordem de caixa de dados correta para a qual você desempacotau, conectou e desbloqueou o disco Data Box. Selecione **Avançar**.

    ![Selecione o data Box](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Na tela **detectar o data Box** , insira o caminho do dispositivo data box e, em seguida, selecione **detectar dispositivo**.

    ![Inserir caminho de rede](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do disco de Azure Data Box. Esse diretório deve conter um diretório com o nome *PageBlob* , como mostrado abaixo:
    >
    > ![Unidade USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Por exemplo, se o caminho do disco for `\\mydomain\myserver\disk1\` e *DISK1* contiver um diretório chamado *PageBlob*, o caminho a ser fornecido no assistente do servidor DPM/mAbs será `\\mydomain\myserver\disk1\` .
    > Se você [configurar um dispositivo Azure Data Box 100 TB](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box), forneça o seguinte como o caminho de rede para o dispositivo `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Selecione **Avançar**. Na página **Resumo** , examine as configurações e selecione **Criar grupo**.

    ![Detectar data Box](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    A tela a seguir confirma que o grupo de proteção foi criado com êxito.

    ![Grupo de proteção criado](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Selecione **fechar** na tela acima.

    Com isso, a replicação inicial dos dados ocorre no disco do DPM/MABS. Quando a proteção for concluída, o status do grupo mostrará o status de proteção como **OK** na página **proteção** .

17. Para iniciar a cópia de backup offline para seu dispositivo Azure Data Box, clique com o botão direito do mouse no **grupo de proteção**e escolha a opção **criar ponto de recuperação** . Em seguida, escolha a opção **Proteção Online**.

    ![Criar Ponto de Recuperação](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Ponto de recuperação](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   O servidor DPM/MABS começará a fazer backup dos dados selecionados para o dispositivo Azure Data Box. Isso pode levar de várias horas a alguns dias, dependendo do tamanho dos dados e da velocidade de conexão entre o servidor DPM/MABS e o Disco do Azure Data Box.

   Você pode monitorar o status do trabalho no painel **monitoramento** . Depois que o backup dos dados for concluído, você verá uma tela semelhante à seguinte:

   ![Console do administrador](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Etapas após o backup

Siga estas etapas quando o backup de dados para o Disco do Azure Data Box for bem-sucedido.

- Siga as etapas neste [artigo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) para enviar o disco Azure data box para o Azure. Se você usou um dispositivo Azure Data Box 100-TB, siga [estas etapas](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) para enviar o Azure data box para o Azure.
- [Monitore o trabalho de data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) no portal do Azure. Depois que o trabalho de Azure Data Box for *concluído*, o servidor DPM/mAbs moverá automaticamente os dados da conta de armazenamento para o cofre dos serviços de recuperação no momento do próximo backup agendado. Em seguida, ele marcará o trabalho de backup como *trabalho concluído* se um ponto de recuperação for criado com êxito.

  > [!NOTE]
  > O servidor DPM/MABS dispara os backups nos horários agendados durante a criação do grupo de proteção. No entanto, esses trabalhos sinalizarão *aguardando Azure data Box trabalho seja concluído* até a hora em que o trabalho for concluído.

- Depois que o servidor DPM/MABS criar com êxito um ponto de recuperação correspondente ao backup inicial, você poderá excluir a conta de armazenamento (ou o conteúdo específico) associada ao trabalho de Azure Data Box.

## <a name="troubleshooting"></a>Solução de problemas

O agente de Backup do Microsoft Azure (MAB) no servidor DPM cria um aplicativo do Azure AD para você, em seu locatário. Este aplicativo requer um certificado para autenticação que é criado e carregado ao configurar a política de propagação offline.

Usamos Azure PowerShell para criar e carregar o certificado para o aplicativo do Azure AD.

### <a name="issue"></a>Problema

No momento da configuração do backup offline, devido a um defeito de código conhecido no cmdlet Azure PowerShell, você não pode adicionar vários certificados ao mesmo aplicativo do Azure AD criado pelo agente MAB. Isso afetará você se você tiver configurado uma política de propagação offline para o mesmo servidor ou para outro.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Verificar se o problema é causado por essa causa raiz específica

Para garantir que a falha seja devido ao [problema](#issue) acima, execute uma das seguintes etapas:

#### <a name="step-1"></a>Etapa 1

Verifique se você vê a seguinte mensagem de erro no console do DPM/MABS no momento da configuração do backup offline:

![Agente dos serviços de recuperação do Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Etapa 2

1. Abra a pasta **Temp** no caminho de instalação (o caminho padrão da pasta Temp é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Procure o arquivo *CBUICurr* e abra o arquivo.
2. No arquivo *CBUICurr* , role até a última linha e verifique se a falha é devido a "não é possível criar uma credencial de aplicativo do Azure ad na conta do cliente. Exceção: a atualização para credencial existente com KeyId \<some guid> não é permitida ".

### <a name="workaround"></a>Solução alternativa

Para resolver esse problema, execute as etapas a seguir e repita a configuração da política.

1. Entre na página de entrada do Azure que aparece na interface do usuário do servidor DPM/MABS usando uma conta diferente com acesso de administrador na assinatura que terá o trabalho de exportação de importação criado.
2. Se nenhum outro servidor tiver a propagação offline configurada e nenhum outro servidor depender do `AzureOfflineBackup_<Azure User Id>` aplicativo, exclua esse aplicativo do **portal do Azure > Azure Active Directory > registros de aplicativo**.

   > [!NOTE]
   > Verifique se o aplicativo não `AzureOfflineBackup_<Azure User Id>` tem nenhuma outra propagação offline configurada e se nenhum outro servidor depende desse aplicativo. Vá para **configurações > chaves** na seção chaves públicas ela não deve ter outras **chaves públicas** adicionadas. Consulte a seguinte captura de tela para referência:
   >
   > ![Chaves públicas](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Etapa 3

No servidor DPM/MABS que você está tentando configurar o backup offline, execute as seguintes ações:

1. Abra a guia **gerenciar aplicativo de certificado de computador**  >  **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Selecione o certificado acima, clique com o botão direito do mouse em **todas as tarefas** e **exporte** sem chave privada, no formato. cer.
3. Vá para o aplicativo de backup offline do Azure mencionado no **ponto 2**. Nas **configurações**  >  **chaves**  >  **carregar chave pública,** carregue o certificado exportado na etapa anterior.

   ![Carregar chaves públicas](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. No servidor, abra o registro digitando **regedit** na janela **executar** .
5. Vá para o registro *Computer\HKEY \_ local \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Clique com o botão direito do mouse em **CloudBackupProvider** e adicione um novo valor de cadeia de caracteres com nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Para obter a ID de usuário do Azure, execute uma destas ações:
    >
    >- No PowerShell conectado ao Azure, execute o `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` comando.
    > - Navegue até o caminho do registro `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` com o nome *CurrentUserId*.

6. Clique com o botão direito do mouse na cadeia de caracteres adicionada na etapa acima e selecione **Modificar**. No valor, forneça a impressão digital do certificado que você exportou no **ponto 2** e selecione **OK**.
7. Para obter o valor de impressão digital, clique duas vezes no certificado e selecione **detalhes**  e role para baixo até ver o campo impressão digital. Selecione **impressão digital** e copie o valor.

   ![Certificado](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Próximas etapas

- [Propagação offline usando o próprio disco (usando o serviço de importação/exportação do Azure)](backup-azure-backup-server-import-export.md)
