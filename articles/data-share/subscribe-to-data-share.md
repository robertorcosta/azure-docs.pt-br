---
title: 'Tutorial: Aceitar e receber dados – Azure Data Share'
description: Tutorial - Aceitar e receber dados usando o Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083096"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Aceitar e receber dados usando o Azure Data Share  

Neste tutorial, você aprenderá a aceitar um convite de compartilhamento de dados usando o Azure Data Share. Você aprenderá a receber dados que estão sendo compartilhados com você, bem como habilitar um intervalo de atualização regular para sempre ter o instantâneo mais recente dos dados que estão sendo compartilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite do Azure Data Share
> * Criar um Azure Data Share
> * Especificar um destino para seus dados
> * Criar uma assinatura para seu compartilhamento de dados para a atualização agendada

## <a name="prerequisites"></a>Prerequisites
Antes de aceitar um convite de compartilhamento de dados, é preciso provisionar vários recursos do Azure, que estão listados abaixo. 

Cumpra todos os pré-requisitos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite do Azure Data Share: Um convite do Microsoft Azure com o assunto intitulado "Convite do Azure Data Share de **<yourdataprovider@domain.com>** ".
* Registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura do Azure, na qual você criará um recurso do Data Share e a assinatura do Azure em que os armazenamentos de dados do Azure de destino estão localizados.

### <a name="receive-data-into-a-storage-account"></a>Receber dados para uma conta de armazenamento: 

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função Colaborador. 
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário.  

### <a name="receive-data-into-a-sql-based-source"></a>Receber dados em uma fonte baseada em SQL:

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


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Receba os dados em um cluster do Azure Data Explorer: 

* Um cluster do Azure Data Explorer no mesmo data center do Azure que o cluster do Data Explorer do provedor de dados: Se você não tiver, poderá criar um [cluster do Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Se você não conhecer o data center do Azure do cluster do provedor de dados, poderá criá-lo posteriormente no processo.
* Permissão para gravar no cluster do Azure Data Explorer, que está presente em *Microsoft.Kusto/clusters/write*. Essa permissão existe na função Colaborador. 
* Permissão para adicionar uma atribuição de função ao cluster do Azure Data Explorer, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário. 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

1. Confira sua caixa de entrada para um convite de seu provedor de dados. O convite é do Microsoft Azure, intitulado **Convite do Azure Data Share de <yourdataprovider@domain.com>** . Anote o nome do compartilhamento para garantir que você esteja aceitando o compartilhamento correto, caso haja vários convites. 

1. Selecione **Exibir convite** para ver seu convite no Azure. Isso o levará à sua exibição de Compartilhamentos Recebidos.

   ![Convites](./media/invitations.png "Lista de convites") 

1. Selecione o compartilhamento que você deseja exibir. 

## <a name="accept-invitation"></a>Aceitar o convite
1. Examine todos os campos, incluindo os **Termos de uso**. Se você concordar com os termos de uso, será solicitado a marcar a caixa para indicar sua concordância. 

   ![Termos de uso](./media/terms-of-use.png "Termos de uso") 

1. Em *Conta do Data Share de Destino*, selecione a assinatura e o Grupo de Recursos em que você vai implantar seu Data Share. 

   Para o campo **Conta do Data Share**, selecione **Criar nova** se você não tiver uma conta do Data Share. Caso contrário, selecione uma conta do Data Share em que você gostaria de aceitar o compartilhamento de dados. 

   Para o campo **Nome do Compartilhamento Recebido**, deixe o padrão especificado pelo provedor de dados ou especifique um novo nome para o compartilhamento recebido. 

   ![Conta de compartilhamento de dados de destino](./media/target-data-share.png "Conta de compartilhamento de dados de destino") 

1. Depois de aceitar os termos de uso e especificar uma localização para seu compartilhamento, selecione *Aceitar e configurar*. Uma assinatura de compartilhamento será criada.

   Para o compartilhamento baseado em instantâneo, a próxima tela solicitará que você selecione uma conta de armazenamento de destino para a qual seus dados deverão ser copiados. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Se você preferir aceitar o convite agora, mas configurar o armazenamento de dados de destino depois, selecione *Aceitar e configurar mais tarde*. Para continuar configurando o armazenamento mais tarde, confira a página [configurar mapeamentos de conjunto de dados](how-to-configure-mapping.md) para obter as etapas detalhadas sobre como retomar a configuração do seu compartilhamento de dados. 

   Para compartilhamento no local, confira a página [configurar mapeamentos de conjunto de dados](how-to-configure-mapping.md) para obter as etapas detalhadas sobre como retomar a configuração do seu compartilhamento de dados. 

   Se você não quiser aceitar o convite, selecione *Rejeitar*. 

## <a name="configure-storage"></a>Configurar o armazenamento
1. Em *Configurações de Armazenamento de Destino*, selecione a Assinatura, o Grupo de recursos e a conta de armazenamento em que você gostaria de receber seus dados. 

   ![Configurações do armazenamento de destino](./media/target-storage-settings.png "Armazenamento de destino") 

1. Para receber atualizações regulares de seus dados, habilite as configurações de instantâneo. Observe que você só verá um agendamento de configuração de instantâneo se o provedor de dados estiver incluído no compartilhamento de dados. 

   ![Configurações de instantâneo](./media/snapshot-settings.png "Configurações de instantâneo") 

1. Clique em *Salvar*. 

> [!IMPORTANT]
> Se você estiver recebendo dados baseados em SQL e quiser receber esses dados em uma fonte baseada em SQL, visite o guia de instruções [configurar um mapeamento de conjunto de dados](how-to-configure-mapping.md) para saber como configurar um SQL Server como o destino do conjunto de dados. 

## <a name="trigger-a-snapshot"></a>Disparar um instantâneo
Essas etapas se aplicam somente ao compartilhamento baseado em instantâneo.

1. Você pode disparar um instantâneo na guia Compartilhamentos Recebidos -> Detalhes selecionando **Disparar Instantâneo**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se esta for a primeira vez que você está recebendo dados de seu provedor de dados, selecione a cópia completa. 

   ![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

1. Quando o status da última execução for *bem-sucedido*, acesse o armazenamento de dados de destino para exibir os dados recebidos. Selecione **Conjuntos de dados** e clique no link no Caminho de Destino. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de dados do consumidor") 

## <a name="view-history"></a>Exibir histórico
Para exibir um histórico de seus instantâneos, navegue até Compartilhamentos Recebidos -> Histórico. Aqui, você encontrará um histórico de todos os instantâneos gerados nos últimos 60 dias. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a aceitar e receber um Azure Data Share. Para saber mais sobre conceitos do Azure Data Share, prossiga para [Conceitos: terminologia do Azure Data Share](terminology.md).