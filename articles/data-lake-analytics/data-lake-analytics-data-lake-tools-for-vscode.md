---
title: Usar as Ferramentas do Azure Data Lake para Visual Studio Code
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code para criar, testar e executar scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: b554869864ff71fb31b6c4e2b891fd42c4a380b1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126475"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usar as Ferramentas do Azure Data Lake para Visual Studio Code

Neste artigo, saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code (VS Code) para criar, testar e executar scripts U-SQL. As informações também são abordadas no vídeo a seguir:

[![Player de vídeo: ferramentas de Azure Data Lake para VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas do Azure Data Lake para VS Code dão suporte para Windows, Linux e macOS.A execução local do U-SQL e a depuração local funcionam somente no Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS e Linux:

- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalar Ferramentas do Azure Data Lake

Após a instalação dos pré-requisitos, você pode instalar as Ferramentas do Azure Data Lake para VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Para instalar Ferramentas do Azure Data Lake

1. Abra o Visual Studio Code.
2. Selecione **Extensões**, no painel esquerdo. Digite **Ferramentas do Azure Data Lake** na caixa de pesquisa.
3. Selecione **Instalar** ao lado de **Ferramentas do Azure Data Lake**.

   ![Seleções para instalar as ferramentas do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.
4. Selecione **Recarregar** para ativar a extensão **Ferramentas do Azure Data Lake**.
5. Clique em **Recarregar Janela** para confirmar. Você pode ver **Azure data Lake ferramentas** no painel **extensões** .

## <a name="activate-azure-data-lake-tools"></a>Ativar Ferramentas do Azure Data Lake

Crie um arquivo .usql ou abra um existente para ativar a extensão.

## <a name="work-with-u-sql"></a>Trabalhar com o U-SQL

Você precisa abrir um arquivo U-SQL ou uma pasta para trabalhar com o U-SQL.

### <a name="to-open-the-sample-script"></a>Para abrir o script de exemplo

Abra a paleta de comandos (Ctrl + Shift + P) e insira **ADL: Abrir Script de Exemplo**. Isso abre outra instância deste exemplo. Você também pode editar, configurar e enviar um script nessa instância.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Para abrir uma pasta para o projeto U-SQL

1. No Visual Studio Code, selecione o menu **Arquivo** e, em seguida, selecione **Abrir Pasta**.
2. Especifique uma pasta e selecione **Selecionar Pasta**.
3. Selecione o menu **Arquivo** e selecione **Novo**. Um arquivo Sem título-1 é adicionado ao projeto.
4. Insira o código a seguir no arquivo Sem título-1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   SAÍDA @departments      para "/output/departments.csv" usando Outputters.Csv ();

    O script cria um arquivo departments.csv com alguns dados incluídos na pasta /output.

5. Salve o arquivo como **myUSQL.usql** na pasta aberta.

### <a name="to-compile-a-u-sql-script"></a>Para compilar um script U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Compilar Script**. Os resultados da compilação aparecem na janela **Saída**. Também é possível clicar com o botão direito do mouse em um arquivo de script e, depois, selecionar **ADL: Compilar Script** para compilar um trabalho em U-SQL. O resultado da compilação aparece no painel **Saída**.

### <a name="to-submit-a-u-sql-script"></a>Para enviar um script U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Enviar Trabalho**. Também é possível clicar com o botão direito do mouse em um arquivo de script e, depois, selecionar **ADL: Enviar Trabalho**.

Depois de enviar um trabalho em U-SQL, os logs de envio aparecerão na janela **Saída** no VS Code. O modo de exibição de trabalho é exibido no painel direito. Se o envio for bem-sucedido, a URL do trabalho também será exibida. Você pode abrir a URL do trabalho em um navegador da Web para acompanhar o status do trabalho em tempo real.

Na guia **RESUMO** da exibição do trabalho, é possível ver os detalhes do trabalho. As principais funções incluem reenviar um script, duplicar um script e abrir no portal. Na guia **DADOS** da exibição do trabalho, é possível consultar os arquivos de entrada, arquivos de saída e arquivos de recursos. Os arquivos podem ser baixados para o computador local.

![Guia Resumo na exibição do trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Guia Dados na exibição do trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Para definir o contexto padrão

É possível definir o contexto padrão para aplicar essa configuração a todos os arquivos de script caso não tenha definido parâmetros para arquivos individualmente.

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Definir contexto padrão**. Ou clique com o botão direito do mouse no editor de scripts e selecione **ADL: Definir contexto padrão**.
3. Escolha a conta, o banco de dados e o esquema que você deseja. A configuração é salva no arquivo de configuração xxx_settings.json.

   ![Conta, banco de dados e esquema definidos como o contexto padrão](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Para definir parâmetros de script

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Definir parâmetros de script**.
3. O arquivo xxx_settings.json é aberto com as propriedades a seguir:

   - **conta**: uma conta do Azure Data Lake Analytics na sua assinatura do Azure que é necessária para compilar e executar trabalhos de U-SQL. É necessário configurar a conta de computador antes de compilar e executar trabalhos do U-SQL.
   - **banco de dados**: um banco de dados em sua conta. O padrão é **Master**.
   - **esquema**: um esquema sob seu banco de dados. O padrão é **dbo**.
   - **optionalSettings**:
        - **prioridade**: o intervalo de prioridade é de 1 a 1000, sendo que 1 é a prioridade mais alta. O valor padrão é **1000**.
        - **degreeOfParallelism**: o intervalo de paralelismo é de 1 a 150. O valor padrão é o paralelismo máximo permitido em sua conta do Azure Data Lake Analytics.

   ![Conteúdos do arquivo JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Depois que a configuração é salva, a conta, o banco de dados e as informações de esquema são exibidas na barra de status no canto inferior esquerdo do arquivo .usql correspondente se você não tiver configurado o contexto padrão.

### <a name="to-set-git-ignore"></a>Para configurar Git Ignore

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Set Git Ignore**.

   - Se você não tiver um arquivo **.gitignore** na pasta de trabalho do VS Code, um arquivo chamado **.gitignore** será criado na pasta. Quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) são adicionados no arquivo por padrão. É possível fazer mais atualizações se for preciso.
   - Se você já tiver um arquivo **.gitignore** na pasta de trabalho do VS Code, a ferramenta adicionará quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) em seu arquivo **.gitignore** caso os quatro itens não tenham sido incluídos no arquivo.

   ![Itens no arquivo .gitignore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Trabalhar com arquivos code-behind: C Sharp, Python e R

As ferramentas do Azure Data Lake oferecem suporte a vários códigos personalizados. Para obter instruções, consulte [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Trabalhar com assemblies

Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).

Use as Ferramentas do Data Lake para registrar assemblies de código personalizado no catálogo do Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Para registrar um assembly

É possível registrar o assembly através do comando **ADL: Registrar Assembly** ou **ADL: Registrar Assembly (Avançado)**.

### <a name="to-register-through-the-adl-register-assembly-command"></a>Para registrar por meio do comando ADL: Registrar Assembly

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Digite **ADL: Registrar Assembly**.
3. Especifique o caminho do assembly local.
4. Selecione uma conta do Data Lake Analytics.
5. Selecione um banco de dados.

O portal será aberto em um navegador e exibirá o processo de registro do assembly.  

Uma forma mais conveniente de disparar o comando **ADL: Registrar Assembly** é clicar com o botão direito do mouse no arquivo .dll no Explorador de Arquivos.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Para registrar por meio do comando ADL: Registrar Assembly (Avançado)

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Registrar Assembly (Avançado)**.
3. Especifique o caminho do assembly local.
4. O arquivo JSON será exibido. Examine e edite as dependências do assembly e os parâmetros de recursos, se necessário. As instruções são exibidas na janela **saída** . Para prosseguir com o registro do assembly, salve (CTRL+S) o arquivo JSON.

   ![Arquivo JSON com dependências do assembly e parâmetros de recursos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- As ferramentas do Azure Data Lake detectam automaticamente se a DLL tem dependências do assembly. As dependências são exibidas no arquivo JSON depois de serem detectadas.
>- É possível carregar os recursos de DLL (por exemplo, .txt, .png e .csv) como parte do registro do assembly.

Outra maneira para disparar o comando **ADL: Registrar Assembly (Avançado)** é clicar com o botão direito no arquivo .dll no Explorador de Arquivos.

O código de U-SQL a seguir demonstra como chamar um assembly. No exemplo, o nome do assembly é *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Usar execução local e depuração local do U-SQL para usuários do Windows

A execução local do U-SQL testa seus dados locais e valida o script localmente, antes que seu código seja publicado no Data Lake Analytics. É possível usar o recurso de depuração local para concluir as seguintes tarefas antes que seu código seja enviado ao Data Lake Analytics:

- Depure o code-behind em C#.
- Explore o código.
- Valide o script localmente.

O recurso de depuração local e execução local funciona apenas em ambientes Windows e não tem suporte em sistemas operacionais baseados em Linux e macOS.

Para obter instruções sobre a execução e a depuração local, confira [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Conectar-se ao Azure

Antes de compilar e executar scripts U-SQL no Data Lake Analytics, você deve se conectar à sua conta do Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Para se conectar ao Azure usando um comando

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.

2. Digite **ADL: Logon**. As informações de logon são exibidas no canto inferior direito.

   ![Inserindo o comando de logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Notificação de entrada e autenticação](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Selecione **Copiar e Abrir** para abrir a [página da Web de logon](https://aka.ms/devicelogin). Cole o código na caixa e selecione **Continuar**.

    ![Página da Web para logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Siga as instruções para entrar na página da Web. Quando você estiver conectado, o nome da conta do Azure será exibido na barra de status no canto inferior esquerdo da janela do VS Code.

> [!NOTE]
>
> - Ferramentas do Data Lake conectam você automaticamente na próxima vez que você não sair.
> - Se sua conta tiver a autenticação por dois fatores habilitada, recomendamos o uso da autenticação por telefone em vez de usar um PIN.

Para sair, insira o comando **ADL: Logout**.

### <a name="to-connect-to-azure-from-the-explorer"></a>Para se conectar ao Azure no explorer

Expanda **AZURE DATALAKE**, selecione **Entrar no Azure** e depois execute as etapas 3 e 4 de [Para se conectar ao Azure usando um comando](#sign-in-by-command).

![Seleção de “Entrar no Azure” no explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Você não pode sair pelo explorer. Para sair, confira [Para se conectar ao Azure usando um comando](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Criar um script de extração

Você pode criar script de extração para arquivos .csv, .tsv, .txt usando o comando **ADL: Criar Script EXTRACT** ou a partir do explorer do Azure Data Lake.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Para criar um script de extração usando um comando

1. Selecione Ctrl+Shift+P para abrir a paleta de comandos e digite **ADL: Criar Script EXTRACT**.
2. Especifique o caminho completo para um arquivo do Armazenamento do Azure e pressione Enter.
3. Selecione uma conta.
4. Para um arquivo .txt, selecione um delimitador para extrair o arquivo.

![Processo para a criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

O script de extração é gerado com base nas suas entradas. Para um script que não pode detectar as colunas, escolha uma das duas opções. Caso contrário, somente um script será gerado.

![Resultado da criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Para criar um script de extração a partir do explorer

Outra maneira de criar o script de extração é clicando com o botão direito do mouse no menu (atalho) nos arquivos .csv, .tsv ou .txt no Azure Data Lake Store ou no Armazenamento de blobs do Azure.

![Comando “Criar Script EXTRACT” a partir do menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrar ao Azure Data Lake Analytics usando um comando

Você pode acessar recursos do Azure Data Lake Analytics para listar contas, acessar metadados e exibir trabalhos de análise.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Para listar as contas do Azure Data Lake Analytics em sua assinatura do Azure

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: List Accounts**. As contas são exibidas no painel **Saída**.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Para acessar os metadados do Azure Data Lake Analytics

1. Pressione CTRL+SHIFT+P e insira **ADL: Listar Tabelas**.
2. Selecione uma das contas do Data Lake Analytics.
3. Selecione um dos bancos de dados do Data Lake Analytics.
4. Selecione um dos esquemas. Você pode ver a lista de tabelas.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Para exibir trabalhos do Azure Data Lake Analytics

1. Abra a paleta de comandos (Ctrl+Shift+P) e escolha **ADL: Mostrar Trabalhos**.
2. Selecione uma conta do Data Lake Analytics ou local.
3. Aguarde a exibição da lista de trabalhos da conta.
4. Selecione um trabalho da lista de trabalhos. As Ferramentas do Data Lake abrem a exibição do trabalho no painel direito e exibem algumas informações na saída do VS Code.

    ![Lista de trabalhos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrar ao Azure Data Lake Store usando um comando

É possível usar comandos relacionados ao Azure Data Lake Store para:

- [Procurar nos recursos do Azure Data Lake Store](#list-the-storage-path)
- [Visualizar o arquivo do Azure Data Lake Store](#preview-the-storage-file)
- Carregar o arquivo diretamente no Azure Data Lake Storage no VS Code
- Baixar o arquivo diretamente do Azure Data Lake Storage no VS Code

### <a name="list-the-storage-path"></a>Listar caminho de armazenamento

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Para listar o caminho de armazenamento por meio da paleta de comandos

1. Clique com o botão direito do mouse no editor de scripts e selecione **ADL: Listar Caminho**.
2. Escolha a pasta na lista ou selecione **Inserir um caminho** ou **Procurar no caminho raiz**. (**Inserir um caminho** está sendo usado como um exemplo.)
3. Selecione sua conta do Data Lake Analytics.
4. Navegue até o caminho da pasta de armazenamento ou digite-o (por exemplo: /output/).  

A paleta de comandos lista as informações de caminho com base nas suas entradas.

![Resultados do caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Uma maneira mais adequada de listar o caminho relativo é pelo menu de atalho.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Para listar o caminho de armazenamento por meio do menu de atalho

Clique com o botão direito do mouse na cadeia de caracteres do caminho e selecione **Listar Caminho**.

![“Listar Caminho” no menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Visualizar arquivo de armazenamento

1. Clique no editor de scripts com o botão direito do mouse e selecione **ADL: Visualizar Caminho**.
2. Selecione sua conta do Data Lake Analytics.
3. Insira um caminho de arquivo do Armazenamento do Azure (por exemplo, /output/SearchLog.txt).

O arquivo é aberto no VS Code.

![Etapas e resultados da visualização do arquivo de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra maneira de visualizar um arquivo é pelo menu de atalho no caminho completo do arquivo ou no caminho relativo do arquivo no editor de scripts.

### <a name="upload-a-file-or-folder"></a>Carregar um arquivo ou uma pasta

1. Clique com o botão direito no editor de scripts e selecione **Carregar Arquivo** ou **Carregar Pasta**.
2. Escolha um ou vários arquivos caso tenha selecionado **Carregar Arquivo** ou escolha a pasta inteira caso tenha selecionado **Carregar Pasta**. Em seguida, selecione **Carregar**.
3. Escolha a pasta de armazenamento na lista ou selecione **Inserir um caminho** ou **Procurar no caminho raiz**. (**Inserir um caminho** está sendo usado como um exemplo.)
4. Selecione sua conta do Data Lake Analytics.
5. Navegue até o caminho da pasta de armazenamento ou digite-o (por exemplo: /output/).
6. Selecione **Escolher Pasta Atual** para especificar o destino do upload.

![Etapas e o resultado ao carregar um arquivo ou uma pasta](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Outra maneira de carregar arquivos para armazenamento é pelo menu de atalho no caminho completo do arquivo ou no caminho relativo do arquivo no editor de script.

É possível [monitorar o status de upload](#check-storage-tasks-status).

### <a name="download-a-file"></a>Baixar um arquivo

É possível baixar um arquivo inserindo os comandos **ADL: Baixar Arquivo** ou **ADL: Baixar Arquivo (Avançado)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Para baixar os arquivos por meio do comando ADL: Baixar Arquivo (Avançado)

1. Clique com o botão direito do mouse no editor de scripts e, depois, selecione **Baixar Arquivo (Avançado)**.
2. O VS Code exibe um arquivo JSON. É possível inserir os caminhos de arquivo e baixar vários arquivos ao mesmo tempo. As instruções são exibidas na janela **saída** . Para prosseguir com o download dos arquivos, salve (CTRL+S) o arquivo JSON.

    ![Arquivo JSON com os caminhos para download de arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A janela **Saída** exibe o status de download do arquivo.

![Janela de saída com o status do download](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

É possível [monitorar o status de download](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Para baixar os arquivos por meio do comando ADL: Baixar Arquivo

1. Clique com botão direito do mouse no editor de scripts, selecione **Baixar Arquivo** e, em seguida, selecione a pasta de destino na caixa de diálogo **Selecionar Pasta**.

1. Escolha a pasta na lista ou selecione **Inserir um caminho** ou **Procurar no caminho raiz**. (**Inserir um caminho** está sendo usado como um exemplo.)

1. Selecione sua conta do Data Lake Analytics.

1. Navegue até o caminho da pasta de armazenamento ou digite-o (por exemplo: /output/) e escolha um arquivo para baixar.

![Etapas e o resultado ao baixar um arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Outra maneira de baixar arquivos de armazenamento é pelo menu de atalho no caminho completo do arquivo ou no caminho relativo do arquivo no editor de script.

É possível [monitorar o status de download](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Verificar o status de tarefas de armazenamento

O status de upload e download aparece na barra de status. Selecione a barra de status e, em seguida, o status aparece na guia **SAÍDA**.

![Barra de status e detalhes de saída](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrar ao Azure Data Lake Analytics pelo explorer

Após o logon, todas as assinaturas em sua conta do Azure são listadas no painel esquerdo do **AZURE DATALAKE**.

![Explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navegação dos metadados do Data Lake Analytics

Expanda sua assinatura do Azure. No nó **Bancos de dados U-SQL**, é possível procurar por meio do banco de dados do U-SQL e exibir pastas como **Esquemas**, **Credenciais**, **Assemblies**, **Tabelas** e **Índice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gerenciamento de entidade dos metadados do Data Lake Analytics

Expanda o **Bancos de dados U-SQL**. É possível criar um banco de dados, esquema, tabela, tipo de tabela, índice ou estatística clicando com o botão direito do mouse no nó correspondente e, em seguida, selecionando **Script para Criar** no menu de atalho. Na página de script aberta, edite o script de acordo com suas necessidades. Em seguida, envie o trabalho clicando com o botão direito do mouse e selecionando **ADL: Enviar Trabalho**.

Depois de terminar de criar o item, clique com botão direito no nó e selecione **Atualizar** para mostrar o item. Também é possível excluir o item clicando com o botão direito do mouse ele e selecionando **Excluir**.

![Comando “Script para Criar” no menu de atalho no Explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Página de script para o novo item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registro de assembly do Data Lake Analytics

É possível registrar um assembly no banco de dados correspondente clicando com o botão direito do mouse no nó **Assemblies** e selecionando **Registrar assembly**.

![Comando “Registrar assembly” no menu de atalho para o nó de Assemblies](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrar ao Azure Data Lake Store pelo explorer

Navegue até **Data Lake Store**:

- É possível clicar com o botão direito do mouse no nó da pasta e usar os comandos **Atualizar**, **Excluir**, **Carregar**, **Carregar Pasta**, **Copiar Caminho Relativo** e **Copiar Caminho Completo** no menu de atalho.

   ![Comandos do menu de atalho para um nó de pasta no explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- É possível clicar com o botão direito do mouse no nó do arquivo e usar os comandos **Visualizar**, **Baixar**, **Excluir**, **Criar Script EXTRACT** (disponível apenas para arquivos CSV, TSV e TXT), **Copiar Caminho Relativo** e **Copiar Caminho Completo** no menu de atalho.

   ![Comandos do menu de atalho para um nó de arquivo no explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrar ao armazenamento de Blobs do Azure pelo explorer

Navegar até o armazenamento de Blobs:

- É possível clicar com o botão direito do mouse no nó do contêiner de blobs e usar os comandos **Atualizar**, **Excluir Contêiner de Blobs** e **Carregar Blob** no menu de atalho.

   ![Comandos do menu de atalho para um nó do contêiner de blob no armazenamento de Blobs](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- É possível clicar com o botão direito do mouse no nó da pasta e usar os comandos **Atualizar** e **Carregar Blob** no menu de atalho.

   ![Comandos do menu de atalho para um nó da pasta no armazenamento de Blobs](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- É possível clicar com o botão direito do mouse no nó do arquivo e usar os comandos **Visualizar/Editar**, **Baixar**, **Excluir**, **Criar Script EXTRACT** (disponível apenas para arquivos CSV, TSV e TXT), **Copiar Caminho Relativo** e **Copiar Caminho Completo** no menu de atalho.

    ![Comandos do menu de atalho para um nó do arquivo no armazenamento de Blobs](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Abrir o explorer do Data Lake no portal

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **Abrir Gerenciador do Armazenamento do Azure da Web** ou clique com o botão direito do mouse em um caminho relativo, ou no caminho completo no editor de scripts, e escolha **Abrir o Gerenciador de Armazenamento do Azure da Web**.
3. Selecione uma conta do Data Lake Analytics.

As Ferramentas do Data Lake abrem o caminho do Armazenamento do Azure no portal do Azure. Você pode encontrar o caminho e visualizar o arquivo na Web.

## <a name="additional-features"></a>Recursos adicionais

As Ferramentas do Data Lake para VS Code dão suporte aos seguintes recursos:

- **Preenchimento automático de IntelliSense**: as sugestões são exibidas em janelas pop-up ao redor dos itens, como palavras-chave, métodos e variáveis. Os diferentes ícones representam diferentes tipos de objetos:

  - Tipo de dados de Scala
  - Tipos de dados complexos
  - UDTs Internos
  - Coleções e classes .Net
  - Expressões em C#
  - UDFs, UDOs e UDAAGs internos de C#
  - Funções em U-SQL
  - Função de janelas do U-SQL

    ![Tipos de objeto do IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Preenchimento automático do IntelliSense em metadados de data Lake Analytics**: o data Lake Tools baixa as informações de metadados de data Lake Analytics localmente. O recurso IntelliSense preenche automaticamente os objetos de metadados do Data Lake Analytics. Esses objetos incluem o banco de dados, esquema, tabela, exibição, função com valor de tabela, procedimentos e assemblies do C#.

  ![Metadados do IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Marcador de erro do IntelliSense**: as Ferramentas do Data Lake sublinham os erros de edição de U-SQL e C#.
- **Destaques de sintaxe**: as Ferramentas do Data Lake usam cores diferentes para diferenciar itens, como variáveis, palavras-chave, tipo de dados e funções.

    ![Sintaxe com várias cores](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Recomenda-se que você atualize para a versão 2.3.3000.4 ou posterior das Ferramentas do Azure Data Lake para Visual Studio. As versões anteriores não estão disponíveis para download e foram preteridas.  

## <a name="next-steps"></a>Próximas etapas

- [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Execução local e depuração local do U-SQL com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
