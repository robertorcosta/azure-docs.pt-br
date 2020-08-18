---
title: 'Tutorial: Implantar um aplicativo Python Django com Postgres'
description: Crie um aplicativo Web Python com um banco de dados PostgreSQL e implante-o no Azure. O tutorial usa a estrutura Django e o aplicativo é hospedado no Serviço de Aplicativo do Azure no Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: d9d8694c8ac81352ab36f3d610f02f3751090b27
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120585"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Implantar um aplicativo Web Django com o PostgreSQL no Serviço de Aplicativo do Azure

Este tutorial mostra como implantar um aplicativo Web Python [Django](https://www.djangoproject.com/) controlado por dados no [Serviço de Aplicativo do Azure](overview.md) e conectá-lo a um Banco de Dados do Azure para PostgreSQL. O Serviço de Aplicativo fornece um serviço de hospedagem Web altamente escalonável e com aplicação automática de patch.

Neste tutorial, você usa a CLI do Azure para concluir as seguintes tarefas:

> [!div class="checklist"]
> * Configurar o ambiente inicial com Python e a CLI do Azure
> * Criar um Banco de Dados do Azure para PostgreSQL
> * Implantar código no Serviço de Aplicativo do Azure e conectar-se ao Postgres
> * Atualizar seu código e reimplantar
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo Web no portal do Azure

Use também a [versão do portal do Azure deste tutorial](/azure/developer/python/tutorial-python-postgresql-app-portal).


## <a name="set-up-your-initial-environment"></a>Configurar o seu ambiente inicial

1. Tenha uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Instale o <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 ou mais recente</a>.
1. Instale a <a href="/cli/azure/install-azure-cli" target="_blank">CLI do Azure</a> 2.0.80 ou superior, com a qual você executa comandos em qualquer shell para provisionar e configurar os recursos do Azure.

Abra uma janela de terminal e verifique se sua versão do Python é 3.6 ou superior:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Verifique se a sua versão da CLI do Azure é 2.0.80 ou superior:

```azurecli
az --version
```

Em seguida, entre no Azure por meio da CLI:

```azurecli
az login
```

Esse comando abre um navegador para coletar suas credenciais. Quando o comando for concluído, ele mostrará a saída JSON que contém informações sobre as suas assinaturas.

Depois de conectado, você poderá executar os comandos do Azure com a CLI do Azure para trabalhar com recursos na sua assinatura.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clone-or-download-the-sample-app"></a>Clonar ou baixar o aplicativo de exemplo

# <a name="git-clone"></a>[Clone do Git](#tab/clone)

Clone o repositório de exemplo:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Em seguida, acesse esta pasta:

```terminal
cd djangoapp
```

# <a name="download"></a>[Download](#tab/download)

Visite [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), selecione **Clonar** e escolha **Baixar ZIP**. 

Descompacte o arquivo ZIP em uma pasta chamada *djangoapp*. 

Em seguida, abra uma janela de terminal nessa pasta *djangoapp*.

---

O exemplo djangoapp contém o aplicativo de enquetes do Django controlado por dados que você obtém seguindo [Escrever seu primeiro aplicativo Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django. O aplicativo concluído é fornecido aqui para fins de conveniência.

O exemplo também é modificado para ser executado em um ambiente de produção, como o Serviço de Aplicativo:

- As configurações de produção estão no arquivo *azuresite/production.py*. Os detalhes de desenvolvimento estão em *azuresite/settings.py*.
- O aplicativo usa as configurações de produção quando a variável de ambiente `DJANGO_ENV` é definida como "produção". Você criará essa variável de ambiente posteriormente no tutorial junto com outras usadas para a configuração do banco de dados PostgreSQL.

Essas alterações são específicas para configurar o Django para execução em qualquer ambiente de produção, e não são específicas para o Serviço de Aplicativo. Para saber mais, confira a [Lista de verificação de implantação do Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="create-postgres-database-in-azure"></a>Criar um banco de dados Postgres no Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. -->

Instale a extensão `db-up` para a CLI do Azure:

```azurecli
az extension add --name db-up
```

Se o comando `az` não for reconhecido, verifique se você tem a CLI do Azure instalada, conforme descrito em [Configurar seu ambiente inicial](#set-up-your-initial-environment).

Em seguida, crie o banco de dados Postgres no Azure com o comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up):

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- Substitua *\<postgres-server-name>* por um nome exclusivo em todo o Azure (o ponto de extremidade do servidor é `https://\<postgres-server-name>.postgres.database.azure.com`). Um bom padrão é usar uma combinação do nome da empresa e outro valor exclusivo.
- Em *\<admin-username>* e *\<admin-password>* , especifique as credenciais para criar um usuário administrador para esse servidor Postgres.
- O [tipo de preço](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Básico, Geração 5, 1 núcleo) usado aqui é o mais barato. Para bancos de dados de produção, omita o argumento `--sku-name` para usar o nível GP_Gen5_2 (Uso Geral, Geração 5, 2 núcleos) em vez disso.

Esse comando executa as seguintes ações, que podem levar alguns minutos:

- Criar um [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) chamado `DjangoPostgres-tutorial-rg` se ele não existir.
- Criar um servidor Postgres.
- Crie uma conta de administrador padrão com um nome de usuário e senha exclusivos. (Para especificar suas credenciais, use os argumentos `--admin-user` e `--admin-password` com o comando `az postgres up`.)
- Criar um banco de dados `pollsdb`.
- Habilitar o acesso do endereço IP local.
- Habilitar o acesso dos serviços do Azure.
- Criando um usuário de banco de dados com acesso a um banco de dados `pollsdb`.

É possível executar todas as etapas separadamente com outros comandos `az postgres` e `psql`, mas `az postgres up` executa todas elas em conjunto.

Quando é concluído, o comando gera um objeto JSON que contém cadeias de conexão diferentes para o banco de dados, em conjunto com a URL do servidor, um nome de usuário gerado (como "joyfulKoala@msdocs-djangodb-12345") e uma senha de GUID. Copie o nome de usuário e a senha para um arquivo de texto temporário, pois você precisará deles mais tarde neste tutorial.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, pode ser definido como qualquer uma das [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). É possível obter as regiões disponíveis para sua assinatura com o comando [`az account list-locations`](/cli/azure/account#az-account-list-locations). Para aplicativos de produção, coloque seu banco de dados e seu aplicativo na mesma localização.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="deploy-the-code-to-azure-app-service"></a>Implantar o código no Serviço de Aplicativo do Azure

Nesta seção, você criará o host do aplicativo do Serviço de Aplicativo, conectará esse aplicativo ao banco de dados Postgres e implantará o código nesse host.

### <a name="create-the-app-service-app"></a>Criar o aplicativo do Serviço de Aplicativo

No terminal, verifique se você está na raiz do repositório (`djangoapp`) que contém o código do aplicativo.

Crie um aplicativo do Serviço de Aplicativo (o processo de host) com o comando [`az webapp up`](/cli/azure/webapp#az-webapp-up):

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Para o argumento `--location`, use a mesma localização usado para o banco de dados na seção anterior.
- Substitua *\<app-name>* por um nome exclusivo em todo o Azure (o ponto de extremidade do servidor é `https://\<app-name>.azurewebsites.net`). Os caracteres permitidos para *\<app-name>* são `A`-`Z`, `0`-`9` e `-`. Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.

Esse comando executa as seguintes ações, que podem levar alguns minutos:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Criar o [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) se ele ainda não existir. (Neste comando, você usa o mesmo grupo de recursos no qual você criou o banco de dados anteriormente.)
- Criar o [plano do Serviço de Aplicativo](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* no tipo de preço Básico (B1) se ele não existir. `--plan` e `--sku` são opcionais.
- Criar o aplicativo do Serviço de Aplicativo se ele não existir.
- Habilitar o log padrão do aplicativo, se ainda não estiver habilitado.
- Carregar o repositório usando a implantação ZIP com a automação do build habilitada.

Após a implantação bem-sucedida, o comando gera uma saída JSON como o seguinte exemplo:

![Exemplo de saída do comando az webapp up](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

> [!TIP]
> Muitos comandos da CLI do Azure armazenam em cache parâmetros comuns, como o nome do grupo de recursos e o plano do Serviço de Aplicativo, no arquivo *.azure/config*. Como resultado, você não precisa especificar todos os mesmos parâmetros com comandos posteriores. Por exemplo, para reimplantar o aplicativo depois de fazer alterações, você pode simplesmente executar `az webapp up` novamente sem parâmetros. No entanto, comandos que vêm de extensões da CLI, como `az postgres up`, não usam o cache no momento, por isso você precisou especificar o grupo de recursos e a localização aqui com `az webapp up`.

> [!NOTE]
> Se você tentar visitar a URL do aplicativo neste ponto, encontrará o erro "DisallowedHost em /". Esse erro ocorre porque você ainda não configurou o aplicativo para usar as configurações de produção abordadas anteriormente. Você fará isso na seção a seguir.

### <a name="configure-environment-variables-to-connect-the-database"></a>Configurar as variáveis de ambiente para conexão com o banco de dados

Com o código implantado no Serviço de Aplicativo, a próxima etapa é conectar o aplicativo ao banco de dados Postgres no Azure.

O código do aplicativo espera encontrar informações sobre o banco de dados em diversas variáveis de ambiente. Para definir as variáveis de ambiente no Serviço de Aplicativo, crie "configurações do aplicativo" usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Substitua *\<postgres-server-name>* pelo nome usado anteriormente com o comando `az postgres up`.
- Substitua *\<username>* e *\<password>* pelas credenciais que o comando também gerou para você.
- O grupo de recursos e o nome do aplicativo são extraídos dos valores armazenados em cache no arquivo *.azure/config*.
- O comando cria configurações chamadas `DJANGO_ENV`, `DBHOST`, `DBNAME`, `DBUSER` e `DBPASS`, conforme esperado pelo código do aplicativo.
- Em seu código Python, você acessa essas configurações como variáveis de ambiente com instruções como `os.environ.get('DJANGO_ENV')`. Para saber mais, confira [Acessar variáveis do ambiente](configure-language-python.md#access-environment-variables).

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="run-django-database-migrations"></a>Executar migrações de banco de dados do Django

As migrações de banco de dados do Django garantem que o esquema no PostgreSQL no banco de dados do Azure corresponda ao descrito em seu código.

1. Abra uma sessão SSH no navegador navegando até *https://\<app-name>.scm.azurewebsites.net/webssh/host* e entre com suas credenciais de conta do Azure (não com as credenciais do servidor de banco de dados).

1. Na sessão SSH, execute os seguintes comandos (você pode colar os comandos usando **CTRL**+**Shift**+**V**):

    ```bash
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. O comando `createsuperuser` solicita suas credenciais de superusuário. Para este tutorial, use o nome de usuário padrão `root`, pressione **Enter** para o endereço de email ser deixado em branco e digite `Pollsdb1` para a senha.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)
    
### <a name="create-a-poll-question-in-the-app"></a>Criar uma pergunta de enquete no aplicativo

1. Em um navegador, abra a URL *http:\//\<app-name>.azurewebsites.net*. O aplicativo deve exibir a mensagem "Não há enquetes disponíveis" porque ainda não há enquetes específicas no banco de dados.

1. Navegue até *http:\//\<app-name>.azurewebsites.net/admin*. Entre usando as credenciais de superusuário da seção anterior (`root` e `Pollsdb1`). Selecione **Enquetes**, selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

1. Navegue novamente para *http:\//\<app-name>.azurewebsites.net/* para confirmar que as perguntas agora são apresentadas ao usuário. Responda às perguntas como desejar para gerar dados no banco de dados.

**Parabéns!** Você está executando um aplicativo Web Django, escrito em Python, no Serviço de Aplicativo do Azure para Linux, com um banco de dados Postgres ativo.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> O Serviço de Aplicativo detecta um projeto Django procurando um arquivo *wsgi.py* em cada subpasta, que o `manage.py startproject` cria por padrão. Quando o Serviço de Aplicativo encontra o arquivo, ele carrega o aplicativo Web Django. Para obter mais informações, confira [Configurar imagem interna do Python](configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Fazer alterações no código e reimplantar

Nesta seção, você faz alterações locais no aplicativo e reimplanta o código no Serviço de Aplicativo. No processo, você configura um ambiente virtual do Python que dá suporte ao trabalho em andamento.

### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Em uma janela de terminal, execute os comandos a seguir. Siga os prompts ao criar o superusuário:

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

```cmd
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

Após o aplicativo Web ser totalmente carregado, o servidor de desenvolvimento do Django fornecerá a URL do aplicativo local na mensagem "Iniciando o servidor de desenvolvimento em http://127.0.0.1:8000/. Feche o servidor com CTRL-BREAK ".

![Exemplo de saída do servidor de desenvolvimento Django](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Teste o aplicativo localmente com as seguintes etapas:

1. Navegue para *http:\//localhost:8000* em um navegador, o que deverá exibir a mensagem "Não há enquetes disponíveis". 

1. Navegue até *http:\//localhost:8000/admin* e entre usando o usuário administrador que você criou anteriormente. Em **Enquetes**, selecione **Adicionar** novamente ao lado de **Perguntas** e crie uma enquete com algumas opções. 

1. Vá para *http:\//localhost:8000* novamente e responda à pergunta para testar o aplicativo. 

1. Pare o servidor Django pressionando **CTRL**+**C**.

Ao ser executado localmente, o aplicativo está usando um banco de dados SQLite3 local e não interfere no banco de dados de produção. Você também poderá usar um banco de dados PostgreSQL local, se desejar, para simular melhor seu ambiente de produção.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="update-the-app"></a>Atualizar o aplicativo

Em `polls/models.py`, localize a linha que começa com `choice_text` e altere o parâmetro `max_length` para 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Como você alterou o modelo de dados, crie uma migração do Django e migre o banco de dados:

```
python manage.py makemigrations
python manage.py migrate
```

Execute o servidor de desenvolvimento novamente com `python manage.py runserver` e teste o aplicativo em *http:\//localhost:8000/admin*:

Pare o servidor Web Django novamente com **CTRL**+**C**.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="redeploy-the-code-to-azure"></a>Reimplantar o código no Azure

Execute o seguinte comando na raiz do repositório:

```azurecli
az webapp up
```

Esse comando usa os parâmetros armazenados em cache no arquivo *.azure/config*. Como o Serviço de Aplicativo detecta que o aplicativo já existe, ele apenas reimplanta o código.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="rerun-migrations-in-azure"></a>Executar migrações novamente no Azure

Como você fez alterações no modelo de dados, é necessário executar novamente as migrações de banco de dados no Serviço de Aplicativo.

Abra uma sessão SSH novamente no navegador acessando *https://\<app-name>.scm.azurewebsites.net/webssh/host*. Em seguida, execute os comandos a seguir:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="review-app-in-production"></a>Examinar o aplicativo na produção

Navegue até *http:\//\<app-name>.azurewebsites.net* e teste o aplicativo novamente em produção. (Como você alterou apenas o comprimento de um campo do banco de dados, a alteração só será perceptível se você tentar inserir uma resposta mais longa ao criar uma pergunta.)

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

Você pode acessar os logs do console gerados de dentro do contêiner que hospeda o aplicativo no Azure.

Execute o comando a seguir da CLI do Azure para ver o fluxo de logs. Esse comando usa parâmetros armazenados em cache no arquivo *.azure/config*.

```azurecli
az webapp log tail
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

Para interromper o streaming de log a qualquer momento, digite **Ctrl**+**C**.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` ativa o log padrão para você. Por motivos de desempenho, esse log se desativa depois de algum tempo, mas volta é ativado novamente cada vez que você executa `az webapp up`. Para ativá-lo manualmente, execute o seguinte comando:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

No [portal do Azure](https://portal.azure.com), pesquise pelo nome do aplicativo e selecione-o nos resultados.

![Navegue até o aplicativo Python Django no portal do Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página de **Visão geral** do aplicativo, que fornece uma exibição do desempenho geral. Aqui você também pode executar tarefas básicas de gerenciamento como procurar, parar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Gerencie seu aplicativo Python Django na página Visão geral no portal do Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser manter o aplicativo ou prosseguir para o próximo tutorial, pule para as [Próximas etapas](#next-steps). Caso contrário, para evitar incorrer em encargos contínuos, você pode excluir o grupo de recursos criado para este tutorial:

```azurecli
az group delete
```

O comando usa o nome do grupo de recursos armazenado em cache no arquivo *.azure/config*. Ao excluir o grupo de recursos, você também desaloca e exclui todos os recursos contidos nele.

[Está com problemas? Fale conosco.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>Próximas etapas

Saiba como mapear um nome DNS personalizado para seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md)

Saiba como o Serviço de Aplicativo executa um aplicativo Python:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](configure-language-python.md)
