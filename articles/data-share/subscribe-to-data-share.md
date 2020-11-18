---
title: 'Tutorial: Aceitar e receber dados – Azure Data Share'
description: Tutorial - Aceitar e receber dados usando o Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: 9031ea2d862a23df5d597b790fffc49e624e53fb
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491912"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Aceitar e receber dados usando o Azure Data Share  

Neste tutorial, você aprenderá a aceitar um convite de compartilhamento de dados usando o Azure Data Share. Você aprenderá a receber dados que estão sendo compartilhados com você, bem como habilitar um intervalo de atualização regular para sempre ter o instantâneo mais recente dos dados que estão sendo compartilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite do Azure Data Share
> * Criar um Azure Data Share
> * Especificar um destino para seus dados
> * Criar uma assinatura para seu compartilhamento de dados para a atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de aceitar um convite de compartilhamento de dados, é preciso provisionar vários recursos do Azure, que estão listados abaixo. 

Cumpra todos os pré-requisitos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite do Azure Data Share: Um convite do Microsoft Azure com o assunto intitulado "Convite do Azure Data Share de **<yourdataprovider@domain.com>** ".
* Registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura do Azure, na qual você criará um recurso do Data Share e a assinatura do Azure em que os armazenamentos de dados do Azure de destino estão localizados.

### <a name="receive-data-into-a-storage-account"></a>Receber dados para uma conta de armazenamento: 

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](../storage/common/storage-account-create.md). 
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função Colaborador. 
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário.  

### <a name="receive-data-into-a-sql-based-target"></a>Receber dados em um destino baseado em SQL:

* Permissão para gravar nos bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função Colaborador. 
* Permissão para que a identidade gerenciada do recurso do compartilhamento de dados acesse o Banco de Dados SQL do Azure ou o Azure Synapse Analytics. Isso pode ser feito executando as seguintes etapas: 
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


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Receba os dados em um cluster do Azure Data Explorer: 

* Um cluster do Azure Data Explorer no mesmo data center do Azure que o cluster do Data Explorer do provedor de dados: Se você não tiver, poderá criar um [cluster do Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal). Se você não conhecer o data center do Azure do cluster do provedor de dados, poderá criá-lo posteriormente no processo.
* Permissão para gravar no cluster do Azure Data Explorer, que está presente em *Microsoft.Kusto/clusters/write*. Essa permissão existe na função Colaborador. 
* Permissão para adicionar uma atribuição de função ao cluster do Azure Data Explorer, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário. 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

1. Você pode abrir o convite do email ou diretamente do portal do Azure. 

   Para abrir um convite do email, confira sua caixa de entrada para um convite de seu provedor de dados. O convite é do Microsoft Azure, intitulado **Convite do Azure Data Share de <yourdataprovider@domain.com>** . Clique em **Exibir convite** para ver seu convite no Azure. 

   Para abrir o convite diretamente do portal do Azure, pesquise por **Convites do Data Share** no portal do Azure. Isso levará você à lista de convites do Data Share.

   ![Lista de convites](./media/invitations.png "Lista de convites") 

1. Selecione o compartilhamento que você deseja exibir. 

## <a name="accept-invitation"></a>Aceitar o convite
1. Examine todos os campos, incluindo os **Termos de uso**. Se você concordar com os termos de uso, será solicitado a marcar a caixa para indicar sua concordância. 

   ![Termos de uso](./media/terms-of-use.png "Termos de uso") 

1. Em *Conta do Data Share de Destino*, selecione a assinatura e o Grupo de Recursos em que você vai implantar seu Data Share. 

   Para o campo **Conta do Data Share**, selecione **Criar nova** se você não tiver uma conta do Data Share. Caso contrário, selecione uma conta do Data Share em que você gostaria de aceitar o compartilhamento de dados. 

   Para o campo **Nome do Compartilhamento Recebido**, deixe o padrão especificado pelo provedor de dados ou especifique um novo nome para o compartilhamento recebido. 

   Depois de concordar com os termos de uso e especificar uma conta do Data Share para gerenciar seu compartilhamento recebido, selecione **Aceitar e configurar**. Uma assinatura de compartilhamento será criada. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Isso redirecionará você para o compartilhamento recebido na sua conta do Data Share. 

   Se você não quiser aceitar o convite, selecione *Rejeitar*. 

## <a name="configure-received-share"></a>Configurar o compartilhamento recebido
Siga as etapas abaixo para configurar o local em que deseja receber os dados.

1. Selecione a guia **Conjuntos de Dados**. Marque a caixa ao lado do conjunto de dados ao qual deseja atribuir um destino. Selecione **+ Mapear para o destino** para escolher um armazenamento de dados de destino. 

   ![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

1. Selecione um tipo de armazenamento de dados de destino no qual deseja que os dados sejam inseridos. Todos os arquivos de dados ou tabelas no armazenamento de dados de destino com o mesmo caminho e nome serão substituídos. 

   Para o compartilhamento in-loco, selecione um armazenamento de dados na Localização especificada. A Localização é o data center do Azure no qual o armazenamento de dados de origem do provedor de dados está localizado. Depois que o conjunto de dados for mapeado, siga o link no Caminho de Destino para acessar os dados.

   ![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de destino") 

1. Para o compartilhamento baseado em instantâneo, se o provedor de dados tiver criado um agendamento de instantâneo para fornecer atualização regular aos dados, você também poderá habilitar o agendamento de instantâneo selecionando a guia **Agendamento de Instantâneos**. Marque a caixa ao lado do agendamento de instantâneo e selecione **+ Habilitar**.

   ![Habilitar o agendamento de instantâneo](./media/enable-snapshot-schedule.png "Habilitar o agendamento de instantâneo")

## <a name="trigger-a-snapshot"></a>Disparar um instantâneo
Essas etapas se aplicam somente ao compartilhamento baseado em instantâneo.

1. Você pode disparar um instantâneo selecionando a guia **Detalhes** seguido de **Disparar instantâneo**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se esta for a primeira vez que você está recebendo dados de seu provedor de dados, selecione a cópia completa. 

   ![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

1. Quando o status da última execução for *bem-sucedido*, acesse o armazenamento de dados de destino para exibir os dados recebidos. Selecione **Conjuntos de dados** e clique no link no Caminho de Destino. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de dados do consumidor") 

## <a name="view-history"></a>Exibir histórico
Esta etapa só se aplica ao compartilhamento baseado em instantâneo. Para ver o histórico dos seus instantâneos, selecione a guia **Histórico**. Aqui, você encontrará o histórico de todos os instantâneos gerados nos últimos 30 dias.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o recurso não for mais necessário, acesse a página **Visão Geral do Data Share** e selecione **Excluir** para removê-lo.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a aceitar e receber um Azure Data Share. Para saber mais sobre os conceitos do Azure Data Share, prossiga para a Terminologia do Azure Data Share.

> [!div class="nextstepaction"]
> [Conceitos do Azure Data Share](terminology.md)