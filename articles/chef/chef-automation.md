---
title: Início Rápido – Configurar uma máquina virtual do Windows no Azure usando o Chef
description: Neste início rápido, você aprenderá a usar a o Chef para implantar e configurar uma máquina virtual do Windows no Azure
keywords: chef, azure, devops, máquina virtual
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77589490"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Início Rápido – Configurar uma máquina virtual do Windows no Azure usando o Chef

O Chef permite que você forneça automação e configurações de estado desejado.

Com a última versão da API de nuvem, o Chef fornece integração perfeita com o Azure, dando a você a capacidade de provisionar e implantar os estados de configuração por meio de um único comando.

Neste artigo, você configurará o ambiente do Chef para provisionar máquinas virtuais do Azure e percorrerá as etapas de criação de uma política ou um guia e de implantação desse guia em uma máquina virtual do Azure.

## <a name="chef-basics"></a>Noções básicas do Chef

Antes de começar este artigo, [leia os conceitos básicos do Chef](https://www.chef.io/chef).

O diagrama a seguir mostra a arquitetura de alto nível do Chef.

![Arquitetura do Chef](media/chef-automation/chef-architecure.png)

O Chef tem três componentes de arquitetura principais: 
- Chef Server – o ponto de gerenciamento e há duas opções para o Chef Server: uma solução hospedada ou uma solução local.
- Chef Client (nó) – o agente que reside nos servidores que você está gerenciando.
- Chef Workstation – o nome tanto da estação de trabalho do administrador (em que você cria políticas e executa comandos de gerenciamento) quanto do pacote de software das ferramentas do Chef.

Em geral, você vê **sua estação de trabalho** como a localização em que você pode executar comandos e o **Chef Workstation** como o pacote de software.

Por exemplo, você baixa o comando knife como parte do **Chef Workstation**, mas executa comandos knife de **sua estação de trabalho** para gerenciar a infraestrutura.

O Chef também usa os conceitos de *guias* e *receitas*. Esses termos são as políticas definidas e aplicadas aos servidores, respectivamente.

## <a name="preparing-your-workstation"></a>Preparando sua estação de trabalho

Primeiro, prepare sua estação de trabalho criando um diretório para armazenar os arquivos de configuração e guias do Chef.

Crie um diretório com o nome `C:\Chef`.

Baixe e instale a versão mais recente da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para sua estação de trabalho.

## <a name="configure-azure-service-principal"></a>Configurar uma entidade de serviço do Azure

Usaremos uma Entidade de Serviço para nos ajudar a criar recursos do Azure do Chef Workstation.  Para criar a Entidade de Serviço relevante com as permissões necessárias, execute os seguintes comandos no PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Anote sua SubscriptionID, TenantID, ClientID e o Segredo do Cliente (a senha definida anteriormente neste tutorial), pois você precisará desses valores. 

## <a name="setup-chef-server"></a>Configurar o Chef Server

Este guia considera que você se inscreverá no Hosted Chef.

Se ainda não estiver usando um Chef Server, você poderá:

* inscrever-se no [Hosted Chef](https://manage.chef.io/signup), que é a maneira mais rápida de começar a usar o Chef.
* instalar um Chef Server autônomo no computador baseado em Linux, seguindo as [instruções de instalação](https://docs.chef.io/install_server.html) em [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Criando uma conta do Hosted Chef

Inscreva-se em uma conta do Hosted Chef [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, será solicitado que você crie uma organização.

![Janela Criar organização](media/chef-automation/create-organization.png)

Depois de criar sua organização, baixe o kit inicial.

![Configurar o Chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Se você receber um aviso informando de que suas chaves serão redefinidas, não haverá problema em prosseguir, pois ainda não temos uma infraestrutura existente configurada.
>

Este arquivo zip do kit de início contém os arquivos de configuração da organização e a chave do usuário no diretório `.chef`.

O `organization-validator.pem` precisa ser baixado separadamente, pois como é uma chave privada não deve ser armazenado no Chef Server. No [Chef Manage](https://manage.chef.io/), entre na seção Administração e selecione "Redefinir Chave de Validação", que fornece um arquivo para baixar separadamente. Salve o arquivo em C:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurando sua estação de trabalho do Chef

Extraia o conteúdo do `chef-starter.zip` para `c:\chef`.

Copie todos os arquivos em `chef-starter\chef-repo\.chef` para seu diretório `c:\chef`.

Copie o arquivo `organization-validator.pem` para `c:\chef`, se ele estiver salvo em `c:\Downloads`.

O diretório agora deve ser semelhante ao exemplo a seguir.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Agora você deve ter cinco arquivos e quatro diretórios (incluindo o diretório chef-repo vazio) na raiz do C:\chef.

### <a name="edit-kniferb"></a>Editar o knife.rb

Os arquivos PEM contêm as chaves privadas da organização e administrativas para comunicação enquanto o arquivo knife.rb contém a configuração do knife. Será necessário editar o arquivo knife.rb.

Abra o arquivo knife.rb no editor de sua escolha. O arquivo inalterado deve ser semelhante a este:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adicione as seguintes informações ao knife.rb, substituindo os espaços reservados por suas informações:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Essas linhas farão o Knife fazer referência ao diretório de guias em `c:\chef\cookbooks`.

O arquivo `knife.rb` agora deve ter aparência semelhante à do seguinte exemplo:

![Exemplo de arquivo do Knife](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Instalar o Chef Workstation

Em seguida, [baixe e instale o Chef Workstation](https://downloads.chef.io/chef-workstation/).

Instale o Chef Workstation na localização padrão.

Na área de trabalho, você verá um PowerShell do CW. Essa ferramenta é usada para interagir com os produtos do Chef. O PowerShell do CW disponibiliza novos comandos, como o `chef-run` e os comandos da CLI do Chef (como o `chef`). Confira sua versão instalada do Chef Workstation e das ferramentas do Chef com `chef -v`. Você também pode verificar a versão do Workstation selecionando **Sobre o Chef Workstation** no aplicativo Chef Workstation.

`chef --version` deve retornar algo como:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> A ordem do caminho é importante! Se os caminhos opscode não estiverem na ordem correta, ocorrerão problemas.
>

Reinicie a estação de trabalho antes de continuar.

### <a name="install-knife-azure"></a>Instalar o Knife Azure

Este tutorial confira que você esteja usando o Azure Resource Manager para interagir com sua máquina virtual.

Instale a extensão Knife Azure, que inclui o plug-in do Azure.

Execute o comando a seguir.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento `–-pre` verifica se você está recebendo a versão mais recente do RC do Plug-in Knife Azure, que fornece acesso ao conjunto mais recente de APIs.
>
>

É provável que um número de dependências também seja instalado ao mesmo tempo.

![Saída da instalação knife-azure](./media/chef-automation/install-knife-azure.png)

Para garantir que tudo esteja configurado corretamente, execute o comando a seguir.

    knife azurerm server list

Se tudo estiver configurado corretamente, você verá uma lista de imagens do Azure disponíveis rolar.

Parabéns. Sua estação de trabalho está configurada.

## <a name="creating-a-cookbook"></a>Criar um guia

Um guia é usado pelo Chef para definir um conjunto de comandos que você deseja executar no cliente gerenciado. A criação de um guia é simples, basta usar o comando `chef generate cookbook` para gerar o modelo de guia. Este guia é indicado para um servidor Web que implante o IIS automaticamente.

Em `C:\Chef directory`, execute o comando a seguir.

    chef generate cookbook webserver

Esse comando gera um conjunto de arquivos no diretório C:\Chef\cookbooks\webserver. Em seguida, defina o conjunto de comandos para o cliente do Chef executar na máquina virtual gerenciada.

Os comandos são armazenados no arquivo default.rb. Nesse arquivo, defina um conjunto de comandos que instale o IIS, inicie o IIS e copie um arquivo de modelo para a pasta `wwwroot`.

Modifique o arquivo C:\chef\cookbooks\webserver\recipes\default.rb e adicione as linhas a seguir.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Quando terminar, salve o arquivo.

## <a name="creating-a-template"></a>Criando um modelo

Nesta etapa, você gerará um arquivo de modelo a ser usado como a página `default.html`.

Execute o seguinte comando para gerar o modelo:

    chef generate template webserver Default.htm

Navegue até o arquivo `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Edite o arquivo adicionando um código HTML simples *Olá, Mundo* e salve o arquivo.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregar o guia para o Chef Server

Nesta etapa, você fará uma cópia do guia que você criou no computador local e a carregará no servidor do Chef Hosted. Depois de ser carregado, o guia será exibido na guia **Política**.

    knife cookbook upload webserver

![Resultados da instalação do guia no Chef Server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implantar uma máquina virtual com o Knife Azure

Implante uma máquina virtual do Azure e aplique o guia `Webserver` usando o comando `knife`.

O comando `knife` também instalará o serviço Web do IIS e a página da Web padrão.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

O comando de exemplo `knife` cria uma máquina virtual *Standard_DS2_v2* com o Windows Server 2016 instalado na região Oeste dos EUA. Modifique esses valores de acordo com as necessidades do seu aplicativo.

Após executar o comando, navegue até o portal do Azure para ver o computador começar a provisionar.

![Máquina virtual que está sendo provisionada](./media/chef-automation/virtual-machine-being-provisioned.png)

O prompt de comando aparecem a seguir.

![Saída do Knife ao criar a máquina virtual](./media/chef-automation/knife-output-when-creating-vm.png)

Quando a implantação for concluída, o endereço IP público da nova máquina virtual será exibido. Cole esse valor em um navegador da Web para exibir o novo site. Quando implantamos a máquina virtual, abrimos a porta 80 para que ela fique disponível externamente.   

![Testar a máquina virtual](./media/chef-automation/testing-the-virtual-machine.png)

Este exemplo usa o código HTML criativo.

Você também pode exibir o status do nó [Chef Manage](https://manage.chef.io/). 

![Exibir o status do nó](./media/chef-automation/viewing-node-status.png)

Não se esqueça de que você também pode se conectar por meio de uma sessão RDP do portal do Azure pela porta 3389.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Chef no Azure](/azure/chef/)