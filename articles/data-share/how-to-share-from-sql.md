---
title: Compartilhar e receber dados do Azure Synapse
description: Saiba como compartilhar e receber dados do banco de dados SQL do Azure e do Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: 2cb06b6802fdc4cebd04f687266f5ac08dde82c0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269984"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Compartilhar e receber dados do Azure Synapse

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

O compartilhamento de dados do Azure dá suporte ao compartilhamento baseado em instantâneo do Azure SQL Database e ao Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW). Este artigo explica como compartilhar e receber dados dessas fontes.

O compartilhamento de dados do Azure dá suporte ao compartilhamento de tabelas ou exibições do banco de dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW). Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Storage Gen2 ou no armazenamento de BLOBs do Azure como arquivos CSV ou parquet, bem como no banco de dados SQL do Azure e no Azure Synapse Analytics como tabelas.

Ao aceitar dados no Azure Data Lake Store Gen2 ou no armazenamento de BLOBs do Azure, instantâneos completos substituem o conteúdo do arquivo de destino, se já existir.
Quando os dados são recebidos na tabela e se a tabela de destino ainda não existir, o compartilhamento de dados do Azure criará a tabela SQL com o esquema de origem. Se uma tabela de destino já existir com o mesmo nome, ela será descartada e substituída pelo instantâneo completo mais recente. Não há suporte para instantâneos incrementais no momento.

## <a name="share-data"></a>Compartilhar dados

### <a name="prerequisites-to-share-data"></a>Pré-requisitos para compartilhar dados

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de email de logon do Azure de seus destinatários (usar o alias de email não funcionará).
* Se o armazenamento de dados do Azure de origem estiver em uma assinatura do Azure diferente daquela que você usará para criar o recurso do Data Share, registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura em que o armazenamento de dados do Azure está localizado. 

### <a name="prerequisites-for-sql-source"></a>Pré-requisitos para a origem do SQL

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL Data Warehouse do Azure) com tabelas e exibições que você deseja compartilhar.
* Permissão para gravar em bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função Colaborador.
* Permissão para o compartilhamento de dados acessar o data warehouse. Isso pode ser feito executando as seguintes etapas: 
    1. Defina-se como o Administrador do Azure Active Directory para o SQL Server.
    1. Conecte-se ao Banco de Dados SQL do Azure/Data Warehouse usando o Azure Active Directory.
    1. Use o Editor de Consultas (versão prévia) para executar o script a seguir para adicionar a Identidade Gerenciada do recurso do Data Share como um db_datareader. Você deve se conectar usando o Active Directory e não a autenticação do SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observe que *<share_acc_name>* é o nome do seu recurso do Data Share. Se você ainda não tiver criado um recurso do Data Share, poderá voltar para esse pré-requisito mais tarde.  

* Um Usuário do Banco de Dados SQL do Azure com acesso 'db_datareader' para navegar e selecionar as tabelas e/ou exibições que você deseja compartilhar. 

* Acesso ao Firewall do SQL Server de IP do cliente. Isso pode ser feito executando as seguintes etapas: 
    1. No SQL Server, no portal do Azure, navegue até *Firewalls e redes virtuais*
    1. Clique na alternância **ligar** para permitir o acesso aos Serviços do Azure.
    1. Clique em **+ Adicionar IP de cliente** e clique em **Salvar**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP. 

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Criar uma conta do Data Share

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. Selecione o botão de menu no canto superior esquerdo do portal e, em seguida, selecione **criar um recurso** (+).

1. Pesquise *Data Share*.

1. Selecione Data Share e **Criar**.

1. Preencha os detalhes básicos de seu recurso do Azure Data Share com as informações a seguir. 

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que deseja usar para sua conta de compartilhamento de dados.|
    | Grupo de recursos | *test-resource-group* | Use um grupo de recursos existente ou crie um novo. |
    | Localização | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | Nome | *datashareaccount* | Especifique um nome para sua conta de compartilhamento de dados. |
    | | |

1. Selecione **Examinar + criar** e **Criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos. 

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

### <a name="create-a-share"></a>Criar um compartilhamento

