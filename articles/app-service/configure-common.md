---
title: Configurar aplicativos no portal
description: Aprenda a configurar configurações comuns para um aplicativo app service no portal Azure. Configurações do aplicativo, strings de conexão, plataforma, pilha de idiomas, contêiner, etc.
keywords: serviço de aplicativo azure, aplicativo web, configurações do aplicativo, variáveis de ambiente
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: ce0a170a629f347e2687a2e9f63fb3438fe2bd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280164"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configure um aplicativo de serviço de aplicativo no portal Azure

Este tópico explica como configurar configurações comuns para aplicativos web, back-end móvel ou aplicativo API usando o [portal Azure].

## <a name="configure-app-settings"></a>Definir configurações de aplicativo

No App Service, as configurações do aplicativo são variáveis passadas como variáveis de ambiente para o código do aplicativo. Para aplicativos Linux e contêineres personalizados, o App `--env` Service passa as configurações do aplicativo para o contêiner usando o sinalizador para definir a variável de ambiente no contêiner.

No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. 

![Procure serviços de aplicativos](./media/configure-common/search-for-app-services.png)

No menu esquerdo do aplicativo, selecione **Configuração** > **Configuração Configuração configuração**.

![Configurações do aplicativo](./media/configure-common/open-ui.png)

Para desenvolvedores ASP.NET e ASP.NET Core, definir configurações de `<appSettings>` aplicativos no App Service é como configurá-los em *Web.config* ou *appsettings.json,* mas os valores no App Service sobrepõem os do *Web.config* ou *appsettings.json*. Você pode manter as configurações de desenvolvimento (por exemplo, senha local do MySQL) no *Web.config* ou *appsettings.json,* mas segredos de produção (por exemplo, senha do banco de dados Azure MySQL) são seguros no App Service. O mesmo código usa suas configurações de desenvolvimento quando você depura localmente, e ele usa seus segredos de produção quando implantado no Azure.

