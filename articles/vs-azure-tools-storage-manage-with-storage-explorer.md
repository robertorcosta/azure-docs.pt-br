---
title: Introdução ao Gerenciador de Armazenamento | Microsoft Docs
description: Gerenciar recursos do armazenamento do Azure com o Gerenciador de Armazenamento
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279787"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Gerenciador de Armazenamento

## <a name="overview"></a>Visão geral

O Microsoft Azure Storage Explorer é um aplicativo autônomo que facilita o trabalho com dados de armazenamento do Azure no Windows, macOS e Linux. Neste artigo, você aprenderá várias maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador do Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windows"></a>[Windows](#tab/windows)

As seguintes versões do Windows suportam o Storage Explorer:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, o Storage Explorer requer o .NET Framework 4.6.2 ou posterior.

# <a name="macos"></a>[macOS](#tab/macos)

As seguintes versões do macOS suportam o Storage Explorer:

* macOS 10.12 Sierra e versões posteriores

# <a name="linux"></a>[Linux](#tab/linux)

O Storage Explorer está disponível na [Snap Store](https://snapcraft.io/storage-explorer) para distribuições mais comuns do Linux. Recomendamos a Snap Store para esta instalação. O snap Do Storage Explorer instala todas as suas dependências e atualizações quando novas versões são publicadas na Snap Store.

Para obter distribuições suportadas, consulte a [página de instalação snapd](https://snapcraft.io/docs/installing-snapd).

O Storage Explorer requer o uso de um gerenciador de senhas. Você pode ter que se conectar a um gerenciador de senhas manualmente. Você pode conectar o Storage Explorer ao gerenciador de senhas do sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer também está disponível como um download *.tar.gz.* Você tem que instalar dependências manualmente. As seguintes distribuições de suporte ao Linux *.tar.gz* instalação:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

A instalação *.tar.gz* pode funcionar em outras distribuições, mas apenas essas listadas são oficialmente suportadas.

Para obter mais ajuda para instalar o Storage Explorer no Linux, consulte [as dependências](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) do Linux no guia de solução de problemas do Azure Storage Explorer.

---

## <a name="download-and-install"></a>Fazer o download e instalar

Para baixar e instalar o Storage Explorer, consulte [O Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço

O Gerenciador de Armazenamento fornece várias maneiras de se conectar às contas de armazenamento. Em geral, você pode:

* [Faça login no Azure para acessar suas assinaturas e seus recursos](#sign-in-to-azure)
* [Anexar um recurso específico de armazenamento ou CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Entrar no Azure

> [!NOTE]
> Para acessar totalmente os recursos após o login, o Storage Explorer requer permissões de gerenciamento (Azure Resource Manager) e de camada de dados. Isso significa que você precisa de permissões do Azure Active Directory (Azure AD), que lhe dão acesso à sua conta de armazenamento, aos contêineres na conta e aos dados nos contêineres. Se você tiver permissões apenas na camada de dados, considere [adicionar um recurso através do Azure AD](#add-a-resource-via-azure-ad). Para obter mais informações sobre as permissões específicas que o Storage Explorer requer, consulte o guia de solução de problemas do [Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. No Storage Explorer, **selecione Exibir** > **gerenciamento de contas** ou selecione o botão Gerenciar **contas.**

    ![Gerenciar Contas][1]

1. **O gerenciamento de** contas agora exibe todas as contas do Azure em que você fez o seu assinatura. Para se conectar a outra conta, **selecione Adicionar uma conta**.

1. Em **Connect to Azure Storage**, selecione uma nuvem Azure do ambiente **Azure** para fazer login em uma nuvem nacional ou em uma Pilha Azure. Depois de escolher seu ambiente, selecione **Next**.

    ![Opção para fazer login][2]

    O Storage Explorer abre uma página para você fazer login. Para obter mais informações, consulte [O connect storage explorer em uma conta de assinatura ou armazenamento do Azure Stack.](/azure-stack/user/azure-stack-storage-connect-se)

1. Depois de fazer login com sucesso com uma conta do Azure, a conta e as assinaturas do Azure associadas a essa conta aparecem em **ACCOUNT MANAGEMENT**. Selecione **Todas as assinaturas** para alternar sua seleção entre todas ou nenhuma das assinaturas listadas do Azure. Selecione as assinaturas do Azure com as quais você deseja trabalhar e selecione **Aplicar**.

    ![Selecionar assinaturas do Azure][3]

    **O EXPLORER** exibe as contas de armazenamento associadas às assinaturas selecionadas do Azure.

    ![Assinaturas do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar um recurso específico

Existem várias maneiras de anexar a um recurso no Storage Explorer:

* [Adicione um recurso via Azure AD](#add-a-resource-via-azure-ad). Se você tiver permissões apenas na camada de dados, use esta opção para adicionar um contêiner blob ou um contêiner de armazenamento Azure Data Lake Gen2 Blob.
* [Use uma seqüência de conexões](#use-a-connection-string). Use esta opção se você tiver uma seqüência de conexão com uma conta de armazenamento. O Storage Explorer suporta as seqüências [de conexão de assinatura de acesso compartilhado](storage/common/storage-dotnet-shared-access-signature-part-1.md) e chave.
* [Use um URI de assinatura de acesso compartilhado](#use-a-shared-access-signature-uri). Se você tiver um [URI de assinatura de acesso compartilhado](storage/common/storage-dotnet-shared-access-signature-part-1.md) a um contêiner blob, compartilhamento de arquivos, fila ou tabela, use-o para anexar ao recurso. Para obter um URI de assinatura de acesso compartilhado, você pode usar o [Storage Explorer](#generate-a-sas-in-storage-explorer) ou o [portal Azure](https://portal.azure.com).
* [Use um nome e uma chave.](#use-a-name-and-key) Se você conhece qualquer uma das chaves da conta da sua conta de armazenamento, você pode usar essa opção para se conectar rapidamente. Encontre suas chaves na página da conta de armazenamento selecionando**teclas de acesso** de **configurações** > no [portal Azure](https://portal.azure.com).
* [Anexar a um emulador local.](#attach-to-a-local-emulator) Se você estiver usando um dos emuladores de armazenamento Azure disponíveis, use esta opção para se conectar facilmente ao seu emulador.
* [Conecte-se a uma conta Azure Cosmos DB usando uma seqüência de conexões](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Use esta opção se você tiver uma seqüência de conexão com uma instância do CosmosDB.
* [Conecte-se ao Azure Data Lake Store por URI](#connect-to-azure-data-lake-store-by-uri). Use esta opção se você tiver um URI para Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Adicione um recurso via Azure AD

1. Selecione o símbolo **Conectar** para abrir **Conectar ao armazenamento azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Se você ainda não fez isso, use a opção **Adicionar uma conta do Azure** para fazer login na conta do Azure que tem acesso ao recurso. Depois de fazer login, retorne ao **Connect to Azure Storage**.

1. Selecione **Adicionar um recurso via Azure Active Directory (Azure AD)** e, em seguida, selecione **Next**.

1. Selecione uma conta e inquilino do Azure. Esses valores devem ter acesso ao recurso armazenamento ao qual você deseja anexar. Selecione **Avançar**.

1. Escolha o tipo de recurso que deseja anexar. Digite as informações necessárias para se conectar. 

   As informações que você inserir nesta página dependem do tipo de recurso que você está adicionando. Certifique-se de escolher o tipo correto de recurso. Depois de inseridas as informações necessárias, selecione **Next**.

1. Revise o **Resumo da Conexão** para ter certeza de que todas as informações estão corretas. Se for, selecione **Conectar**. Caso contrário, **selecione Voltar** para retornar às páginas anteriores para corrigir qualquer informação incorreta.

Depois que a conexão é adicionada com sucesso, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece em**Contêineres Blob****(Caixas** > de armazenamento **anexados)** > do local &**(Contêineres anexados).** >  Se o Storage Explorer não puder adicionar sua conexão ou se você não puder acessar seus dados depois de adicionar a conexão com sucesso, consulte o [guia de solução de problemas do Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Use uma seqüência de conexão

1. Selecione o símbolo **Conectar** para abrir **Conectar ao armazenamento azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **Usar uma seqüência de conexões**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para sua conexão e digite sua seqüência de conexões. Em seguida, selecione **Next**.

1. Revise o **Resumo da Conexão** para ter certeza de que todas as informações estão corretas. Se for, selecione **Conectar**. Caso contrário, **selecione Voltar** para retornar às páginas anteriores para corrigir qualquer informação incorreta.

Depois que a conexão é adicionada com sucesso, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece em Contas de**armazenamento** **anexadas** > do & local . Se o Storage Explorer não puder adicionar sua conexão ou se você não puder acessar seus dados depois de adicionar a conexão com sucesso, consulte o [guia de solução de problemas do Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Use um URI de assinatura de acesso compartilhado

1. Selecione o símbolo **Conectar** para abrir **Conectar ao armazenamento azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **Usar um URI de assinatura de acesso compartilhado (SAS)** e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para sua conexão e digite seu URI de assinatura de acesso compartilhado. O ponto final de serviço para o tipo de recurso que você está anexando deve ser preenchido automaticamente. Se você está usando um ponto final personalizado, é possível que não. Selecione **Avançar**.

1. Revise o **Resumo da Conexão** para ter certeza de que todas as informações estão corretas. Se for, selecione **Conectar**. Caso contrário, **selecione Voltar** para retornar às páginas anteriores para corrigir qualquer informação incorreta.

Depois que a conexão é adicionada com sucesso, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece em Contas de**armazenamento** >  **anexadas** > do & local **(Contêineres anexados)** > *o nó de serviço para o tipo de recipiente que você anexou*. Se o Storage Explorer não puder adicionar sua conexão, consulte o guia de solução de problemas do [Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Consulte o guia de solução de problemas se não puder acessar seus dados depois de adicionar a conexão com sucesso.

#### <a name="use-a-name-and-key"></a>Use um nome e uma chave

1. Selecione o símbolo **Conectar** para abrir **Conectar ao armazenamento azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **Usar o nome e a chave da conta de armazenamento**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para sua conexão.

1. Digite o nome da sua conta de armazenamento e qualquer uma de suas chaves de acesso.

1. Escolha o **domínio Armazenamento** para usar e, em seguida, selecione **Next**.

1. Revise o **Resumo da Conexão** para ter certeza de que todas as informações estão corretas. Se for, selecione **Conectar**. Caso contrário, **selecione Voltar** para retornar às páginas anteriores para corrigir qualquer informação incorreta.

Depois que a conexão é adicionada com sucesso, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece em Contas de**armazenamento** **anexadas** > do & local . Se o Storage Explorer não puder adicionar sua conexão ou se você não puder acessar seus dados depois de adicionar a conexão com sucesso, consulte o [guia de solução de problemas do Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

O Storage Explorer atualmente suporta dois emuladores de armazenamento oficiais:

* [Emulador de armazenamento azure](storage/common/storage-use-emulator.md) (somente windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o emulador estiver ouvindo as portas padrão, você poderá usar o nó **Emulador - Portas Padrão** para acessar o emulador. Procure **por Emulador - Portas padrão** em contas de armazenamento **anexadas** > &**locais**.

Se você quiser usar um nome diferente para sua conexão ou se o emulador não estiver sendo executado nas portas padrão, siga estas etapas:

1. Inicie seu emulador. Digite `AzureStorageEmulator.exe status` o comando para exibir as portas para cada tipo de serviço.

   > [!IMPORTANT]
   > O Storage Explorer não inicia automaticamente o emulador. Você deve iniciá-lo manualmente.

1. Selecione o símbolo **Conectar** para abrir **Conectar ao armazenamento azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **Anexar a um emulador local**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para sua conexão e digite as portas que seu emulador está ouvindo para cada tipo de serviço. **Anexar a um emulador local** sugere os valores de porta padrão para a maioria dos emuladores. **A porta arquivos** está em branco, porque nenhum dos emuladores oficiais atualmente suporta o serviço Arquivos. Se o emulador que você está usando fizer suporte a arquivos, você pode entrar na porta para usar. Em seguida, selecione **Next**.

1. Revise o **Resumo da Conexão** e certifique-se de que todas as informações estão corretas. Se for, selecione **Conectar**. Caso contrário, **selecione Voltar** para retornar às páginas anteriores para corrigir qualquer informação incorreta.

Depois que a conexão é adicionada com sucesso, a árvore de recursos vai para o nó que representa a conexão. O nó deve aparecer em Contas de**armazenamento** **anexadas** > & local . Se o Storage Explorer não puder adicionar sua conexão ou se você não puder acessar seus dados depois de adicionar a conexão com sucesso, consulte o [guia de solução de problemas do Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Em vez de gerenciar as contas do Azure Cosmos DB através de uma assinatura do Azure, você pode se conectar ao Azure Cosmos DB usando uma string de conexão. Para se conectar, siga essas etapas:

1. Em **EXPLORER,** **expanda o Local & Attached,** clique com o botão direito **do mouse Nas contas do Cosmos DB**e selecione **Conectar-se ao Azure Cosmos DB**.

    ![Conectar-se ao Azure Cosmos DB por uma cadeia de conexão][21]

1. Selecione a API do Azure Cosmos DB, digite os dados **da string de conexão** e selecione **OK** para conectar a conta Azure Cosmos DB. Para obter informações sobre como recuperar a seqüência de conexões, consulte [Gerenciar uma conta do Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Cadeia de conexão][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Conectar-se ao Azure Data Lake Store por URI

Você pode acessar um recurso que não está em sua assinatura. Você precisa de alguém que tenha acesso a esse recurso para lhe dar o recurso URI. Depois de fazer login, conecte-se ao Data Lake Store usando o URI. Para se conectar, siga essas etapas:

1. Em **EXPLORER,** expanda **o & Local Conectado**.

1. Clique com o botão direito do **mouse Data Lake Storage Gen1**e selecione **Conecte-se ao Data Lake Storage Gen1**.

    ![Conecte-se ao menu de contexto da Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Digite o URI e, em seguida, selecione **OK**. O Data Lake Store é exibido em **Data Lake Storage**.

    ![Conecte-se ao resultado da Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Este exemplo usa Data Lake Storage Gen1. O Azure Data Lake Storage Gen2 já está disponível. Para obter mais informações, consulte [O que é Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Gerar uma assinatura de acesso compartilhado no Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Assinatura de acesso compartilhado no nível da conta

1. Clique com o botão direito do mouse na conta de armazenamento que você deseja compartilhar e, em seguida, **selecione Obter assinatura de acesso compartilhado**.

    ![Obtenha opção de menu de contexto de assinatura de acesso compartilhado][14]

1. Em **Assinatura de acesso compartilhado,** especifique o prazo e as permissões desejadas para a conta e, em seguida, selecione **Criar**.

    ![Obtenha uma assinatura de acesso compartilhado][15]

1. Copie a **seqüência de conexão** ou a **seqüência** de consulta bruta na área de transferência.

### <a name="service-level-shared-access-signature"></a>Assinatura de acesso compartilhado em nível de serviço

Você pode obter uma assinatura de acesso compartilhado no nível de serviço. Para obter mais informações, consulte [Obter o SAS para um recipiente blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Pesquisar nas contas de armazenamento

Para encontrar um recurso de armazenamento, você pode pesquisar no painel **EXPLORER.**

À medida que você digita texto na caixa de pesquisa, o Storage Explorer exibe todos os recursos que correspondem ao valor de pesquisa que você inseriu até aquele ponto. Este exemplo mostra uma pesquisa por **pontos finais:**

![Pesquisa na conta de armazenamento][23]

> [!NOTE]
> Para acelerar sua pesquisa, use **o Gerenciamento de Contas** para desmarcar quaisquer assinaturas que não contenham o item que você está procurando. Você também pode clicar com o botão direito do mouse em um nó e selecionar **Pesquisar a partir daqui** para começar a pesquisar a partir de um nó específico.
>
>

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos de armazenamento do Azure Blob com o Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Trabalhar com os dados usando o Gerenciador de Armazenamento do Azure](./cosmos-db/storage-explorer.md)
* [Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
