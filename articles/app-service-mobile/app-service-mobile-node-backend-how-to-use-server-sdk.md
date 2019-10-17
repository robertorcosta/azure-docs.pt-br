---
title: Como trabalhar com o SDK de servidor back-end do Node.js para Aplicativos Móveis | Microsoft Docs
description: Aprenda a trabalhar com o SDK do Servidor back-end do Node.js para Aplicativos Móveis do Serviço de Aplicativo do Azure.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a3883d233bd621607ec724e0c85734b508195340
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388668"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Como usar o SDK do Node.js para Aplicativos Móveis

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
>  Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

Este artigo fornece informações detalhadas e exemplos de como trabalhar com um back-end do Node.js no recurso Aplicativos Móveis do Serviço de Aplicativo do Azure.

## <a name="Introduction"></a>Introdução

Os Aplicativos Móveis fornecem a capacidade de adicionar uma API Web de acesso a dados otimizada para mobilidade a um aplicativo Web. O SDK de Aplicativos é fornecido para aplicativos Web ASP.NET e Node.js. O SDK oferece as seguintes operações:

* Operações de tabela (ler, inserir, atualizar, excluir) para acesso a dados
* Operações de API personalizadas

Ambas as operações fornecem autenticação em todos os provedores de identidade que o Serviço de Aplicativo do Azure permite. Esses provedores incluem provedores de identidade social, como Facebook, Twitter, Google e Microsoft, assim como o Azure Active Directory para identidade corporativa.

Você pode encontrar exemplos para cada caso de uso no [diretório de exemplos no GitHub].

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK do Node.js dos Aplicativos Móveis dá suporte à versão atual de LTS do Node e posterior. Atualmente, a versão mais recente do LTS é Node v4.5.0. Outras versões do Node podem funcionar, mas não são têm suporte.

O SDK do Node.js dá suporte a dois drivers de banco de dados: 

* O driver node-mssql dá suporte ao Banco de Dados SQL do Azure e instâncias do SQL Server locais.  
* O driver sqlite3 dá suporte a bancos de dados SQLite somente em uma instância única.

### <a name="howto-cmdline-basicapp"></a>Criar um back-end do Node.js básico usando a linha de comando

Cada back-end do Node.js dos Aplicativos Móveis inicia como um aplicativo ExpressJS. ExpressJS é a estrutura de serviços Web mais popular disponível para o Node.js. Você pode criar um aplicativo [Express] básico da seguinte maneira:

1. Em um comando ou a janela do PowerShell, crie um diretório para seu projeto:

        mkdir basicapp

1. Execute `npm init` para inicializar a estrutura do pacote:

        cd basicapp
        npm init

   O comando `npm init` solicita um conjunto de perguntas para inicializar o projeto. Confira a saída de exemplo:

   ![A saída de init npm][0]

1. Instale as bibliotecas `express` e `azure-mobile-apps` do repositório npm:

        npm install --save express azure-mobile-apps

1. Crie um arquivo app.js para implementar o servidor móvel básico:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Esse aplicativo cria uma API Web otimizada para celular com um único ponto de extremidade, (`/tables/TodoItem`) que fornece acesso não autenticado a um armazenamento de dados SQL subjacente usando um esquema dinâmico. Ele é adequado para os inícios rápidos da biblioteca de clientes a seguir:

* [Início rápido do Cliente Android]
* [Início rápido do cliente Apache Cordova]
* [Início rápido do Cliente iOS]
* [Início rápido do cliente Windows Store]
* [Início rápido do cliente Xamarin.iOS]
* [Início rápido do cliente Xamarin.Android]
* [Início rápido do cliente Xamarin.Forms]

Você pode encontrar o código para esse aplicativo básico no [exemplo de aplicativo básico no GitHub].

### <a name="howto-vs2015-basicapp"></a>Criar um back-end do Node.js usando o Visual Studio 2015

O Visual Studio 2015 exige uma extensão para desenvolver aplicativos Node.js no IDE. Para começar, instale as [Ferramentas do Node.js 1.1 para Visual Studio]. Ao concluir a instalação, crie um aplicativo Express 4.x:

1. Abra a caixa de diálogo **Novo Projeto** (do **Arquivo** > **Novo** > **Projeto**).
1. Expanda **Modelos** > **JavaScript** > **Node. js**.
1. Selecione **Aplicativo Node.js Express 4 Básico do Azure**.
1. Preencha o nome do projeto. Selecione **OK**.

   ![Novo projeto do Visual Studio 2015][1]