Outras pilhas de idiomas, da mesma forma, obtêm as configurações do aplicativo como variáveis de ambiente em tempo de execução. Para obter etapas específicas de pilha de idiomas, consulte:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contêineres personalizados](containers/configure-custom-container.md#configure-environment-variables)

As configurações do aplicativo são sempre criptografadas quando armazenadas (criptografadas em repouso).

> [!NOTE]
> As configurações do aplicativo também podem ser resolvidas a partir do [Key Vault](/azure/key-vault/) usando [referências do Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para configurações do aplicativo estão escondidos no portal para segurança. Para ver um valor oculto de uma configuração de aplicativo, clique no **campo Valor** dessa configuração. Para ver os valores de todas as configurações do aplicativo, clique no botão **Mostrar valor.**

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova configuração de aplicativo, clique em **Nova configuração de aplicativo**. Na caixa de diálogo, você pode [colocar a configuração no slot atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma configuração, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Salvar** de volta na **página Configuração.**

> [!NOTE]
> Em um contêiner Linux padrão ou em um contêiner Linux personalizado, qualquer `ApplicationInsights:InstrumentationKey` estrutura de chave JSON `ApplicationInsights__InstrumentationKey` aninhada no nome de configuração do aplicativo precisa ser configurada no App Service quanto ao nome da chave. Em outras `:` palavras, qualquer `__` um deve ser substituído por (sublinhado duplo).
>

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar as configurações do aplicativo em massa, clique no botão **de edição Avançado.** Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Salvar** de volta na **página Configuração.**

As configurações do aplicativo têm a seguinte formatação JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Configurar cadeias de conexão

No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. No menu esquerdo do aplicativo, selecione **Configuração** > **Configuração Configuração configuração**.

![Configurações do aplicativo](./media/configure-common/open-ui.png)

Para desenvolvedores ASP.NET e ASP.NET Core, definir strings de `<connectionStrings>` conexão no App Service é como configurá-los na *Web.config,* mas os valores definidos no App Service sobrepõem os da *Web.config*. Você pode manter as configurações de desenvolvimento (por exemplo, um arquivo de banco de dados) em *Segredos de Web.config* e produção (por exemplo, credenciais de banco de dados SQL) com segurança no App Service. O mesmo código usa suas configurações de desenvolvimento quando você depura localmente, e ele usa seus segredos de produção quando implantado no Azure.

Para outras pilhas de idiomas, é melhor usar [as configurações](#configure-app-settings) do aplicativo, porque as seqüências de conexão exigem formatação especial nas teclas variáveis para acessar os valores. Aqui está uma exceção, no entanto: certos tipos de banco de dados do Azure são copiados junto com o aplicativo se você configurar suas strings de conexão em seu aplicativo. Para obter mais informações, consulte [O que recebe backup](manage-backup.md#what-gets-backed-up). Se você não precisar desse backup automatizado, use as configurações do aplicativo.

Em tempo de execução, as strings de conexão estão disponíveis como variáveis de ambiente, prefixadas com os seguintes tipos de conexão:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Banco de Dados SQL: `SQLAZURECONNSTR_`
* Personalizado: `CUSTOMCONNSTR_`

Por exemplo, uma seqüência de conexões MySql chamada `MYSQLCONNSTR_connectionString1` *connectionstring1* pode ser acessada como a variável de ambiente . Para obter etapas específicas de pilha de idiomas, consulte:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contêineres personalizados](containers/configure-custom-container.md#configure-environment-variables)

As cadeias de conexão são sempre criptografadas quando armazenadas (criptografadas em repouso).

> [!NOTE]
> As seqüências de conexão também podem ser resolvidas a partir do [Key Vault](/azure/key-vault/) usando [referências do Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para strings de conexão estão escondidos no portal para segurança. Para ver um valor oculto de uma seqüência de conexões, basta clicar no **campo Valor** dessa string. Para ver os valores de todas as seqüências de conexão, clique no botão **Mostrar valor.**

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova seqüência de conexões, clique em **Nova seqüência de conexões**. Na caixa de diálogo, você pode [colocar a seqüência de conexão no slot atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma configuração, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Salvar** de volta na **página Configuração.**

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar seqüências de conexão em massa, clique no botão **de edição Avançado.** Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Salvar** de volta na **página Configuração.**

As seqüências de conexão têm a seguinte formatação JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Definir as configurações gerais

No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. No menu esquerdo do aplicativo, selecione **Configuração** > **geral .**

![Configurações gerais](./media/configure-common/open-general.png)

Aqui, você pode configurar algumas configurações comuns para o aplicativo. Algumas configurações exigem que você [dimensione até níveis de preços mais altos](manage-scale-up.md).

- **Configurações de pilha**: A pilha de software para executar o aplicativo, incluindo as versões de idioma e SDK. Para aplicativos Linux e aplicativos de contêiner personalizados, você também pode definir um comando ou arquivo de inicialização opcional.
- **Configurações da plataforma**: Permite configurar configurações para a plataforma de hospedagem, incluindo:
    - **Bitness**: 32-bit ou 64-bit.
    - **Protocolo WebSocket**: Para [ASP.NET SignalR] ou [socket.io,](https://socket.io/)por exemplo.
    - **Sempre ligado**: Mantenha o aplicativo carregado mesmo quando não há tráfego. É necessário para WebJobs contínuos ou para WebJobs que são acionados usando uma expressão CRON.
      > [!NOTE]
      > Com o recurso Always On, você não pode controlar o ponto final. Ele sempre envia uma solicitação para a raiz do aplicativo.
    - **Versão gerenciada do pipeline**: O [modo de pipeline]IIS . Defina-o **como Classic** se você tiver um aplicativo legado que requer uma versão mais antiga do IIS.
    - **Versão HTTP**: Configurado como **2.0** para habilitar o suporte ao protocolo [HTTPS/2.](https://wikipedia.org/wiki/HTTP/2)
    > [!NOTE]
    > A maioria dos navegadores modernos dá suporte para protocolo HTTP/2 somente em TLS, enquanto o tráfego não criptografado continua usando HTTP / 1.1. Para garantir que os navegadores clientes se conectem ao seu aplicativo com HTTP/2, [proteja seu nome DNS personalizado com uma vinculação SSL no Azure App Service](configure-ssl-bindings.md).
    - **Afinidade ARR**: Em uma implantação em várias instâncias, certifique-se de que o cliente seja encaminhado para a mesma instância durante a vida útil da sessão. Você pode definir esta opção como **Off** para aplicativos apátridas.
- **Depuração**: Habilite a depuração remota para [aplicativos ASP.NET,](troubleshoot-dotnet-visual-studio.md#remotedebug) [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)ou [Node.js.](containers/configure-language-nodejs.md#debug-remotely) Esta opção desliga-se automaticamente após 48 horas.
- **Certificados de cliente de entrada**: requerem certificados de cliente em [autenticação mútua](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurar documentos padrão

Esta configuração é apenas para aplicativos windows.

No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. No menu esquerdo do aplicativo, selecione**Documentos padrão**de **configuração** > .

![Documentos padrão](./media/configure-common/open-documents.png)

O documento padrão é a página da Web exibida na URL raiz de um site. O primeiro arquivo correspondente na lista é usado. Para adicionar um novo documento padrão, clique em **Novo documento**. Não se esqueça de clicar em **Salvar**.

Se o aplicativo usa módulos que rotem com base em URL em vez de servir conteúdo estático, não há necessidade de documentos padrão.

## <a name="configure-path-mappings"></a>Configurar mapeamentos de caminhos

No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. No menu esquerdo do aplicativo, selecione**mapeamentos de caminho de** **configuração** > .

![Mapeamentos de caminhos](./media/configure-common/open-path.png)

A página **de mapeamentos Path** mostra coisas diferentes com base no tipo de Sistema Operacional.

### <a name="windows-apps-uncontainerized"></a>Aplicativos do Windows (não contêiner)

Para aplicativos windows, você pode personalizar os mapeamentos do manipulador IIS e aplicativos e diretórios virtuais.

Os mapeamentos do manipulador permitem adicionar processadores de script personalizados para lidar com solicitações de extensões de arquivo específicas. Para adicionar um manipulador personalizado, clique em **Novo manipulador**. Configure o manipulador da seguinte forma:

- **Extensão**. A extensão de arquivo que você deseja manusear, como * \*.php* ou *handler.fcgi*.
- **Processador de script**. O caminho absoluto do processador de script para você. As solicitações de arquivos que correspondem à extensão do arquivo são processadas pelo processador de script. Use o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz do seu aplicativo.
- **Argumentos**. Argumentos opcionais de linha de comando para o processador de script.

Cada aplicativo tem o`/`caminho raiz `D:\home\site\wwwroot`padrão () mapeado para, onde seu código é implantado por padrão. Se a raiz do aplicativo estiver em uma pasta diferente, ou se o seu repositório tiver mais de um aplicativo, você pode editar ou adicionar aplicativos e diretórios virtuais aqui. Clique em **Novo aplicativo ou diretório virtual**.

Para configurar aplicativos e diretórios virtuais, especifique cada diretório virtual e`D:\home`seu caminho físico correspondente em relação à raiz do site ( ). Opcionalmente, você pode marcar a caixa de seleção **Aplicativo** para marcar um diretório virtual como um aplicativo.

### <a name="containerized-apps"></a>Aplicativos containerizados

Você pode [adicionar armazenamento personalizado para o seu aplicativo contêiner.](containers/how-to-serve-content-from-azure-storage.md) Os aplicativos containerizados incluem todos os aplicativos Linux e também os contêineres personalizados do Windows e Linux em execução no App Service. Clique **em Novo suporte de armazenamento do Azure** e configure seu armazenamento personalizado da seguinte forma:

- **Nome**: O nome da exibição.
- **Opções de configuração**: **Básico** ou **Avançado**.
- **Contas de armazenamento**: A conta de armazenamento com o contêiner que você deseja.
- **Tipo de armazenamento** **: Azure Blobs** ou **Azure Files**.
  > [!NOTE]
  > Os aplicativos de contêiner do Windows só suportam arquivos Azure.
- **Recipiente de armazenamento**: Para configuração básica, o recipiente deseja.
- **Nome de compartilhamento**: Para configuração avançada, o nome do compartilhamento de arquivos.
- **Tecla de acesso**: Para configuração avançada, a chave de acesso.
- **Caminho de montagem**: O caminho absoluto no recipiente para montar o armazenamento personalizado.

Para obter mais informações, confira [Servir conteúdo do Armazenamento do Microsoft Azure no Serviço de Aplicativo no Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurar configurações de pilha de idiomas

Para aplicativos Linux, veja:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [Php](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurar recipientes personalizados

Consulte [Configurar um contêiner Linux personalizado para o Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Próximas etapas

- [Configure um nome de domínio personalizado no Azure App Service]
- [Configurar ambientes de preparo no Serviço de Aplicativo do Azure]
- [Proteger um nome DNS personalizado com uma associação SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
- [Habilitar logs de diagnóstico](troubleshoot-diagnostic-logs.md)
- [Dimensionar um aplicativo no Serviço de Aplicativo do Azure]
- [Conceitos básicos de monitoramento no Serviço de Aplicativo do Azure]
- [Alterar aplicativosConfiguraçãoHost.config com aplicativoHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Configure um nome de domínio personalizado no Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Configurar ambientes de preparo no Serviço de Aplicativo do Azure]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Conceitos básicos de monitoramento no Serviço de Aplicativo do Azure]: ./web-sites-monitor.md
[modo de pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar um aplicativo no Serviço de Aplicativo do Azure]: ./manage-scale-up.md
