---
title: 'Tutorial: Aplicativo Python do Linux com o Postgres'
description: Saiba como executar um aplicativo Python do Linux no Serviço de Aplicativo do Azure, com uma conexão com um banco de dados PostgreSQL no Azure. O Django é usado neste tutorial.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: 3aa5b5085a6120ca513f0aeba344e7f541f0fd72
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713416"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Executar um aplicativo Web Python (Django) com PostgreSQL no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como conectar um Aplicativo Web Django do Python controlado por dados a um Banco de Dados do Azure para PostgreSQL e implantar e executar o aplicativo no Serviço de Aplicativo do Azure.

![Aplicativo Web Django do Python no Serviço de Aplicativo do Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Banco de Dados do Azure para PostgreSQL e conectar um aplicativo Web a ele
> * Implantar o aplicativo Web no Serviço de Aplicativo do Azure
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo Web no portal do Azure

Você pode seguir as etapas deste artigo no macOS, no Linux ou no Windows. As etapas são semelhantes na maioria dos casos, embora as diferenças não sejam detalhadas neste tutorial. A maioria dos exemplos abaixo usa uma janela de terminal `bash` no Linux. 

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Instale o [Git](https://git-scm.com/).
- Instale o [Python 3](https://www.python.org/downloads/).
- Instale e execute o [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL e criar um banco de dados

Primeiro, conecte-se ao seu servidor PostgreSQL local e crie um banco de dados: 

Em uma janela do terminal local, execute `psql` para se conectar ao servidor PostgreSQL local como o usuário `postgres` interno.

```bash
sudo su - postgres
psql
```
ou
```PowerShell
psql -U postgres
```

Se sua conexão tiver êxito, o banco de dados PostgreSQL já estará em execução. Se não, verifique se o banco de dados PostgresQL local foi iniciado seguindo as instruções para seu sistema operacional em [Downloads – Distribuição Principal do PostgreSQL](https://www.postgresql.org/download/).

Crie um banco de dados chamado *pollsdb* e configure um usuário de banco de dados chamado *manager* com a senha *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Criar e executar o aplicativo local do Python

Em seguida, configure e execute o aplicativo Web Django do Python de exemplo.

O repositório de exemplo [djangoapp](https://github.com/Azure-Samples/djangoapp) contém o aplicativo de enquetes do [Django](https://www.djangoproject.com/) controlado por dados que você obtém ao seguir as instruções em [Escrever o seu primeiro aplicativo Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django.

### <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Em uma janela de terminal, execute os comandos a seguir para clonar o repositório do aplicativo de exemplo e altere para o novo diretório de trabalho:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Configurar o ambiente virtual do Python

Crie e ative um ambiente virtual do Python para executar seu aplicativo.

```bash
python3 -m venv venv
source venv/bin/activate
```
ou
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

No ambiente `venv`, execute *env.sh* ou *env.ps1* para definir as variáveis de ambiente que *azuresite/settings.py* usará para as configurações de conexão de banco de dados.

```bash
source ./env.sh
```
ou
```PowerShell
.\env.ps1
```

Instale os pacotes necessários de *requirements.txt*, execute [migrações do Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) e [crie um usuário administrador](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Executar o aplicativo Web

Depois que você criar o usuário administrador, execute o servidor do Django.

```bash
python manage.py runserver
```

Quando o aplicativo Web Django estiver totalmente carregado, ele retornará algo semelhante à seguinte mensagem:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Navegue para *http:\//localhost:8000* em um navegador. Você deverá ver a mensagem **Não há enquetes disponíveis**. 

Navegue até *http:\//localhost:8000/admin* e entre usando o usuário administrador que você criou na etapa anterior. Selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

![Execute o aplicativo Python Django nos Serviços de Aplicativos localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá para *http:\//localhost: 8000* novamente para ver a pergunta da enquete e respondê-la. O aplicativo de exemplo local do Django grava e armazena dados de usuário no banco de dados PostgreSQL local.

Para parar o servidor o Django, digite Ctrl+C no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A maioria das etapas restantes neste artigo usa comandos da CLI do Azure no Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Criar e conectar-se ao Banco de Dados do Azure para PostgreSQL

Nesta seção, você cria um servidor e um banco de dados do Banco de Dados do Azure para PostgreSQL e conecta seu aplicativo Web a ele. Quando você implanta seu aplicativo Web no Serviço de Aplicativo do Azure, o aplicativo usa esse banco de dados de nuvem. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Você pode criar um grupo de recursos para seu servidor do Banco de Dados do Azure para PostgreSQL ou usar um grupo de recursos existente. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Você cria um servidor PostgreSQL com o comando [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

> [!NOTE]
> Antes de criar um servidor de Banco de Dados do Azure para PostgreSQL, verifique qual [geração da computação](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) está disponível em sua região. Se sua região não for compatível com hardware Gen4, altere *--sku-name* na linha de comando a seguir para um valor compatível com sua região, como Gen5. 

No comando a seguir, substitua *\<postgresql-name>* por um nome do servidor exclusivo. Esse nome do servidor é parte de seu ponto de extremidade do PostgreSQL (*https://\<postgresql-name>.postgres.database.azure.com*), portanto, ele precisa ser exclusivo entre todos os servidores no Azure. 

Substitua *\<resourcegroup-name>* e *\<region>* pelo nome e pela região do grupo de recursos que você deseja usar. Para *\<admin-username>* e *\<admin-password>* , crie credenciais de usuário para a conta de administrador do banco de dados. Lembre-se do *\<admin-username>* e da *\<admin-password>* para usá-los posteriormente para entrar no servidor e nos bancos de dados PostgreSQL.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Quando o servidor do Banco de Dados do Azure para PostgreSQL for criado, a CLI do Azure retornará código JSON semelhante ao do exemplo a seguir:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Criar regras de firewall para o servidor do Banco de Dados do Azure para PostgreSQL

Execute o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) para permitir o acesso de recursos do Azure ao banco de dados. Substitua os espaços reservados *\<postgresql-name>* e *\<resourcegroup-name>* pelos seus valores.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> A configuração mostrada acima permite conexões de rede de todos os endereços IP na rede do Azure. Para uso em produção, tente configurar as regras de firewall mais restritivas possíveis [permitindo somente os endereços IP de saída que seu aplicativo usa](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Execute o comando `firewall-rule create` novamente para permitir o acesso do seu computador local. Substitua *\<your-ip-address>* pelo [seu endereço IP IPv4 local](https://www.whatsmyip.org/). Substitua os espaços reservados *\<postgresql-name>* e *\<resourcegroup-name>* pelos seus próprios valores.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Criar e conectar-se ao Banco de Dados do Azure para PostgreSQL

Conecte-se ao servidor do Banco de Dados do Azure para PostgreSQL executando o comando a seguir. Use seus próprios *\<postgresql-name>* e *\<admin-username>* e entre com a senha que você criou.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Assim como você fez no servidor PostgreSQL local, crie um banco de dados e um usuário no servidor do Banco de Dados do Azure para PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> É uma melhor prática criar usuários de banco de dados com permissões restritas para aplicativos específicos, em vez de usar o usuário administrador. O usuário `manager` tem privilégios totais *apenas* para o banco de dados `pollsdb`.

Digite `\q` para sair do cliente do PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testar a conectividade do aplicativo com o Banco de Dados do Azure para PostgreSQL

Edite o arquivo local *env.sh* ou *env.ps1* para apontar para o banco de dados de nuvem para PostgreSQL, substituindo *\<postgresql-name>* pelo nome do servidor do Banco de Dados do Azure para PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
ou
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

No ambiente `venv` na janela do seu terminal local, execute o *env.sh* ou *env.ps1* editado. 
```bash
source ./env.sh
```
ou
```PowerShell
.\env.ps1
```

Execute a migração do Django para o banco de dados do Azure e crie um usuário administrador.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Depois que o usuário administrador for criado, execute o servidor do Django.

```bash
python manage.py runserver
```

Em um navegador, navegue para *http:\//localhost:8000* e você deverá ver novamente a mensagem **Não há enquetes disponíveis**. 

Navegue até *http:\//localhost:8000/admin*, entre usando o usuário administrador que você criou e crie uma enquete, assim como foi feito antes.

![Execute o aplicativo Python Django nos Serviços de Aplicativos localmente](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Vá até *http:\//localhost:8000/admin* novamente e veja a pergunta da enquete ser exibida. Agora, seu aplicativo está gravando dados no Banco de Dados do Azure para PostgreSQL.

Para parar o servidor o Django, digite Ctrl+C no terminal.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Implantar o aplicativo Web no Serviço de Aplicativo do Azure

Nesta etapa, o aplicativo Python conectado ao Banco de Dados do Azure para PostgreSQL é conectado por você ao Serviço de Aplicativo do Azure.

### <a name="configure-repository"></a>Configurar repositório

Já que este tutorial usa um exemplo de Django, você precisa alterar e adicionar algumas configurações em seu arquivo *djangoapp/azuresite/settings.py* arquivo para trabalhar com o Serviço de Aplicativo do Azure. 

1. O Django valida o cabeçalho `HTTP_HOST` em solicitações de entrada. Para seu aplicativo Web Django funcionar no Serviço de Aplicativo, você precisará adicionar o nome de domínio totalmente qualificado do aplicativo para os hosts permitidos. 
   
   Edite *azuresite/settings.py* para alterar a linha `ALLOWED_HOSTS` da seguinte maneira:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. O Django não dá suporte à [disponibilização de arquivos estáticos em produção](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). Para este tutorial, você usa o [WhiteNoise](https://whitenoise.evans.io/en/stable/) para habilitar o fornecimento dos arquivos. O pacote WhiteNoise já estava instalado com o arquivo *requirements.txt*. 
   
   Para configurar o Django para usar o WhiteNoise, em *azuresite/settings.py*, localize a configuração `MIDDLEWARE` e adicione `whitenoise.middleware.WhiteNoiseMiddleware` à lista, logo após a linha `django.middleware.security.SecurityMiddleware`. Sua configuração `MIDDLEWARE` deve ter esta aparência:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Ao final do *azuresite/settings.py*, adicione as seguintes linhas:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Para obter mais informações sobre como configurar o WhiteNoise, confira a [documentação do WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> A seção de configurações de banco de dados já segue a melhor prática de segurança do uso de variáveis de ambiente. Para as recomendações de implantação completas, confira [Documentação do Django: lista de verificação de implantação](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Confirme suas alterações em seu fork do repositório do *djangoapp*:

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Criar plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

No início do tutorial, você definiu as variáveis de ambiente para se conectar a seu banco de dados PostgreSQL.

No Serviço de Aplicativo do Azure, defina as variáveis de ambiente como *configurações do aplicativo* usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

No Azure Cloud Shell, execute o comando a seguir para especificar os detalhes de conexão de banco de dados como configurações do aplicativo. Substitua *\<app-name>* , *\<resourcegroup-name>* e *\<postgresql-name>* pelos seus próprios valores.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Para obter informações sobre como seu código acessa essas configurações de aplicativo, confira [Acessar as variáveis de ambiente](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

O servidor de implantação do Serviço de Aplicativo vê o *requirements.txt* na raiz do repositório e executa o gerenciamento de pacotes do Python automaticamente após `git push`.

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até o aplicativo implantado com a URL *http:\//\<nome-do-aplicativo>.azurewebsites.net*. Ele leva algum tempo para ser iniciado porque o contêiner tem que ser baixado e executado quando o aplicativo é solicitado pela primeira vez. Se a página expirar ou exibir uma mensagem de erro, aguarde alguns minutos e atualize-a.

Você deve ver as perguntas da enquete que criou anteriormente. 

O Serviço de Aplicativo detecta um projeto do Django em seu repositório procurando por um arquivo *wsgi.py* em cada subdiretório, que é criado por `manage.py startproject` por padrão. Ao encontrar o arquivo, o Serviço de Aplicativo carrega o aplicativo Web Django. Para obter mais informações sobre como o Serviço de Aplicativo carrega os aplicativos Python, confira [Configurar a imagem interna do Python](how-to-configure-python.md).

Navegue até *http:\//\<nome-do-aplicativo>.azurewebsites.net/admin* e entre usando o usuário administrador que você criou. Se quiser, crie mais algumas perguntas de enquete.

![Execute o aplicativo Python Django nos Serviços de Aplicativos no Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Parabéns!** Você está executando um aplicativo Web Python (Django) no Serviço de Aplicativo do Azure para Linux.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

No [portal do Azure](https://portal.azure.com), procure o aplicativo que você criou e selecione-o.

![Navegue até o aplicativo Python Django no portal do Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui você também pode executar tarefas básicas de gerenciamento como procurar, parar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Gerencie seu aplicativo Python Django na página Visão geral no portal do Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)