1. Navegue até sua página de Visão Geral do Data Share.

    ![Compartilhar seus dados](./media/share-receive-data.png "Compartilhar seus dados") 

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes do seu compartilhamento. Especifique um nome, um tipo de compartilhamento, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Inserir detalhes do Compartilhamento") 

1. Selecione **Continuar**.

1. Para adicionar conjuntos de itens ao seu compartilhamento, selecione **Adicionar conjuntos**de os. 

    ![Adicionar conjuntos de itens ao seu compartilhamento](./media/datasets.png "Conjunto de dados")

1. Selecione o tipo de conjunto de dados que você deseja adicionar. Você verá uma lista diferente de tipos de conjuntos de dados, dependendo do tipo de compartilhamento (instantâneo ou in-loco) que você selecionou na etapa anterior. 

    ![AddDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Selecione o SQL Server, forneça as credenciais e selecione **Avançar** para navegar até o objeto que você deseja compartilhar e selecione "Adicionar conjuntos de os". 

    ![SelectDatasets](./media/select-datasets-sql.png "Selecionar conjuntos de dados")    

1. Na guia Destinatários, insira os endereços de email de seu Consumidor de Dados selecionando "+ Adicionar Destinatário". 

    ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**.

1. Se você tiver selecionado o tipo de compartilhamento de instantâneo, poderá configurar a agenda de instantâneo para fornecer atualizações de seus dados para o consumidor de dados. 

    ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**.

1. Na guia Examinar + Criar, examine o Conteúdo do Pacote, Configurações, Destinatários e Configurações de Sincronização. Selecione **Criar**.

O Azure Data Share agora foi criado e o destinatário de seu Compartilhamento de Dados agora está pronto para aceitar seu convite. 

## <a name="receive-data"></a>Receber dados

### <a name="prerequisites-to-receive-data"></a>Pré-requisitos para receber dados
Antes de aceitar um convite de compartilhamento de dados, é preciso provisionar vários recursos do Azure, que estão listados abaixo. 

Cumpra todos os pré-requisitos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite do Azure Data Share: Um convite do Microsoft Azure com o assunto intitulado "Convite do Azure Data Share de **<yourdataprovider@domain.com>** ".
* Registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura do Azure, na qual você criará um recurso do Data Share e a assinatura do Azure em que os armazenamentos de dados do Azure de destino estão localizados.

### <a name="prerequisites-for-target-storage-account"></a>Pré-requisitos para a conta de armazenamento de destino
Se você optar por receber dados no armazenamento do Azure, abaixo está a lista de pré-requisitos.

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função Colaborador. 
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário.  

### <a name="prerequisites-for-sql-target"></a>Pré-requisitos para o destino do SQL
Se você optar por receber dados para o Azure SQL Database, a análise de Synapse do Azure, abaixo, será a lista de pré-requisitos.

* Permissão para gravar nos bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função Colaborador. 
* Permissão para a identidade gerenciada do recurso do compartilhamento de dados acessar o Banco de Dados SQL do Azure ou o SQL Data Warehouse do Azure. Isso pode ser feito executando as seguintes etapas: 
    1. Defina-se como o Administrador do Azure Active Directory para o SQL Server.
    1. Conecte-se ao Banco de Dados SQL do Azure/Data Warehouse usando o Azure Active Directory.
    1. Use o Editor de Consultas (versão prévia) para executar o script a seguir para adicionar a Identidade Gerenciada do Data Share como um 'db_datareader, db_datawriter, db_ddladmin'. Você deve se conectar usando o Active Directory e não a autenticação do SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Observe que *<share_acc_name>* é o nome do seu recurso do Data Share. Se você ainda não tiver criado um recurso do Data Share, poderá voltar para esse pré-requisito mais tarde.         

* Acesso ao Firewall do SQL Server de IP do cliente. Isso pode ser feito executando as seguintes etapas: 
    1. No SQL Server, no portal do Azure, navegue até *Firewalls e redes virtuais*
    1. Clique na alternância **ligar** para permitir o acesso aos Serviços do Azure.
    1. Clique em **+ Adicionar IP de cliente** e clique em **Salvar**. O endereço IP do cliente está sujeito a alterações. Esse processo poderá precisar ser repetido na próxima vez que você receber dados em um destino SQL do portal do Azure. Você também pode adicionar um intervalo de IP. 

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="open-invitation"></a>Convite aberto

1. Você pode abrir o convite do email ou diretamente do portal do Azure. 

   Para abrir um convite do email, confira sua caixa de entrada para um convite de seu provedor de dados. O convite é do Microsoft Azure, intitulado **Convite do Azure Data Share de <yourdataprovider@domain.com>** . Clique em **Exibir convite** para ver seu convite no Azure. 

   Para abrir o convite diretamente do portal do Azure, pesquise por **Convites do Data Share** no portal do Azure. Isso levará você à lista de convites do Data Share.

   ![Lista de convites](./media/invitations.png "Lista de convites") 

1. Selecione o compartilhamento que você deseja exibir. 

### <a name="accept-invitation"></a>Aceitar o convite
1. Examine todos os campos, incluindo os **Termos de uso**. Se você concordar com os termos de uso, será solicitado a marcar a caixa para indicar sua concordância. 

   ![Termos de uso](./media/terms-of-use.png "Termos de uso") 

1. Em *Conta do Data Share de Destino*, selecione a assinatura e o Grupo de Recursos em que você vai implantar seu Data Share. 

   Para o campo **Conta do Data Share**, selecione **Criar nova** se você não tiver uma conta do Data Share. Caso contrário, selecione uma conta do Data Share em que você gostaria de aceitar o compartilhamento de dados. 

   Para o campo **Nome do Compartilhamento Recebido**, deixe o padrão especificado pelo provedor de dados ou especifique um novo nome para o compartilhamento recebido. 

   Depois de concordar com os termos de uso e especificar uma conta do Data Share para gerenciar seu compartilhamento recebido, selecione **Aceitar e configurar**. Uma assinatura de compartilhamento será criada. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Isso redirecionará você para o compartilhamento recebido na sua conta do Data Share. 

   Se você não quiser aceitar o convite, selecione *Rejeitar*. 

### <a name="configure-received-share"></a>Configurar o compartilhamento recebido
Siga as etapas abaixo para configurar o local em que deseja receber os dados.

1. Selecione a guia **Conjuntos de Dados**. Marque a caixa ao lado do conjunto de dados ao qual deseja atribuir um destino. Selecione **+ Mapear para o destino** para escolher um armazenamento de dados de destino. 

   ![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

1. Selecione um armazenamento de dados de destino no qual você deseja que os dados entrem. Todos os arquivos de dados ou tabelas no armazenamento de dados de destino com o mesmo caminho e nome serão substituídos. 

   ![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de dados de destino") 

1. Para o compartilhamento baseado em instantâneo, se o provedor de dados tiver criado um agendamento de instantâneo para fornecer atualização regular aos dados, você também poderá habilitar o agendamento de instantâneo selecionando a guia **Agendamento de Instantâneos**. Marque a caixa ao lado do agendamento de instantâneo e selecione **+ Habilitar**.

   ![Habilitar o agendamento de instantâneo](./media/enable-snapshot-schedule.png "Habilitar o agendamento de instantâneo")

### <a name="trigger-a-snapshot"></a>Disparar um instantâneo
Essas etapas se aplicam somente ao compartilhamento baseado em instantâneo.

1. Você pode disparar um instantâneo selecionando a guia **Detalhes** seguido de **Disparar instantâneo**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se esta for a primeira vez que você está recebendo dados de seu provedor de dados, selecione a cópia completa. Para fontes SQL, somente o instantâneo completo tem suporte.

   ![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

1. Quando o status da última execução for *bem-sucedido*, acesse o armazenamento de dados de destino para exibir os dados recebidos. Selecione **Conjuntos de dados** e clique no link no Caminho de Destino. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de dados do consumidor") 

### <a name="view-history"></a>Exibir histórico
Esta etapa só se aplica ao compartilhamento baseado em instantâneo. Para ver o histórico dos seus instantâneos, selecione a guia **Histórico**. Aqui, você encontrará o histórico de todos os instantâneos gerados nos últimos 30 dias. 

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como compartilhar e receber dados da conta de armazenamento usando o serviço de compartilhamento de dados do Azure. Para saber mais sobre o compartilhamento de outras fontes de dados, continue com [os armazenamentos de dados com suporte](supported-data-stores.md).

