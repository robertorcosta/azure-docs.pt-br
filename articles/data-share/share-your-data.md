---
title: 'Tutorial: Compartilhar fora de sua organização – Azure Data Share'
description: Tutorial – compartilhar dados com clientes e parceiros usando o Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083057"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutorial: Compartilhar dados usando o Azure Data Share  

Neste tutorial, você aprenderá a configurar um novo Azure Data Share e começará a compartilhar seus dados com clientes e parceiros fora da organização do Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Data Share.
> * Adicionar conjuntos de dados a seu Data Share.
> * Habilitar uma agenda de instantâneo para seu Data Share. 
> * Adicionar destinatários a seu Data Share. 

## <a name="prerequisites"></a>Prerequisites

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de email de logon do Azure de seus destinatários (usar o alias de email não funcionará).
* Se o armazenamento de dados do Azure de origem estiver em uma assinatura do Azure diferente daquela que você usará para criar o recurso do Data Share, registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura em que o armazenamento de dados do Azure está localizado. 

### <a name="share-from-a-storage-account"></a>Compartilhar de uma conta de armazenamento:

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função Colaborador.
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário. 


### <a name="share-from-a-sql-based-source"></a>Compartilhar de uma fonte baseada em SQL:

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

### <a name="share-from-azure-data-explorer"></a>Compartilhar do Azure Data Explorer
* Um cluster do Azure Data Explorer com bancos de dados que você deseja compartilhar.
* Permissão para gravar no cluster do Azure Data Explorer, que está presente em *Microsoft.Kusto/clusters/write*. Essa permissão existe na função Colaborador.
* Permissão para adicionar uma atribuição de função ao cluster do Azure Data Explorer, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma conta do Data Share

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

1. Pesquise *Data Share*.

1. Selecione Data Share (versão prévia) e selecione **Criar**.

1. Preencha os detalhes básicos de seu recurso do Azure Data Share com as informações a seguir. 

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *datashareacount* | Especifique um nome para sua conta de compartilhamento de dados. |
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que deseja usar para sua conta de compartilhamento de dados.|
    | Resource group | *test-resource-group* | Use um grupo de recursos existente ou crie um novo. |
    | Location | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | | |

1. Selecione **Criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos. 

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

## <a name="create-a-data-share"></a>Criar um Data Share

1. Navegue até sua página de Visão Geral do Data Share.

    ![Compartilhar seus dados](./media/share-receive-data.png "Compartilhar seus dados") 

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes para seu Data Share. Especifique um nome, um tipo de compartilhamento, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Inserir detalhes do Compartilhamento") 

1. Selecione **Continuar**

1. Para adicionar Conjuntos de Dados a seu Data Share, selecione **Adicionar Conjuntos de Dados**. 

    ![Conjunto de dados](./media/datasets.png "Conjunto de dados")

1. Selecione o tipo de conjunto de dados que você deseja adicionar. Você verá uma lista diferente de tipos de conjuntos de dados, dependendo do tipo de compartilhamento (instantâneo ou in-loco) que você selecionou na etapa anterior. Se estiver compartilhando de um Banco de Dados SQL do Azure ou de um SQL Data Warehouse do Azure, você será solicitado a fornecer algumas credenciais do SQL. Autentique usando o usuário criado como parte dos pré-requisitos.

    ![AddDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Navegue até o objeto que você gostaria de compartilhar e selecione "Adicionar Conjuntos de Dados". 

    ![SelectDatasets](./media/select-datasets.png "Selecionar conjuntos de dados")    

1. Na guia Destinatários, insira os endereços de email de seu Consumidor de Dados selecionando "+ Adicionar Destinatário". 

    ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**

1. Se você tiver selecionado o tipo de compartilhamento de instantâneo, poderá configurar a agenda de instantâneo para fornecer atualizações de seus dados para o consumidor de dados. 

    ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**

1. Na guia Examinar + Criar, examine o Conteúdo do Pacote, Configurações, Destinatários e Configurações de Sincronização. Escolha **Criar**

O Azure Data Share agora foi criado e o destinatário de seu Compartilhamento de Dados agora está pronto para aceitar seu convite. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um Azure Data Share e convidar destinatários. Para saber mais sobre como um Consumidor de Dados pode aceitar e receber um compartilhamento de dados, prossiga para o tutorial [Aceitar e receber dados](subscribe-to-data-share.md). 
