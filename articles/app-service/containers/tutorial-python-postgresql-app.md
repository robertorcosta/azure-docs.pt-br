---
title: 'Tutorial: Implantar o Python (Django) com o Postgres'
description: Saiba como criar um aplicativo Python com um banco de dados PostgreSQL e implantá-lo no Serviço de Aplicativo do Azure no Linux. O tutorial usa um aplicativo de exemplo Django para demonstração.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085749"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Implantar um aplicativo Web Python (Django) com o PostgreSQL no Serviço de Aplicativo do Azure

Este tutorial mostra como implantar um aplicativo Web Python (Django) controlado por dados no [Serviço de Aplicativo do Azure](app-service-linux-intro.md) e conectá-lo a um Banco de Dados do Azure para PostgreSQL. O Serviço de Aplicativo fornece um serviço de hospedagem Web altamente escalonável e com aplicação automática de patch.

![Implantar um aplicativo Web Django do Python no Serviço de Aplicativo do Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para PostgreSQL
> * Implantar código no Serviço de Aplicativo do Azure e conectar-se ao Postgres
> * Atualizar seu código e reimplantar
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo Web no portal do Azure

Você pode seguir as etapas deste artigo no macOS, no Linux ou no Windows.

## <a name="install-dependencies"></a>Instalar dependências

Antes de começar este tutorial:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Instale a [CLI do Azure](/cli/azure/install-azure-cli).
- Instale o [Git](https://git-scm.com/).
- Instale o [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Em uma janela de terminal, execute os seguintes comandos para clonar o repositório do aplicativo de exemplo e altere para a raiz do repositório:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

O repositório de exemplo djangoapp contém o aplicativo de enquetes do [Django](https://www.djangoproject.com/) controlado por dados que você obtém seguindo [Escrever seu primeiro aplicativo Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django. Ele é fornecido aqui para facilitar.

## <a name="prepare-app-for-app-service"></a>Preparar o aplicativo para o Serviço de Aplicativo

Como muitas estruturas Web do Python, o Django [requer determinadas alterações antes que possam ser executadas em um servidor de produção](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) e não é diferente do Serviço de Aplicativo. É necessário alterar e adicionar algumas configurações no arquivo padrão *azuresite/settings.py* para que o aplicativo funcione depois de ser implantado no Serviço de Aplicativo. 

Dê uma olhada em *azuresite/production.py*, que realiza a configuração necessária para o Serviço de Aplicativo. Resumindo, ele faz o seguinte:

- Herda todas as configurações de *azuresite/settings.py*.
- Adiciona o nome de domínio totalmente qualificado do aplicativo do Serviço de Aplicativo aos hosts permitidos. 
- Usa [WhiteNoise](https://whitenoise.evans.io/en/stable/) para habilitar o fornecimento de arquivos estáticos em produção, pois o Django, por padrão, não fornece arquivos estáticos em produção. O pacote WhiteNoise já está incluído em *requirements.txt*.
- Adiciona configuração para o banco de dados PostgreSQL. Por padrão, o Django usa o Sqlite3 como o banco de dados, mas ele não é adequado para aplicativos de produção. O pacote [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) já está incluído em *requirements.txt*.
- A configuração do Postgres usa variáveis de ambiente. Posteriormente, você descobrirá como definir variáveis de ambiente no Serviço de Aplicativo.

*azuresite/production.py* está incluído no repositório para facilitar, mas ainda não é usado pelo aplicativo. Para verificar se as configurações dele são usadas no Serviço de Aplicativo, é necessário configurar dois arquivos, *manage.py* e *azuresite/wsgi.py*, para acessá-lo.

- Em *manage.py*, altere a seguinte linha:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Para o seguinte código:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Você definirá a variável de ambiente `DJANGO_ENV` posteriormente quando configurar seu aplicativo do Serviço de Aplicativo.

- No *azuresite/wsgi.py*, faça a mesma alteração acima.

    No Serviço de Aplicativo, você usa *manage.py* para executar migrações de banco de dados e o Serviço de Aplicativo usa *azuresite/wsgi.py* para executar seu aplicativo Django em produção. Com essa alteração em ambos os arquivos, as configurações de produção são usadas em ambos os casos.

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

A CLI do Azure já deverá estar instalada. A [CLI do Azure](/cli/azure/what-is-azure-cli) permite que você trabalhe com os recursos do Azure no terminal de linha de comando. 

Para entrar no Azure, execute o comando [`az login`](/cli/azure/reference-index#az-login):

```azurecli
az login
```

Siga as instruções no terminal para entrar em sua conta do Azure. Quando tiver terminado, suas assinaturas serão mostradas no formato JSON na saída do terminal.

## <a name="create-postgres-database-in-azure"></a>Criar um banco de dados Postgres no Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Nesta seção, você criará um servidor e um Banco de Dados do Azure para PostgreSQL. Para começar, instale a extensão `db-up` com o seguinte comando:

```azurecli
az extension add --name db-up
```

Crie o banco de dados Postgres no Azure com o comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), conforme mostrado no exemplo a seguir. Substitua *\<nome-do-postgresql>* por um nome *exclusivo* (o ponto de extremidade do servidor é *https://\<nome-do-postgresql>.postgres.database.azure.com*). Para *\<nome-do-usuário-admin>* e *\<senha-do-admin>* , especifique as credenciais para criar um usuário administrador para esse servidor do Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Esse comando pode levar algum tempo porque está fazendo o seguinte:

- Criando um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) chamado `myResourceGroup` se ele não existir. Todo recurso do Azure precisa estar em um deles. `--resource-group` é opcional.
- Criando um servidor Postgres com o usuário administrativo.
- Criando um banco de dados `pollsdb`.
- Permitindo o acesso do seu endereço IP local.
- Permitindo o acesso dos serviços do Azure.
- Criando um usuário de banco de dados com acesso a um banco de dados `pollsdb`.

É possível executar todas as etapas separadamente com outros comandos `az postgres` e com `psql`, mas `az postgres up` executa todas elas em uma etapa para você.

Quando o comando for concluído, localize as linhas de saída com `Ran Database Query:`. Elas mostram o usuário do banco de dados criado para você, com o nome de usuário `root` e a senha `Pollsdb1`. Você os usará posteriormente para conectar seu aplicativo ao banco de dados.

<!-- not all locations support az postgres up -->
> [!TIP]
> Para especificar a localização do seu servidor Postgres, inclua o argumento `--location <location-name>`, em que `<location_name>` é uma das [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). É possível obter as regiões disponíveis para sua assinatura com o comando [`az account list-locations`](/cli/azure/account#az-account-list-locations).

## <a name="deploy-the-app-service-app"></a>Implantar o aplicativo do Serviço de Aplicativo

Nesta seção, você criará o aplicativo do Serviço de Aplicativo. Você conectará esse aplicativo ao banco de dados Postgres criado e implantará seu código.

### <a name="create-the-app-service-app"></a>Criar o aplicativo do Serviço de Aplicativo

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Verifique se você está de volta na raiz do repositório (`djangoapp`), porque o aplicativo será implantado com base nesse diretório.

Crie um aplicativo do Serviço de Aplicativo com o comando [`az webapp up`](/cli/azure/webapp#az-webapp-up), conforme mostrado no exemplo a seguir. Substitua *\<nome-do-aplicativo>* por um nome *exclusivo* (o ponto de extremidade do servidor é *https://\<nome-do-aplicativo>.azurewebsites.net*). Os caracteres permitidos para *\<nome-do-aplicativo>* são `A`-`Z`, `0`-`9` e `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Esse comando pode levar algum tempo porque está fazendo o seguinte:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Gerando um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) automaticamente.
- Criando o [plano do Serviço de Aplicativo](../overview-hosting-plans.md) *myAppServicePlan* no tipo de preço Básico (B1), se ele não existir. `--plan` e `--sku` são opcionais.
- Criando o aplicativo do Serviço de Aplicativo se ele não existir.
- Habilitando o log padrão do aplicativo, se ainda não está habilitado.
- Carregando o repositório usando a implantação ZIP com a automação de build habilitada.

Após a conclusão da implantação, você verá uma saída JSON semelhante à seguinte:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Copie o valor de *\<app-resource-group>* . Ele será necessário para configurar o aplicativo posteriormente. 

> [!TIP]
> Você pode usar o mesmo comando posteriormente para implantar alterações e habilitar imediatamente os logs de diagnóstico com:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

O código de exemplo agora foi implantado, mas o aplicativo ainda não se conectará ao banco de dados Postgres no Azure. Você fará isso em seguida.

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Ao executar o aplicativo localmente, você pode definir as variáveis de ambiente na sessão de terminal. No Serviço de Aplicativo, faça isso com as *configurações do aplicativo* usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Execute o comando a seguir para especificar os detalhes de conexão de banco de dados como configurações do aplicativo. Substitua *\<nome-do-aplicativo>* , *\<grupo-de-recursos-do-aplicativo>* e *\<nome-do-postgresql>* pelos seus valores. Lembre-se de que as credenciais de usuário `root` e `Pollsdb1` foram criadas para você por `az postgres up`.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Para obter informações sobre como seu código acessa essas configurações de aplicativo, confira [Acessar as variáveis de ambiente](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Executar migrações de banco de dados

Para executar migrações de banco de dados no Serviço de Aplicativo, abra uma sessão SSH no navegador acessando *https://\<nome-do-aplicativo>.scm.azurewebsites.net/webssh/host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Na sessão SSH, execute os seguintes comandos:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até o aplicativo implantado com a URL *http:\//\<nome-do-aplicativo>.azurewebsites.net* em um navegador. Você deverá ver a mensagem **Não há enquetes disponíveis**. 

Navegue até *http:\//\<nome-do-aplicativo>.azurewebsites.net/admin* e entre usando o usuário administrador que você criou na última etapa. Selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

Navegue até o aplicativo implantado com a URL *http:\//\<nome-do-aplicativo>.azurewebsites.net/admin* e crie algumas perguntas de enquete. É possível ver as perguntas em *http:\//\<nome-do-aplicativo>.azurewebsites.net/* . 

![Execute o aplicativo Python Django nos Serviços de Aplicativos no Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Navegue até o aplicativo implantado com a URL *http:\//\<nome-do-aplicativo>.azurewebsites.net* novamente para ver a pergunta de enquete e responde-la.

O Serviço de Aplicativo detecta um projeto do Django em seu repositório procurando por um arquivo *wsgi.py* em cada subdiretório, que é criado por `manage.py startproject` por padrão. Ao encontrar o arquivo, o Serviço de Aplicativo carrega o aplicativo Web Django. Para obter mais informações sobre como o Serviço de Aplicativo carrega os aplicativos Python, confira [Configurar a imagem interna do Python](how-to-configure-python.md).

**Parabéns!** Você está executando um aplicativo Web Python (Django) no Serviço de Aplicativo do Azure para Linux.

## <a name="develop-app-locally-and-redeploy"></a>Desenvolver o aplicativo localmente e reimplantar

Nesta seção, você desenvolverá seu aplicativo em seu ambiente local e reimplantará seu código no Serviço de Aplicativo.

### <a name="set-up-locally-and-run"></a>Configurar localmente e executar

Para configurar seu ambiente de desenvolvimento local e executar o aplicativo de exemplo pela primeira vez, execute os seguintes comandos:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Quando o aplicativo Web Django estiver totalmente carregado, ele retornará algo semelhante à seguinte mensagem:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Navegue para *http:\//localhost:8000* em um navegador. Você deverá ver a mensagem **Não há enquetes disponíveis**. 

Navegue até *http:\//localhost:8000/admin* e entre usando o usuário administrador que você criou na etapa anterior. Selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

![Execute o aplicativo Python Django nos Serviços de Aplicativos localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá para *http:\//localhost: 8000* novamente para ver a pergunta da enquete e respondê-la. O aplicativo de exemplo Django local grava e armazena dados de usuário em um banco de dados Sqlite3 local para que você não precise se preocupar com a bagunça de seu banco de dados de produção. Para fazer seu ambiente de desenvolvimento corresponder ao ambiente do Azure, considere usar um banco de dados Postgres localmente.

Para parar o servidor o Django, digite Ctrl+C.

### <a name="update-the-app"></a>Atualizar o aplicativo

Apenas para ver como a realização de atualizações de aplicativo funciona, faça uma pequena alteração no `polls/models.py`. Localize a linha:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

Altere-o para:

```python
choice_text = models.CharField(max_length=100)
```

Alterando o modelo de dados, é necessário criar uma migração do Django. Faça isso com o seguinte comando:

```
python manage.py makemigrations
```

É possível testar suas alterações localmente executando migrações, executando o servidor de desenvolvimento e acessando *http:\//localhost:8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Reimplantar o código no Azure

Para reimplantar as alterações, execute o seguinte comando na raiz do repositório:

```azurecli
az webapp up --name <app-name>
```

O Serviço de Aplicativo detecta que o aplicativo existe e apenas implanta o código.

### <a name="rerun-migrations-in-azure"></a>Executar migrações novamente no Azure

Como você fez alterações no modelo de dados, é necessário executar novamente as migrações de banco de dados no Serviço de Aplicativo. Abra uma sessão SSH no navegador acessando *https://\<nome-do-aplicativo>.scm.azurewebsites.net/webssh/host*. Execute os seguintes comandos:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Examinar o aplicativo na produção

Acesse *http:\//\<nome-do-aplicativo>.azurewebsites.net* e veja as alterações sendo executadas em tempo real em produção. 

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

Você pode acessar os logs do console gerados de dentro do contêiner.

> [!TIP]
> `az webapp up` ativa o log padrão para você. Por motivos de desempenho, esse log se desativa depois de algum tempo, mas volta é ativado novamente cada vez que você executa `az webapp up`. Para ativá-lo manualmente, execute o seguinte comando:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Execute o seguinte comando da CLI do Azure para ver o fluxo de logs:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

> [!NOTE]
> Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.

## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

No [portal do Azure](https://portal.azure.com), procure o aplicativo que você criou e selecione-o.

![Navegue até o aplicativo Python Django no portal do Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui você também pode executar tarefas básicas de gerenciamento como procurar, parar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Gerencie seu aplicativo Python Django na página Visão geral no portal do Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você achar que não precisará desses recursos no futuro, exclua os grupos de recursos executando os seguintes comandos:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para PostgreSQL
> * Implantar código no Serviço de Aplicativo do Azure e conectar-se ao Postgres
> * Atualizar seu código e reimplantar
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo Web no portal do Azure

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)