1. Clique com o botão direito do mouse no nó **npm** e selecione **Instalar Novos Pacotes npm**.
1. Talvez seja necessário atualizar o catálogo npm após criar seu primeiro aplicativo Node.js. Selecione **Atualizar**, se necessário.
1. Digite **azure-mobile-apps** na caixa de pesquisa. Selecione o pacote **azure-mobile-apps 2.0.0** e, em seguida, selecione **Instalar Pacote**.

   ![Instalar novos pacotes npm][2]
1. Selecione **Fechar**.
1. Abra o arquivo app.js para adicionar suporte ao SDK dos Aplicativos Móveis. Na linha 6, na parte inferior das instruções da biblioteca`require`, adicione o código a seguir:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Aproximadamente na linha 27, após as outras instruções `app.use`, adicione o código a seguir:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Salve o arquivo.

1. Execute o aplicativo localmente (a API é servida no `http://localhost:3000`) ou publique no Azure.

### <a name="create-node-backend-portal"></a>Criar um back-end do Node.js usando o Portal do Azure

É possível criar um back-end de Aplicativos Móveis diretamente no [Azure portal]. Você pode concluir as etapas a seguir ou criar um cliente e um servidor juntos, acompanhando o tutorial [Criar um aplicativo móvel](app-service-mobile-ios-get-started.md). O tutorial contém uma versão simplificada dessas instruções e funciona melhor para projetos de prova de conceito.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta ao painel **Introdução**, em **Criar uma API de tabela**, escolha **Node.js** como a linguagem de back-end.
Selecione a caixa **Estou ciente de que isso substituirá todo o conteúdo do site** e, em seguida, clique em **Criar tabela TodoItem**.

### <a name="download-quickstart"></a>Baixar o projeto de código de início rápido do back-end do Node.js usando Git

Ao criar um back-end de Aplicativos Móveis do Node.js usando o painel **Início Rápido** do portal, um projeto do Node.js é criado e implantado em seu site. No portal, você pode adicionar tabelas e APIs, e editar arquivos de código para o back-end do Node.js. Também é possível usar várias ferramentas de implantação para baixar o projeto de back-end para que você possa adicionar ou modificar tabelas e APIs e, em seguida, republicar o projeto. Para obter mais informações, consulte o [Guia de implantação do Serviço de Aplicativo do Azure].

O procedimento a seguir usa um repositório Git para baixar o código do projeto de início rápido:

1. Instale o Git, caso ainda não tenha feito isso. As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Para distribuições específicas de sistema operacional e diretrizes sobre a instalação, consulte [Instalar Git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Consulte [Preparar o repositório](../app-service/deploy-local-git.md#prepare-your-repository) para habilitar o repositório Git para seu site de back-end. Anote o nome de usuário e a senha da implantação.
3. No painel do back-end de Aplicativos Móveis, anote a configuração da **URL de clone de Git**.
4. Execute o comando `git clone`, usando a URL de clone de Git. Insira sua senha, quando necessário, como no exemplo a seguir:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navegue até o diretório local (`/todolist` no exemplo anterior) e observe que os arquivos de projeto foram baixados. Localize o arquivo todoitem.json no diretório `/tables`. Esse arquivo define as permissões na tabela. Localize também o arquivo todoitem.js no mesmo diretório. Ele define os scripts de operação CRUD para a tabela.
6. Após fazer alterações em arquivos de projeto, execute os comandos a seguir para adicionar, confirmar e, em seguida, carregar as alterações no site:

        $ git commit -m "updated the table script"
        $ git push origin master

   Ao adicionar novos arquivos ao projeto, primeiro será necessário executar o comando `git add .`.

O site é publicado novamente sempre que um novo conjunto de confirmações é enviado ao site.

### <a name="howto-publish-to-azure"></a>Publicar o back-end do Node.js no Azure

O Microsoft Azure fornece muitos mecanismos para publicar o back-end do Node.js dos Aplicativos Móveis para o serviço Azure. Esses mecanismos incluem ferramentas de implantação integradas no Visual Studio, ferramentas de linha de comando e opções de implantação contínua baseada em controle do código-fonte. Para obter mais informações, consulte o [Guia de implantação do Serviço de Aplicativo do Azure].

O Serviço de Aplicativo do Azure tem recomendações específicas para aplicativos do Node.js que deverão ser revisadas antes de publicar o back-end:

* Como [especificar a versão do Node]
* Como [usar módulos do Node]

### <a name="howto-enable-homepage"></a>Habilitar uma home page para o aplicativo

Muitos aplicativos são uma combinação de aplicativos móveis e da Web. Você pode utilizar a estrutura ExpressJS para combinar as duas facetas. No entanto, às vezes você pode querer implementar apenas uma interface móvel. É útil fornecer uma home page para garantir que o serviço de aplicativo esteja em execução. Você pode fornecer sua própria home page ou habilitar uma home page temporária. Para habilitar uma home page temporária, use o código a seguir para criar uma instância de Aplicativos Móveis:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Se você quiser apenas essa opção disponível ao desenvolver localmente, é possível adicionar essa configuração ao arquivo azureMobile.js.

## <a name="TableOperations"></a>Operações de tabela

O SDK do servidor do Node.js do azure-mobile-apps fornece mecanismos para expor tabelas de dados armazenadas no Banco de Dados SQL do Azure como uma API Web. Ele fornece cinco operações:

| Operação | Descrição |
| --- | --- |
| GET /tables/*tablename* |Obter todos os registros na tabela. |
| GET /tables/*tablename*/:id |Obter um registro específico na tabela. |
| POST /tables/*tablename* |Criar um registro na tabela. |
| PATCH /tables/*tablename*/:id |Atualizar um registro na tabela. |
| DELETE /tables/*tablename*/:id |Excluir um registro na tabela. |

Essa Web API dá suporte para [OData] e estende o esquema da tabela para dar suporte à [sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Definir tabelas usando um esquema dinâmico

Antes de poder usar uma tabela, será necessário defini-la. Você pode definir tabelas usando um esquema estático (onde você define as colunas no esquema) ou dinamicamente (onde o SDK controla o esquema baseado em solicitações de entrada). Além disso, é possível controlar aspectos específicos da API Web, adicionando código JavaScript à definição.

Como melhor prática, é necessário definir cada tabela em um arquivo JavaScript no diretório `tables` e, em seguida, usar o método `tables.import()` para importar as tabelas. Estendendo o exemplo basic-app, o arquivo app.js seria ajustado:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Definir a tabela em. /tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

As tabelas usam um esquema dinâmico por padrão. Para desativar o esquema dinâmico globalmente, defina a configuração do aplicativo `MS_DynamicSchema` para falso no Portal do Azure.

Você pode encontrar um exemplo completo no [exemplo de tarefas pendentes no GitHub].

### <a name="howto-staticschema"></a>Definir tabelas usando um esquema estático

É possível definir explicitamente as colunas que serão expostas usando a API Web. O SDK do Node.js do azure-mobile-apps adiciona automaticamente as colunas extras necessárias à sincronização de dados offline para a lista fornecida por você. Por exemplo, os aplicativos clientes de inicio rápido exigem uma tabela com duas colunas: `text` (uma cadeia de caracteres) e `complete` (uma booliana).  
A tabela pode ser definida no arquivo JavaScript de definição de tabela (localizado no diretório `tables`), conforme a seguir:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Se você definir tabelas estaticamente, será necessário também chamar o `tables.initialize()` método para criar o esquema de banco de dados ao inicializar. O `tables.initialize()` método retorna uma [promessa] para que o serviço Web não atenda as solicitações antes que o banco de dados seja inicializado.

### <a name="howto-sqlexpress-setup"></a>Usar o SQL Server Express como um armazenamento de dados de desenvolvimento no computador local

O SDK do Node.js de Aplicativos Móveis fornece três opções para servir dados sem a necessidade de:

* Usar o driver de **memória** para fornecer um repositório de exemplo não persistente.
* Usar o driver **MSSQL** para fornecer um repositório de dados do SQL Server Express para desenvolvimento.
* Usar o driver **MSSQL** para fornecer um armazenamento de dados do Banco de Dados SQL do Azure para produção.

O SDK do Node.js dos Aplicativos Móveis usa o [pacote de Node.js mssql] para estabelecer e usar uma conexão tanto para o Banco de Dados SQL como para o SQL Server Express. Este pacote exige que você habilite as conexões TCP na instância do SQL Server Express.

> [!TIP]
> O driver de memória não fornece um conjunto completo de recursos para teste. Se você quiser testar o back-end local, é recomendável o uso de um armazenamento de dados do SQL Server Express e do driver MSSQL.

1. Baixe e instale o [Microsoft SQL Server 2014 Express]. Certifique-se de instalar o SQL Server 2014 Express com Tools Edition. A menos que você precise explicitamente do suporte de 64 bits, a versão de 32 bits consome menos memória durante a execução.
1. Execute o Gerenciador de Configurações do SQL Server 2014:

   a. Expanda o nó **Configuração de Rede do SQL Server** no menu de árvore.

   b. Selecione **Protocolos para SQLEXPRESS**.

   c. Clique com o botão direito do mouse em **TCP/IP** e escolha **Habilitar**. Selecione **OK** caixa de diálogo popup.

   d. Clique com o botão direito do mouse em **TCP/IP** e escolha **Propriedades**.

   e. Selecione a guia **Endereços IP**.

   f. Encontre o nó **IPAll** . No campo **Porta TCP**, insira **1433**.

      ![Configurar o SQL Server Express para TCP/IP][3]

   g. Selecione **OK**. Selecione **OK** caixa de diálogo popup.

   h. Selecione **Serviços do SQL Server** no menu de árvore.

   i. Clique com o botão direito do mouse em **SQL Server (SQLEXPRESS)** e selecione **Reiniciar**.

   j. Feche o Gerenciador de Configurações do SQL Server 2014.

1. Executar o SQL Server 2014 Management Studio e conectar-se à instância do SQL Server Express local:

   1. Clique com o botão direito do mouse em sua instância no Pesquisador de Objetos e selecione **Propriedades**.
   1. Escolha a página **Segurança** .
   1. Verifique se o **Modo de Autenticação do SQL Server e do Windows** está selecionado.
   1. Selecione **OK**.

      ![Configurar a autenticação do SQL Server Express][4]
   1. Expanda a **Segurança** > **Logons** no Pesquisador de Objetos.
   1. Clique com o botão direito do mouse em **Logons** e selecione **Novo Logon**.
   1. Insira um nome de logon. Selecione **Autenticação do SQL Server**. Insira uma senha e, em seguida, insira a mesma senha em **Confirmar senha**. A senha deve atender aos requisitos de complexidade do Windows.
   1. Selecione **OK**.

      ![Adicionar um novo usuário ao SQL Server Express][5]
   1. Clique com o botão direito do mouse no novo logon e selecione **Propriedades**.
   1. Selecione a página **Funções de Servidor**.
   1. Selecione a caixa de seleção para a função de servidor **dbcreator**.
   1. Selecione **OK**.
   1. Feche o SQL Server 2015 Management Studio.

Certifique-se de registrar o nome de usuário e senha que você selecionou. Talvez seja necessário atribuir permissões ou funções de servidor adicionais, dependendo dos requisitos do banco de dados.

O aplicativo Node.js lê a variável de ambiente `SQLCONNSTR_MS_TableConnectionString` para a cadeia de conexão para esse banco de dados. Você pode definir essa variável em seu ambiente. Por exemplo, você pode usar o PowerShell para definir essa variável de ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Acesse o banco de dados por meio de uma conexão TCP/IP. Forneça um nome de usuário e senha para a conexão.

### <a name="howto-config-localdev"></a>Configurar seu projeto para desenvolvimento local

Os Aplicativos Móveis leem um arquivo JavaScript chamado *azureMobile.js* no sistema de arquivos local. Não use esse arquivo para configurar o SDK de Aplicativos Móveis em produção. Em vez disso, use **Configurações de aplicativo** no [Azure portal].

O arquivo azureMobile.js deve exportar um objeto de configuração. As configurações mais comuns são:

* Configurações de banco de dados
* Configurações de registro em log de diagnóstico
* Configurações de CORS alternativas

Esse exemplo de arquivo **azureMobile.js** implementa as configurações de banco de dados anteriores:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

Recomendamos que você adicione **azureMobile.js** ao arquivo **.gitignore** (ou outro arquivo de controle do código-fonte a ser ignorado) para impedir que as senhas sejam armazenadas na nuvem. Sempre defina as configurações de produção em **Configuração de Aplicativo** no [Azure portal].

### <a name="howto-appsettings"></a>Definir configurações de aplicativo para seu aplicativo móvel

A maioria das configurações no arquivo azureMobile.js tem uma Configuração do Aplicativo equivalente no [Azure portal]. Use a lista a seguir para configurar seu aplicativo nas **Configurações de Aplicativo**:

| Configurações de aplicativo | Configuração azureMobile.js | Descrição | Valores válidos |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |Nome |Nome do aplicativo |string |
| **MS_MobileLoggingLevel** |logging.level |Nível de log mínimo das mensagens a serem registradas |erro, aviso, informações, detalhado, depuração, simples |
| **MS_DebugMode** |depurar |Habilita ou desabilita o modo de depuração |verdadeiro, falso |
| **MS_TableSchema** |data.schema |Nome do esquema padrão para tabelas SQL |cadeia de caracteres (padrão: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Habilita ou desabilita o modo de depuração |verdadeiro, falso |
| **MS_DisableVersionHeader** |versão (definido como indefinido) |Desabilita o cabeçalho X-ZUMO-Server-Version |verdadeiro, falso |
| **MS_SkipVersionCheck** |skipversioncheck |Desabilita a verificação de versão de API do cliente |verdadeiro, falso |

Para definir uma configuração de Aplicativo:

1. Entre no [Azure portal].
1. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e, em seguida, selecione o nome do aplicativo móvel.
1. O painel **Configurações** abre por padrão. Se não abrir, selecione **Configurações**.
1. No menu **GERAL**, selecione **Configurações de aplicativo**.
1. Role até a seção **Configurações de aplicativo**.
1. Se a configuração do seu aplicativo já existir, selecione o valor da configuração de aplicativo para editar o valor.
   Se a configuração do seu aplicativo não existir, insira a configuração de aplicativo na caixa **Chave** e o valor na caixa **Valor**.
1. Clique em **Salvar**.

A alteração da maioria das Configurações do Aplicativo requer o reinício do serviço.

### <a name="howto-use-sqlazure"></a>Usar o Banco de Dados SQL como o armazenamento de dados de produção

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

O uso do Banco de Dados SQL do Azure como armazenamento de dados é idêntico em todos os tipos de aplicativo do Serviço de Aplicativo do Azure. Se você ainda não fez isso, siga estas etapas para criar um back-end de Aplicativos Móveis:

1. Entre no [Azure portal].
1. No canto superior esquerdo da janela, selecione o botão **+NOVO** > **Web + Celular** > **Aplicativo Móvel** e, em seguida, forneça um nome para o back-end de Aplicativos Móveis.
1. Na caixa **Grupo de Recursos** , digite o mesmo nome do aplicativo.
1. O Plano do Serviço de Aplicativo padrão é selecionado. Se você quiser alterar o Plano do Serviço de Aplicativo:

   a. Selecione **Plano do Serviço de Aplicativo** >  **+Criar novo**.

   b. Forneça um nome ao novo Plano de Serviço de Aplicativo e selecione um local apropriado.

   c. Selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**.

   d. Clique no botão **Selecionar**.

   e. De volta ao painel do **Plano do Serviço de Aplicativo**, selecione **OK**.
1. Clique em **Criar**.

O provisionamento de um back-end de Aplicativos Móveis pode demorar alguns minutos. Depois que o back-end de Aplicativos Móveis for provisionado, o portal abrirá o painel **Configurações** do back-end de Aplicativos Móveis.

Você pode escolher conectar um banco de dados SQL existente ao seu back-end de Aplicativos Móveis ou criar um novo banco de dados SQL. Nesta seção, criamos um banco de dados SQL.

> [!NOTE]
> Se já houver um banco de dados no mesmo local do back-end de Aplicativos móveis, você poderá selecionar **Usar um banco de dados existente** e selecionar esse banco de dados. É recomendável o uso de um banco de dados em um local diferente devido a latências mais altas.

1. No novo back-end de Aplicativos Móveis, selecione **Configurações** > **Aplicativos Móveis** > **Dados** >  **+Adicionar**.
1. No painel **Adicionar conexão de dados**, selecione **Banco de Dados SQL - Definir configurações necessárias** > **Criar um novo banco de dados**. Insira o nome do novo banco de dados na caixa **Nome**.
1. Selecione o **Servidor**. No painel **Novo servidor**, insira um nome de servidor exclusivo na caixa **Nome do servidor** e forneça um logon do administrador do servidor e senha adequados. Certifique-se de que **Permitir que os serviços do Azure acessem o servidor** está selecionado. Selecione **OK**.

   ![Criar um banco de dados SQL do Azure][6]
1. No painel **Novo banco de dados**, selecione **OK**.
1. De volta ao painel **Adicionar conexão de dados**, selecione **Cadeia de conexão**, insira o logon e a senha que você forneceu ao criar o banco de dados. Se você usar um banco de dados existente, forneça as credenciais de logon desse banco de dados. Selecione **OK**.
1. De volta ao painel **Adicionar conexão de dados**, selecione **OK** para criar o banco de dados.

<!--- END OF ALTERNATE INCLUDE -->

A criação do banco de dados pode levar alguns minutos. Use a área **Notificações** para monitorar o progresso da implantação. Não continue até que o banco de dados seja implantado com êxito. Depois que o banco de dados é implantado, uma cadeia de conexão é criada para a instância do Banco de Dados SQL nas configurações de aplicativo de back-end de Aplicativos Móveis. É possível ver essa configuração de aplicativo em **Configurações** > **Configurações do aplicativo** > **Cadeias de conexão**.

### <a name="howto-tables-auth"></a>Exigir autenticação para acesso às tabelas

Se você quiser usar a Autenticação do Serviço de Aplicativo com o ponto de extremidade`tables`, primeiro será necessário configurar a Autenticação do Serviço de Aplicativo no [Azure portal]. Para obter mais informações, consulte o guia de configuração para o provedor de identidade que você pretende utilizar:

* [Configurar a autenticação do Microsoft Azure Active Directory]
* [Configurar a autenticação do Facebook]
* [Configurar a autenticação do Google]
* [Configurar o Microsoft authentication]
* [Configurar a autenticação do Twitter]

Cada tabela tem uma propriedade de acesso que pode ser utilizada para controlar o acesso à tabela. O exemplo a seguir mostra uma tabela estaticamente definida com autenticação necessária.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

A propriedade de acesso pode assumir um dentre três valores:

* *anônimo* indica que o aplicativo cliente tem permissão para ler os dados sem autenticação.
* *autenticado* indica que o aplicativo cliente deve enviar um token de autenticação válido com a solicitação.
* *desabilitado* indica que essa tabela está atualmente desabilitada.

Se a propriedade de acesso estiver indefinida, o acesso não autenticado será permitido.

### <a name="howto-tables-getidentity"></a>Usar declarações de autenticação com as tabelas
Você pode configurar várias declarações que são solicitadas durante a configuração da autenticação. Normalmente, essas declarações não estão disponíveis por meio do objeto `context.user` . No entanto, é possível recuperá-las utilizando o `context.user.getIdentity()` método. O método `getIdentity()` retorna uma promessa que resolve um objeto. O objeto é inserido pelo método de autenticação (`facebook`, `google`, `twitter`, `microsoftaccount`, ou `aad`).

Por exemplo, se você configurar a autenticação de Conta da Microsoft e solicitar a declaração dos endereços de email, será possível adicionar o endereço de email ao registro com o seguinte controlador de tabela:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Para ver quais declarações estão disponíveis, use um navegador da Web para exibir o ponto de extremidade do `/.auth/me` de seu site.

### <a name="howto-tables-disabled"></a>Desabilitar o acesso a operações de tabela específicas

Além de aparecer na tabela, a propriedade de acesso pode ser usada para controlar operações individuais. Há quatro operações:

* `read` é a operação RESTful GET na tabela.
* `insert` é a operação RESTful POST na tabela.
* `update` é a operação RESTful PATCH na tabela.
* `delete` é a operação RESTful DELETE na tabela.

Por exemplo, você pode querer fornecer uma tabela não autenticada somente leitura:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Ajustar a consulta usada em operações de tabela

Um requisito comum para operações de tabela é fornecer uma exibição restrita dos dados. Por exemplo, você pode fornecer uma tabela que é marcada com a ID de usuário autenticado, de modo que só possa ler ou atualizar seus próprios registros. A definição da tabela abaixo fornece essa funcionalidade:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

As operações que normalmente executam uma consulta possuem uma propriedade de consulta que podem ser ajustadas utilizando uma cláusula `where`. A propriedade de consulta é um objeto [QueryJS] usado para converter uma consulta OData em algo que pode ser processado pelo back-end de dados. Para casos de igualdade simples (como mostrado anteriormente), você pode usar um mapa. Você também pode adicionar cláusulas SQL específicas:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Configurar a exclusão reversível em uma tabela

A exclusão reversível não exclui registros. Em vez disso, ela os marca como excluídos no banco de dados definindo a coluna excluída como true. O SDK de Aplicativos Móveis remove automaticamente registros com exclusão reversível dos resultados, exceto se o SDK de Cliente Móvel usar `IncludeDeleted()`. Para configurar uma tabela para exclusão reversível, defina a propriedade `softDelete` no arquivo de definição de tabela:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Você deve estabelecer um mecanismo para excluir registros: um aplicativo cliente, um WebJob, um Azure Function ou uma API personalizada.

### <a name="howto-tables-seeding"></a>Propagar seu banco de dados com dados

Quando você está criando um novo aplicativo, é possível que você queira propagar uma tabela com dados. Você pode fazer isso no arquivo de JavaScript da definição da tabela da seguinte maneira:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

A propagação de dados ocorre apenas quando você usou o SDK de Aplicativos Móveis para criar a tabela. Se a tabela já existir no banco de dados, nenhum dado será injetado na tabela. Se o esquema dinâmico estiver ativado, o esquema será inferido dos dados propagados.

Recomendamos que você chame explicitamente o método `tables.initialize()` para criar a tabela quando o serviço começar a ser executado.

### <a name="Swagger"></a>Habilitar o suporte para o Swagger
Os Aplicativos Móveis vêm com suporte do [Swagger]. Para habilitar o suporte do Swagger, primeiro instale a swagger-ui como uma dependência:

    npm install --save swagger-ui

Você poderá, então, habilitar o suporte do Swagger no construtor dos Aplicativos Móveis:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Provavelmente você quer apenas habilitar o suporte do Swagger em edições de desenvolvimento. Você pode fazer isso, utilizando a configuração de aplicativo `NODE_ENV`:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

O ponto de extremidade `swagger` está localizado em http://*seusite*.azurewebsites.net/swagger. Você pode acessar a interface do usuário do Swagger pelo ponto de extremidade `/swagger/ui` . Se você optar por exigir autenticação em todo o aplicativo, o Swagger produzirá um erro. Para obter melhores resultados, opte por permitir solicitações não autenticadas nas configurações de Autenticação/Autorização do Serviço de Aplicativo do Azure e, então, controle a autenticação usando a propriedade `table.access`.

Também será possível adicionar a opção do Swagger ao arquivo azureMobile.js se você quiser o suporte do Swagger durante o desenvolvimento local.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Notificações por Push

Os Aplicativos Móveis integram-se aos Hubs de Notificação do Azure para que você possa enviar notificações por push direcionadas para milhões de dispositivos em todas as plataformas principais. Usando os Hubs de Notificação, você pode enviar notificações por push para iOS, Android e dispositivos com Windows. Para saber mais sobre tudo o que você pode fazer com os Hubs de Notificação, consulte a [Visão geral dos Hubs de Notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Enviar notificações por push

O código a seguir mostra como usar o objeto `push` para enviar uma notificação de push de difusão para dispositivos iOS registrados:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Ao criar um registro de envio por push modelo do cliente, você poderá enviar uma mensagem de envio de modelo para dispositivos em todas as plataformas com suporte. O código abaixo mostra como enviar uma notificação de modelo:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>Enviar notificações por push para um usuário autenticado usando marcações
Quando um usuário autenticado se registra para notificações por push, uma marca de ID de usuário é adicionada automaticamente ao registro. Usando essa marca, você pode enviar notificações por push para todos os dispositivos registrados por um usuário específico. O código abaixo obtém a SID do usuário que fez a solicitação e envia um modelo de notificação por push para cada registro de dispositivo do usuário:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Ao se registrar para notificações por push de um cliente autenticado, certifique-se de que a autenticação foi concluída antes de tentar o registro.

## <a name="CustomAPI"></a> APIs personalizadas

### <a name="howto-customapi-basic"></a>Definir uma API personalizada

Além da API de Acesso a Dados por meio do ponto de extremidade `/tables`, os Aplicativos Móveis podem fornecer cobertura API personalizada. As APIs personalizadas são definidas de forma semelhante às definições de tabela e pode acessar todos os mesmos recursos, incluindo autenticação.

Se você quiser usar a Autenticação do Serviço de Aplicativo com uma API Personalizada, primeiro será necessário configurar a Autenticação do Serviço de Aplicativo no [Azure portal]. Para obter mais informações, consulte o guia de configuração para o provedor de identidade que você pretende utilizar:

* [Configurar a autenticação do Microsoft Azure Active Directory]
* [Configurar a autenticação do Facebook]
* [Configurar a autenticação do Google]
* [Configurar o Microsoft authentication]
* [Configurar a autenticação do Twitter]

As APIs personalizadas são definidas da mesma forma que a API de tabelas:

1. Crie um diretório `api`.
1. Crie um arquivo JavaScript de definição de API no diretório `api`.
1. Use o método de importação para importar o diretório `api`.

Aqui está a definição de API do protótipo baseada no exemplo basic-app que usamos anteriormente:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Vamos tomar como exemplo, uma API que retorna a data do servidor usando o método `Date.now()`. Eis o arquivo api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Cada parâmetro é um dos verbos padrão RESTful: GET, POST, PATCH ou DELETE. O método é uma função padrão [ExpressJS middleware] que envia a saída necessária.

### <a name="howto-customapi-auth"></a>Solicitar autenticação para acesso a uma API personalizada

O SDK de Aplicativos Móveis implementa a autenticação da mesma forma para o ponto de extremidade `tables` e APIs personalizadas. Para adicionar autenticação à API desenvolvida na seção anterior, adicione uma propriedade `access`:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Você também pode especificar a autenticação em operações específicas:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

O mesmo token que é usado para o ponto de extremidade `tables` deve ser utilizado para APIs personalizadas que requerem autenticação.

### <a name="howto-customapi-auth"></a>Manipular transferências de arquivos grandes

O SDK de Aplicativos Móveis usa o [body-parser middleware](https://github.com/expressjs/body-parser) para aceitar e decodificar o conteúdo do corpo em seu envio. Você pode pré-configurar o body-parser para aceitar carregamentos de arquivos maiores:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

O arquivo é codificado em base 64 antes da transmissão. Essa codificação aumenta o tamanho do upload real (e o tamanho que você deve considerar).

### <a name="howto-customapi-sql"></a>Executar instruções SQL personalizadas

O SDK de aplicativos móveis permite acesso a todo o contexto por meio do objeto de solicitação. Você pode executar instruções SQL parametrizadas com facilidade para o provedor de dados definido:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>Pura

### <a name="howto-diagnostic-logs"></a>Depurar, diagnosticar e solucionar problemas dos Aplicativos Móveis

O Serviço de Aplicativo do Azure fornece várias técnicas de depuração e de solução de problemas para aplicativos Node.js.
Para a introdução em solução de problemas de back-end de Aplicativos Móveis do Node.js, consulte os artigos a seguir:

* [Monitorar o Serviço de Aplicativo do Azure]
* [Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure]
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio]

Os aplicativos Node.js têm acesso a uma ampla gama de ferramentas de log de diagnóstico. Internamente, o SDK do Node.js dos Aplicativos Móveis usa o [Winston] para o registro em log de diagnóstico. O registro em log é habilitado automaticamente quando você habilita o modo de depuração ou define a configuração do aplicativo `MS_DebugMode` como verdadeiro no [Azure portal]. Logs gerados aparecem nos logs de diagnóstico no [Azure portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Início rápido do Cliente Android]: app-service-mobile-android-get-started.md
[Início rápido do Cliente Apache Cordova]: app-service-mobile-cordova-get-started.md
[Início rápido do Cliente iOS]: app-service-mobile-ios-get-started.md
[Início rápido do Cliente Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Início rápido do Cliente Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Início rápido do Cliente Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Início rápido do cliente Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Configurar a autenticação do Microsoft Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Configurar a autenticação do Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Configurar a autenticação do Google]: ../app-service/configure-authentication-provider-google.md
[Configurar o Microsoft authentication]: ../app-service/configure-authentication-provider-microsoft.md
[Configurar a autenticação do Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Guia de implantação do Serviço de Aplicativo do Azure]: ../app-service/deploy-local-git.md
[Monitorar o Serviço de Aplicativo do Azure]: ../app-service/web-sites-monitor.md
[Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure]: ../app-service/troubleshoot-diagnostic-logs.md
[Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[especificar a versão do Node]: ../nodejs-specify-node-version-azure-apps.md
[usar módulos do Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promessa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemplo de aplicativo básico no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemplo de tarefas pendentes no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de exemplos no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Ferramentas do Node.js 1.1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacote de Node.js mssql]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
