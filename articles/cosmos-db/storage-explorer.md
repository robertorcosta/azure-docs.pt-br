---
title: Gerenciar recursos do Azure Cosmos DB usando o Gerenciador de Armazenamento do Azure
description: Saiba como conectar o Azure Cosmos DB e gerenciar os recursos usando o Gerenciador de Armazenamento do Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: b892e4c5078b50bb865a715ddf12aebc1eb05f57
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799104"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Trabalhar com os dados usando o Gerenciador de Armazenamento do Azure

O uso do Azure Cosmos DB no Gerenciador de Armazenamento do Azure permite que os usuários gerenciem entidades do Azure Cosmos DB, manipulem dados, atualizem procedimentos armazenados e gatilhos junto com outras entidades do Azure, como os blobs de armazenamento e as filas. Agora você pode usar a mesma ferramenta para gerenciar suas diferentes entidades do Azure em um único local. Neste momento, o Gerenciador de Armazenamento do Microsoft Azure oferece suporte para contas do Cosmos, configuradas para SQL, MongoDB, Graph e APIs de Tabela.


## <a name="prerequisites"></a>Prerequisites

Uma conta do Cosmos com API SQL ou API do Azure Cosmos DB para MongoDB. Caso não tenha uma conta, você pode criar uma no Portal do Azure, conforme descrito em [Azure Cosmos DB: Compilar um aplicativo Web da API com o .NET e com o Portal do Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalação

Instale os bits mais recentes do Gerenciador de Armazenamento do Azure aqui: [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), agora há suporte para a versão do Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

1. Depois de instalar o **Gerenciador de armazenamento do Azure**, selecione o ícone de **plug-in** à esquerda, conforme mostrado na imagem a seguir:

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Selecione o ícone de plug-in para se conectar":::

2. Selecione **Adicionar uma Conta do Azure** e, em seguida, selecione **Entrar**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Conectar-se à assinatura do Azure necessária":::

2. Na caixa de diálogo **logon do Azure** , selecione **entrar**e insira suas credenciais do Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Entre em sua assinatura do Azure":::

3. Selecione sua assinatura na lista e, em seguida, selecione **aplicar**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Escolha uma ID da assinatura na lista a ser filtrada":::

    O painel do Explorer atualiza e exibe as contas na assinatura selecionada.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Selecione uma conta de Azure Cosmos DB na lista disponível":::

    Você conectou sua **Conta do Cosmos DB** à sua assinatura do Azure com êxito.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Cosmos DB** e escolha **Conectar-se ao Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão":::

2. Atualmente, só há suporte para API do SQL e de Tabela. Escolha API, colar **cadeia de conexão**, **rótulo da conta**de entrada, selecione **Avançar** para verificar o resumo e, em seguida, selecione **conectar** para conectar Azure Cosmos DB conta. Para obter informações sobre como recuperar a cadeia de conexão primária, consulte [obter a cadeia de conexão](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Insira sua cadeia de conexão":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Conectar-se ao Azure Cosmos DB usando um emulador local

Use as seguintes etapas para se conectar a um Azure Cosmos DB pelo Emulador. Atualmente, só há suporte para conta do SQL.

1. Instale o emulador e inicialize-o. Para saber como instalar o emulador, consulte [Emulador Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Cosmos DB** e escolha **Conectar-se ao Emulador Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Conectar-se a Azure Cosmos DB do emulador":::

3. Atualmente, só há suporte para API do SQL. Cole a **cadeia de conexão**, insira o rótulo da **conta**, selecione **Avançar** para verificar o resumo e, em seguida, selecione **conectar** para conectar Azure Cosmos DB conta. Para obter informações sobre como recuperar a cadeia de conexão primária, consulte [obter a cadeia de conexão](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Conectar-se a Cosmos DB na caixa de diálogo do emulador":::


## <a name="azure-cosmos-db-resource-management"></a>Gerenciamento de recursos do Azure Cosmos DB

Você pode gerenciar uma conta do Azure Cosmos DB seguindo as operações a seguir:
* Abra a conta no Portal do Azure
* Adicione o recurso à lista de Acesso Rápido
* Pesquise e atualize os recursos
* Criar e excluir bancos de dados
* Criar e excluir coleções
* Crie, edite, exclua e filtre documentos
* Gerencie procedimentos armazenados, gatilhos e funções definidas pelo usuário

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Ao clicar com o botão direito do mouse em uma assinatura no painel do Explorer, você poderá executar várias tarefas de ação rápida:

* Clique com o botão direito do mouse em uma conta ou banco de dados do Azure Cosmos DB. Você pode escolher a opção **Abrir no Portal** e gerenciar o recurso no navegador do Portal do Azure.

     :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Abrir no portal":::

* Você também pode adicionar a conta, o banco de dados e a coleção do Azure Cosmos DB no **Acesso Rápido**.
* A opção **Pesquisar daqui** habilita a pesquisa de palavra-chave no caminho selecionado.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Pesquisar daqui":::

### <a name="database-and-collection-management"></a>Gerenciamento de banco de dados e coleção

#### <a name="create-a-database"></a>Criar um banco de dados

-   Clique com o botão direito do mouse na conta do Azure Cosmos DB, escolha **Criar Banco de Dados**, insira o nome do banco de dados e pressione **Enter** para concluir.

    :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Criar um banco de dados em sua conta do Azure Cosmos":::

#### <a name="delete-a-database"></a>Excluir um banco de dados

- Clique com o botão direito do mouse no banco de dados, selecione **excluir banco de dados**e selecione **Sim** na janela pop-up. O nó do banco de dados é excluído e a conta do Azure Cosmos DB é atualizada automaticamente.

    :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Excluir o primeiro banco de dados":::

    :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Excluir os segundo bancos de dados":::

#### <a name="create-a-collection"></a>Criar uma coleção

1. Clique com o botão direito do mouse no banco de dados, escolha **criar coleção**e forneça as informações a seguir, como **ID da coleção**, **capacidade de armazenamento**, etc. Clique em **OK** para concluir.

    :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Criar primeira coleção no banco de dados":::

    :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Criar a segunda coleção no banco de dados":::

2. Selecione **ilimitado** para poder especificar a chave de partição e, em seguida, selecione **OK** para concluir.

    Se uma chave de partição for usada ao criar uma coleção, depois que a criação for concluída, o valor da chave de partição não poderá ser alterado na coleção.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Configurar uma chave de partição":::

#### <a name="delete-a-collection"></a>Excluir uma coleção

- Clique com o botão direito do mouse na coleção, selecione **excluir coleção**e, em seguida, selecione **Sim** na janela pop-up.

    O nó da coleção é excluído e o banco de dados é atualizado automaticamente.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Excluir uma das coleções":::

### <a name="document-management"></a>Gerenciamento de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos

- Para criar um novo documento, abra **documentos** na janela esquerda, selecione **novo documento**, edite o conteúdo no painel direito e, em seguida, selecione **salvar**. Você também pode atualizar um documento existente e, em seguida, selecionar **salvar**. As alterações podem ser descartadas clicando em **Descartar**.

    :::image type="content" source="./media/storage-explorer/document.png" alt-text="Criar um novo documento":::

#### <a name="delete-a-document"></a>Excluir um documento

- Clique no botão **Excluir** para excluir o documento selecionado.

#### <a name="query-for-documents"></a>Consulta de documentos

- Edite o filtro de documento inserindo uma [consulta SQL](how-to-sql-query.md) e, em seguida, selecione **aplicar**.

    :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Consulta de documentos específicos":::

### <a name="graph-management"></a>Gerenciamento de gráfico

#### <a name="create-and-modify-vertex"></a>Criar e modificar vértice

1. Para criar um novo vértice, abra o **grafo** na janela esquerda, selecione **novo vértice**, edite o conteúdo e selecione **OK**.
2. Para modificar um vértice existente, selecione o ícone de caneta no painel direito.

    :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Modificar o vértice de um grafo":::

#### <a name="delete-a-graph"></a>Excluir um gráfico

- Para excluir um vértice, selecione o ícone de Lixeira ao lado do nome do vértice.

#### <a name="filter-for-graph"></a>Filtro para gráficos

- Edite o filtro de gráfico inserindo uma [consulta Gremlin](gremlin-support.md) e, em seguida, selecione **aplicar filtro**.

    :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Executar uma consulta de grafo":::

### <a name="table-management"></a>Gerenciamento de tabela

#### <a name="create-and-modify-table"></a>Criar e modificar tabela

1. Para criar uma nova tabela, abra **entidades** na janela esquerda, selecione **Adicionar**, edite o conteúdo na caixa de diálogo **Adicionar entidade** , adicione a propriedade clicando no botão **Adicionar Propriedade**e selecione **Inserir**.
2. Para modificar uma tabela, selecione **Editar**, modifique o conteúdo e, em seguida, selecione **Atualizar**.

    :::image type="content" source="./media/storage-explorer/table.png" alt-text="Criar e modificar uma tabela":::

#### <a name="import-and-export-table"></a>Importar e exportar tabela

1. Para importar, selecione o botão **importar** e escolha uma tabela existente.
2. Para exportar, selecione o botão **Exportar** e escolha um destino.

    :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Importar ou exportar uma tabela":::

#### <a name="delete-entities"></a>Excluir entidades

- Selecione as entidades e selecione o botão **excluir**.

    :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Excluir uma tabela":::

#### <a name="query-table"></a>Tabela de consulta

- Clique no botão **consulta** , insira a condição de consulta e selecione o botão **Executar consulta** . Feche o painel de consulta clicando no botão **Fechar consulta**.

    :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Consultar dados da tabela":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerenciar procedimentos armazenados, gatilhos e UDFs

* Para criar um procedimento armazenado, na árvore à esquerda, clique com o botão direito do mouse em **procedimento armazenado**, escolha **criar procedimento armazenado**, insira um nome à esquerda, digite os scripts de procedimento armazenado na janela direita e, em seguida, selecione **criar**.
* Você também pode editar os procedimentos armazenados existentes clicando duas vezes em, fazendo a atualização e, em seguida, clicando em **Atualizar** para salvar ou selecionar **descartar** para cancelar a alteração.

    :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Criar e gerenciar procedimentos armazenados":::
* As operações para **Gatilhos** e **UDF** são semelhantes aos **Procedimentos Armazenados**.

## <a name="troubleshooting"></a>Solução de problemas

[Azure Cosmos DB no Gerenciador de Armazenamento](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) é um aplicativo autônomo que permite que você se conecte a contas do Azure Cosmos DB hospedadas nas nuvens do Azure e soberanas do Windows, do macOS ou do Linux. Ele permite a você gerenciar entidades do Azure Cosmos DB, manipular dados, atualizar procedimentos armazenados e gatilhos junto com outras entidades do Azure, como blobs e filas do Armazenamento.

Estas são as soluções de problemas comuns do Azure Cosmos DB encontradas no Gerenciador de Armazenamento.

### <a name="sign-in-issues"></a>Problemas de entrada

Antes de continuar, tente reiniciar o aplicativo e ver se os problemas podem ser corrigidos.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autoassinado na cadeia confiável

Há alguns motivos que podem exibir esse erro. Os dois mais comuns são:

+ Você está atrás de um *proxy transparente*, o que significa que alguém (como seu departamento de ti) está interceptando o tráfego HTTPS, descriptografando-o e, em seguida, criptografando-o usando um certificado autoassinado.

+ Você está executando software, como um software antivírus, que está injetando um certificado TLS/SSL autoassinado nas mensagens HTTPS recebidas.

Quando o Gerenciador de Armazenamento encontrar um desses "certificados autoassinados", ele pode não saber se a mensagem HTTPS recebida foi adulterada. No entanto, se você tiver uma cópia do certificado autoassinado, poderá ordenar o Gerenciador de Armazenamento para confiar nele. Se você não tiver certeza de quem está injetando o certificado, tente localizá-lo por conta própria seguindo as etapas abaixo:

1. Instalar o OpenSSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves serve)
     - Mac e Linux: deve estar incluído com o sistema operacional
2. Executar OpenSSL
    - Windows: vá para o diretório de instalação; em seguida, para **/bin/** e clique duas vezes em **openssl.exe**.
    - Mac e Linux: execute **openssl** em um terminal
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procurar certificados autoassinados. Se você não tiver certeza quais são autoassinados, procure onde o assunto ("s") e o emissor ("i") são os mesmos.
5.  Depois de encontrar os certificados autoassinados, copie e cole tudo desde, e incluindo, **---BEGIN CERTIFICATE---** até **---END CERTIFICATE---** em um novo arquivo .cer para cada um.
6.  Abra Gerenciador de armazenamento e vá para **Editar**certificados  >  **SSL**  >  **importar certificados**. Usando o seletor de arquivos, encontre, selecione e abra os arquivos .cer criados.

Se você não conseguir localizar todos os certificados autoassinados usando as etapas acima, envie comentários para obter mais ajuda.

#### <a name="unable-to-retrieve-subscriptions"></a>Não é possível recuperar as assinaturas

Se não for possível recuperar suas assinaturas depois de entrar com êxito:

- Verifique se sua conta tem acesso às assinaturas entrando no [portal do Azure](https://portal.azure.com/)
- Verifique se você entrou usando o ambiente certo ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemanha](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) ou Ambiente Personalizado/Azure Stack)
- Se você estiver atrás de um proxy, verifique se configurou o proxy do Gerenciador de Armazenamento apropriadamente
- Tentar remover e readicionar a conta
- Tente excluir os seguintes arquivos do diretório inicial (por exemplo,C:\Users\ContosoUser) e adicione a conta novamente:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Veja se aparecem mensagens de erro no console de ferramentas de desenvolvedor (F12) ao entrar

:::image type="content" source="./media/storage-explorer/console.png" alt-text="Verifique se há erros no console de ferramentas de desenvolvedor":::

#### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação

Se você não conseguir ver a página de autenticação:

- Dependendo da velocidade de sua conexão, talvez demore algum tempo para a página de entrada carregar. Aguarde pelo menos um minuto antes de fechar o diálogo de autenticação
- Se você estiver atrás de um proxy, verifique se configurou o proxy do Gerenciador de Armazenamento apropriadamente
- Exiba o console do desenvolvedor pressionando a tecla F12. Veja as respostas no console do desenvolvedor e tente encontrar alguma dica do motivo do não funcionamento da autenticação

#### <a name="cannot-remove-account"></a>Não é possível remover a conta

Se você não conseguir remover uma conta, ou se o link de nova autenticação não fizer nada

- Tente excluir os seguintes arquivos do diretório inicial e adicione a conta novamente:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Se você quiser remover SAS associado a recursos de Armazenamento, exclua:
  - Pasta %AppData%/StorageExplorer para Windows
  - /Usuários/<seu_nome>/Library/Application SUpport/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Você precisará inserir novamente todas as suas credenciais** se excluir esses arquivos


### <a name="httphttps-proxy-issue"></a>Problema de proxy HTTP/HTTPS

Não é possível listar os nós do Azure Cosmos DB na árvore à esquerda na hora de configurar o proxy HTTP/HTTPS no ASE. Você pode usar o Data Explorer do Azure Cosmos DB no Portal do Azure como uma solução alternativa no momento.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema de nó "Desenvolvimento" em "Local e Anexado"

Não há resposta depois de selecionar o nó "desenvolvimento" no nó "local e anexado" na árvore à esquerda.  O comportamento é esperado. O emulador de local do Azure Cosmos DB terá suporte a partir da próxima versão.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Nó de desenvolvimento":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Erro de anexação de conta do Azure Cosmos DB ao nó "Local e Anexado"

Se você vir o erro abaixo depois de anexar a conta do Azure Cosmos DB ao nó "Local e Anexado", verifique se você está usando a cadeia de conexão certa.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Erro de anexação do Azure Cosmos DB a Local e Anexado":::

### <a name="expand-azure-cosmos-db-node-error"></a>Erro de expansão do nó do Azure Cosmos DB

Você pode ver o erro abaixo ao tentar expandir os nós da árvore à esquerda.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Erro de expansão do nó do Azure Cosmos DB":::

Tente as seguintes sugestões:

- Verifique se a conta do Azure Cosmos DB está sendo provisionada e tente novamente quando a conta for criada com êxito.
- Se a conta estiver no nó "Acesso Rápido" ou "Local e Anexado", verifique se a conta foi excluída. Nesse caso, você precisará remover o nó manualmente.

## <a name="next-steps"></a>Próximas etapas

* Assista ao seguinte vídeo para aprender a usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure).
* Saiba mais sobre o Gerenciador de Armazenamento e conecte mais serviços em [Introdução ao Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
