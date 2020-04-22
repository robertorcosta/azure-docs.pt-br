---
title: Desenvolva um aplicativo web seguro | Microsoft Docs
description: Este simples aplicativo de exemplo implementa práticas recomendadas de segurança que melhoram sua aplicação e a postura de segurança da sua organização quando você se desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686803"
---
# <a name="develop-a-secure-web-app"></a>Desenvolver um aplicativo Web seguro

Esta amostra é um aplicativo Python simples que exibe uma página da Web contendo links para recursos de segurança para o desenvolvimento de aplicativos no Azure. O aplicativo implementa práticas recomendadas de segurança que podem ajudar a melhorar seu aplicativo e a postura de segurança da sua organização quando você desenvolve aplicativos no Azure.

Você deve seguir as etapas descritas neste artigo sequencialmente para garantir que os componentes do aplicativo estejam configurados corretamente. O banco de dados, o Azure App Service, a instância do Azure Key Vault e a ocorrência do Azure Application Gateway dependem um do outro.

Os scripts de implantação configuram a infra-estrutura. Depois de executar os scripts de implantação, você precisará fazer alguma configuração manual no portal Azure para vincular os componentes e serviços juntos.

O aplicativo de exemplo é direcionado para iniciantes que desenvolvem aplicativos no Azure que desejam implementar medidas de segurança em seus aplicativos.

Ao desenvolver e implantar este aplicativo, você aprenderá como:

- Crie uma instância do Azure Key Vault, armazene e recupere segredos dele.
- Implantar o Banco de Dados Azure para PostgreSQL, configurar senhas seguras e autorizar o acesso a ele.
- Execute um contêiner Alpine Linux no Azure Web Apps para Linux e habilite identidades gerenciadas para recursos do Azure.
- Crie e configure uma instância do Gateway do aplicativo Azure com um firewall que usa [o OWASP Top 10 Ruleset](https://coreruleset.org/).
- Habilite a criptografia de dados em trânsito e em repouso usando os serviços do Azure.

Depois de desenvolver e implantar este aplicativo, você terá configurado o seguinte aplicativo web de amostra, juntamente com as medidas de configuração e segurança descritas.

![Exemplo de aplicativo web](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Arquitetura

O aplicativo é um aplicativo n-tier típico com três níveis. A camada front-end, back-end e banco de dados com componentes de monitoramento e gerenciamento secreto integrados são mostrados aqui:

![Arquitetura do aplicativo](./media/secure-web-app/architecture.png)

A arquitetura consiste nesses componentes:

- [Gateway de Aplicativo do Azure](../../application-gateway/index.yml). Fornece o gateway e o firewall para nossa arquitetura de aplicativos.
- [Azure Web Apps no Linux](../../app-service/containers/app-service-linux-intro.md). Fornece tempo de execução do contêiner para executar o aplicativo Python em um ambiente Linux.
- [Azure Key Vault](../../key-vault/index.yml). Armazena e criptografa os segredos do nosso aplicativo e gerencia a criação de políticas de acesso ao seu redor.
- [Banco de dados Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql/). Armazena com segurança os dados do nosso aplicativo.
- [Azure Security Center](../../security-center/index.yml) e [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Fornece monitoramento e alertas sobre o funcionamento do nosso aplicativo.

## <a name="threat-model"></a>Modelo de ameaça

Modelagem de ameaças é o processo de identificar potenciais ameaças à segurança de sua empresa e aplicativo e, em seguida, garantir que um plano de mitigação adequado esteja em vigor.

Esta amostra usou a [Ferramenta de Modelagem de Ameaças](threat-modeling-tool.md) da Microsoft para implementar a modelagem de ameaças para o aplicativo de amostra segura. Ao diagramar os componentes e os fluxos de dados, você pode identificar problemas e ameaças no início do processo de desenvolvimento. Isso economiza tempo e dinheiro depois.

Este é o modelo de ameaça para o aplicativo de amostra:

![Modelo de ameaça](./media/secure-web-app/threat-model.png)

Algumas ameaças de exemplo e potenciais vulnerabilidades que a ferramenta de modelagem de ameaças gera são mostradas na captura de tela a seguir. O modelo de ameaça dá uma visão geral da superfície de ataque exposta e leva os desenvolvedores a pensar em como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção de SQL na saída do modelo de ameaça anterior é atenuada pela higienização das entradas do usuário e pelo uso de funções armazenadas no Banco de Dados Azure para PostgreSQL. Essa mitigação impede a execução arbitrária de consultas durante a leitura e a gravação de dados.

Os desenvolvedores melhoram a segurança geral do sistema, mitigando cada uma das ameaças na saída do modelo de ameaças.

## <a name="deployment"></a>Implantação

As seguintes opções permitem que você execute o Linux no Azure App Service:

- Escolha um contêiner na lista de contêineres Microsoft pré-construídos no Azure que foram criados com tecnologias de suporte (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Use um recipiente personalizado. Selecione seus próprios registros de contêineres como a fonte da imagem e baseie-se nas muitas tecnologias disponíveis que suportam HTTP.

Neste exemplo, você executará o script de implantação que implantará o webapp no App Service e criará os recursos.

O aplicativo pode usar os diferentes modelos de implantação mostrados abaixo:

![Diagrama de fluxo de dados de implantação](./media/secure-web-app/deployment.png)

Existem muitas maneiras de implantar aplicativos no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- Azure DevOps

Este aplicativo utilizado:

- [Docker](https://docs.docker.com/) para criar e construir as imagens do contêiner.
- [Cli Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para implantação.
- [Docker Hub](https://hub.docker.com/) como registro de contêineres.

## <a name="security-considerations"></a>Considerações sobre segurança

### <a name="network"></a>Rede

O aplicativo de exemplo usa criptografia TLS/SSL de ponta a ponta para dados em trânsito que entram e saem da rede. O gateway é configurado com um certificado auto-assinado.
> [!IMPORTANT]
> Um certificado auto-assinado é usado nesta demonstração. Em um ambiente de produção, você deve obter certificados de uma Autoridade de Certificado (CA) verificada.

O firewall do aplicativo também inspeciona o tráfego de entrada e alerta os admins quando o tráfego malicioso é detectado no tráfego da rede.
O Application Gateway mitiga a possibilidade de ameaças de injeção DDoS e SQL descobertas no modelo de ameaça.

### <a name="identity"></a>Identidade

Para entrar no portal, o aplicativo de exemplo usa administradores do Azure Active Directory (Azure AD) que têm acesso aos recursos.
O aplicativo de exemplo usa identidades gerenciadas para obter permissões para ler e recuperar segredos do Azure Key Vault, garantindo que o aplicativo não precise de credenciais de código rígido e tokens para ler os segredos. O Azure AD cria automaticamente os princípios de serviço que o aplicativo precisa ler e modifica os segredos quando as identidades gerenciadas são usadas.

As identidades gerenciadas para os recursos do Azure e do MFA tornam mais difícil para os adversários obter privilégios e aumentar seus privilégios no sistema. Essa ameaça foi apontada no modelo de ameaça.
O aplicativo usa o OAuth, que permite que os usuários cadastrados no aplicativo OAuth entrem no aplicativo.

### <a name="storage"></a>Armazenamento

Os dados no banco de dados PostgreSQL são criptografados automaticamente pelo Banco de Dados Azure para PostgreSQL. O banco de dados autoriza os endereços IP do App Service para que apenas o aplicativo web do App Service implantado possa acessar os recursos do banco de dados com as credenciais de autenticação certas.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

O aplicativo implementa o registro usando o Application Insights para rastrear métricas, logs e exceções que ocorrem. Esse registro fornece metadados suficientes do aplicativo para informar os desenvolvedores e membros da equipe de operações sobre o status do aplicativo. Ele também fornece dados suficientes para retroceder em caso de incidentes de segurança.

## <a name="cost-considerations"></a>Considerações de custo

Se você ainda não tem uma conta no Azure, você pode criar uma gratuita. Vá para a [página de conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que você pode fazer com uma conta gratuita do Azure e saiba quais produtos são gratuitos por 12 meses.

Para implantar os recursos no aplicativo de exemplo com os recursos de segurança, você precisa pagar por alguns recursos premium. À medida que o aplicativo escala e os níveis e testes gratuitos oferecidos pelo Azure precisam ser atualizados para atender aos requisitos do aplicativo, seus custos podem aumentar. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure para estimar seus custos.

## <a name="deploy-the-solution"></a>Implantar a solução

### <a name="prerequisites"></a>Pré-requisitos

Para colocar o aplicativo em funcionamento, você precisa instalar essas ferramentas:

- Um editor de código para modificar e visualizar o código do aplicativo. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código aberto.
- [Azure CLI](/cli/azure/install-azure-cli) em seu computador de desenvolvimento.
- [Git](https://git-scm.com/) em seu sistema. O Git é usado para clonar o código fonte localmente.
- [jq](https://stedolan.github.io/jq/), uma ferramenta UNIX para consultar JSON de forma fácil de usar.

Você precisa de uma assinatura do Azure para implantar os recursos do aplicativo de exemplo. Se você não tiver uma assinatura do Azure, você pode [criar uma conta gratuita](https://azure.microsoft.com/free/) para testar o aplicativo de exemplo.

Depois de instalar essas ferramentas, você está pronto para implantar o aplicativo no Azure.

### <a name="environment-setup"></a>Configuração do ambiente

Execute os scripts de implantação para configurar o ambiente e a assinatura:

1. Para clonar o repositório de código-fonte, use este comando Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Para passar para o diretório, use este comando:

   ```shell
   cd tutorial-project/scripts
   ```

3. Existem arquivos na pasta de scripts que são específicos da plataforma que você está usando (Windows ou Linux). Como o Azure CLI já foi instalado, faça login na conta do Azure no prompt de comando executando este comando Azure CLI:

   ```azurecli-interactive
   az login
   ```

O navegador abrirá, faça login com suas credenciais. Depois de fazer login, você pode começar a implantar os recursos a partir do prompt de comando.

Os scripts `deploy-powershell.ps1` `deploy-bash.sh` de implantação e o código de contenção que implantam todo o aplicativo.
Para implantar a solução:

1. Se você estiver no `deploy-powershell.ps1` PowerShell `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` execute o arquivo digitando a substituição do nome da região e do grupo de recursos por regiões Azure adequadas e um nome para o grupo de recursos
2. Se você estiver no `deploy-bash.sh` Linux `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`executar o arquivo digitando, você pode ter que tornar o arquivo executável digitando`chmod +x deploy-bash.sh`

Os exemplos a seguir mostram trechos dos componentes-chave. Você pode implantar os exemplos individualmente ou com o resto dos componentes executando os arquivos de implantação.

### <a name="implementation-guidance"></a>Diretrizes de implementação

O script de implantação é um script que pode ser dividido em quatro fases. Cada fase implanta e configura um recurso Azure que está no [diagrama](#architecture)de arquitetura .

As quatro fases são:

- Implantar o Cofre de Chaves Azure.
- Implantar banco de dados Azure para PostgreSQL.
- Implantar aplicativos Web do Azure no Linux.
- Implantar o Gateway de aplicativos com firewall de aplicativos web.

Cada fase se baseia na anterior usando a configuração dos recursos anteriormente implantados.

Para concluir as etapas de implementação, certifique-se de que você instalou as ferramentas listadas em [Pré-requisitos](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Implantar o cofre de chaves do Azure

Nesta seção, você cria e implanta uma instância do Azure Key Vault que é usada para armazenar segredos e certificados.

Depois de concluir a implantação, você tem uma ocorrência do Azure Key Vault implantada no Azure.

Para implantar o Azure Key Vault usando o Azure CLI:

1. Declare as variáveis para Azure Key Vault.
2. Registre o provedor do Azure Key Vault.
3. Crie o grupo de recursos para a ocorrência.
4. Crie a instância do Azure Key Vault no grupo de recursos criado na etapa 3.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

É uma prática recomendada usar identidades gerenciadas para recursos do Azure em aplicativos que usam o Key Vault para acessar recursos. Sua postura de segurança aumenta quando as chaves de acesso ao Key Vault não são armazenadas em código ou configuração.

#### <a name="deploy-azure-database-for-postgresql"></a>Implantar banco de dados Azure para PostgreSQL

O Banco de Dados Do Azure para PostgreSQL funciona da seguinte forma, primeiro crie o servidor de banco de dados e crie o banco de dados no qual armazenar o esquema e os dados.

Depois de concluir a implantação, você tem um servidor PostgreSQL e um banco de dados em execução no Azure.

Para implantar o Banco de Dados Azure para PostgreSQL usando o Azure CLI:

1. Abra um terminal com o Azure CLI e sua configuração de assinatura do Azure.
2. Gerar uma combinação segura de nome de usuário e senha que é usada para acessar o banco de dados. (Estes devem ser armazenados no Azure Key Vault para aplicativos que os usam.)
3. Crie a instância do servidor PostgreSQL.
4. Crie um banco de dados na instância do servidor que você criou na etapa 3.
5. Execute scripts PostgreSQL na instância PostgreSQL.

O código abaixo se baseia nos segredos PGUSERNAME e PGPASSWORD armazenados no Azure KeyVault a partir do passo de implantação do KeyVault acima.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Depois de implantar o banco de dados, você precisa armazenar suas credenciais e seqüência de conexão no Azure Key Vault.
Na pasta scripts, há `functions.sql` um arquivo que contém o código PL/pgSQL que cria funções armazenadas quando você executá-lo. A execução deste arquivo parametriza as entradas para limitar a injeção SQL.

PostgreSQL é empacotado `psql` com uma ferramenta chamada que é usada para se conectar ao banco de dados. Para `functions.sql`ser executado, você precisa se conectar ao banco de dados Do Zure para a instância PostgreSQL da sua máquina local e executá-lo a partir daí. A instalação da ferramenta psql está incluída na instalação padrão para PostgreSQL em cada sistema operacional.
Para obter mais informações, consulte a [documentação do psql](https://www.postgresql.org/docs/9.3/app-psql.html).

O Azure Cloud Shell `psql` também inclui a ferramenta. Você pode usar o Cloud Shell diretamente do portal Azure selecionando o Ícone cloud shell.

Para habilitar o acesso remoto à instância PostgreSQL, você precisa autorizar o endereço IP no PostgreSQL.
Você habilita esse acesso indo para a guia **de segurança Conexão,** selecionando **Adicionar IP do cliente**e salvando as novas configurações.

![Autorizar ip do cliente](./media/secure-web-app/add-client-ip-postgres.png)

Se você estiver usando o Cloud Shell em vez da ferramenta psql local, selecione **Permitir acesso aos serviços do Azure** e alterar seu valor para **ON** para permitir o acesso ao Cloud Shell.

Em seguida, conecte-se à instância executando o comando psql abaixo com parâmetros de seqüência de conexão da guia **Conexão strings** da instância PostgreSQL no portal Azure.
Substitua as chaves vazias por parâmetros da lâmina de conexão string do banco de dados e da senha com a senha do Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Execute o seguinte script PL/pgSQL depois de ter certeza de que está conectado ao banco de dados. O script cria as funções armazenadas usadas para inserir dados no banco de dados.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Para obter mais informações sobre como configurar a verificação de TLS e Certificate Authority (CA) para PostgreSQL, consulte [Configure conectividade TLS no Banco de Dados Azure para PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Um certificado raiz está incluído no recipiente. As medidas tomadas para obter o certificado são:

1. Baixe o arquivo de certificado da Autoridade de [Certificado](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Baixe e instale o OpenSSL em sua máquina](/azure/postgresql/concepts-ssl-connection-security).
3. Decodfique seu arquivo de certificado:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Leia mais sobre como configurar a segurança TLS para PostgreSQL aqui [Configure a segurança de conexão TLS](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Implantar aplicativos Web do Azure no Linux

Você pode facilmente construir serviços Linux em cima do Azure App Service, pois o Azure fornece um conjunto de contêineres e imagens pré-construídos para linguagens amplamente usadas como Python, Ruby, C#e Java. O Azure também suporta contêineres personalizados, o que pode permitir que praticamente todas as linguagens de programação seja executada na plataforma Azure App Service.

O aplicativo que está sendo implantado é um simples aplicativo Python que é executado na mais recente distribuição Linux do Ubuntu. Ele se conecta às instâncias Azure Key Vault e PostgreSQL que foram criadas nas seções anteriores para gerenciamento de credenciais e armazenamento de dados, respectivamente.

O seguinte arquivo Docker é fornecido na pasta raiz do aplicativo:

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

O Arquivo Docker acima é usado para construir o contêiner hospedado `mcr.microsoft.com/samples/basic-linux-app`no Registro de Contêineres do Azure em .

O código abaixo:

1. Declara as variáveis e nomes para a instância do Serviço de Aplicativo.
2. Cria o grupo de recursos para o plano App Service.
3. Provisionamento de uma instância de aplicativos Web do Azure na instância de contêineres Linux.
4. Habilita o registro do contêiner do aplicativo web.
5. Define algumas configurações de aplicativos nas configurações do aplicativo do contêiner.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Este script cria uma identidade atribuída para a instância do Serviço de Aplicativo que pode ser usada com o MSI para interagir com o Azure Key Vault sem segredos de codificação rígidos em código ou configuração.

Vá até a instância do Azure Key Vault no portal para autorizar a identidade atribuída na guia de diretiva de acesso. Selecione **Adicionar nova diretiva de acesso**. Em **Select principal,** procure o nome do aplicativo semelhante ao nome da instância do Serviço de Aplicativo criada.
Um princípio de serviço anexado ao aplicativo deve ser visível. Selecione-o e salve a página de diretiva de acesso, conforme mostrado na captura de tela a seguir.

Como o aplicativo só precisa recuperar chaves, selecione a permissão **Obter** nas opções de segredos, permitindo o acesso enquanto reduz os privilégios concedidos.

![Política de acesso ao Key Vault](./media/secure-web-app/kv-access-policy.png)

*Crie uma política de acesso do Key Vault*

Salve a diretiva de acesso e salve a nova alteração na guia **Políticas de acesso** para atualizar as políticas.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Implantar o Application Gateway com firewall de aplicativos web ativado

Em aplicativos web, não é recomendável que você exponha serviços diretamente para o mundo exterior na internet.
As regras de balanceamento de carga e firewall fornecem mais segurança e controle sobre o tráfego de entrada e ajudam a gerenciá-lo.

Para implantar uma instância do Gateway de aplicativos:

1. Crie o grupo de recursos para abrigar o gateway de aplicativo.
2. Provisionar uma rede virtual para anexar ao gateway.
3. Crie uma sub-rede para o gateway na rede virtual.
4. Provisão um endereço IP público.
5. Provisão o gateway de aplicação.
6. Habilite o firewall do aplicativo web no gateway.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

O script anterior:

1. Cria um novo certificado auto-assinado no Azure.
2. Baixa o certificado auto-assinado como um arquivo codificado pela base64.
3. Gera uma senha para o certificado auto-assinado.
4. Exporta o certificado como um arquivo PFX assinado com a senha.
5. Armazena a senha do certificado no Azure Key Vault.

Esta seção implanta o gateway de aplicativo:

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Depois de concluir a implantação, você tem um gateway de aplicativo com firewall de aplicativo web ativado.

A instância do gateway expõe a porta 443 para HTTPS. Essa configuração garante que nosso aplicativo só esteja acessível na porta 443 através de HTTPS.

Bloquear portas não utilizadas e limitar a exposição à superfície de ataque é uma prática recomendada de segurança.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Adicionar grupos de segurança de rede à instância do Serviço de Aplicativo

As instâncias do App Service podem ser integradas a redes virtuais. Essa integração permite que eles sejam configurados com políticas de grupo de segurança de rede que gerenciam o tráfego de entrada e saída do aplicativo.

1. Para habilitar esse recurso, na lâmina de instância de serviço do Azure App, em **Configurações,** **selecione Rede**. No painel direito, em **Integração VNet,** selecione **Clique aqui para configurar**.

   ![Nova integração de rede virtual](./media/secure-web-app/app-vnet-menu.png)

    *Nova integração de rede virtual para serviço de aplicativos*

1. Na próxima página, selecione **Adicionar VNET (visualização)**.

1. No menu seguinte, selecione a rede virtual criada `hello-vnet`na implantação que começa com . Você pode criar uma nova sub-rede ou selecionar uma já existente.
   Neste caso, crie uma nova sub-rede. Defina o **intervalo de endereços** para **10.0.3.0/24** e nomeie a **sub-sub-rede de aplicativos de sub-rede**.

   ![Configuração de rede virtual do App Service](./media/secure-web-app/app-vnet-config.png)

    *Configuração de rede virtual para serviço de aplicativos*

Agora que você habilitou a integração de rede virtual, você pode adicionar grupos de segurança de rede ao nosso aplicativo.

1. Use a caixa de pesquisa, procure grupos **de segurança da rede**. Selecione **grupos de segurança da rede** nos resultados.

    ![Procure por grupos de segurança de rede](./media/secure-web-app/nsg-search-menu.png)

    *Procure por grupos de segurança de rede*

2. No menu seguinte, selecione **Adicionar**. Digite o **nome** do NSG e do **grupo Resource** no qual ele deve estar localizado. Este NSG será aplicado à sub-rede do gateway de aplicativo.

    ![Criar um NSG](./media/secure-web-app/nsg-create-new.png)

    *Criar um NSG*

3. Depois que o NSG for criado, selecione-o. Em sua lâmina, em **Configurações,** selecione **Regras de segurança de entrada**. Configure essas configurações para permitir que as conexões entrem no gateway de aplicativo sobre a porta 443.

   ![Configure o NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configure o NSG*

4. Nas regras de saída para o GATEWAY NSG, adicione uma regra que permite conexões de saída `AppService`à instância do App Service, criando uma regra que visa a tag de serviço :

   ![Adicionar regras de saída para o NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Adicionar regras de saída para o NSG*

    Adicione outra regra de saída para permitir que o gateway envie regras de saída para uma rede virtual.

   ![Adicione outra regra de saída](./media/secure-web-app/nsg-outbound-vnet.png)

    *Adicione outra regra de saída*

5. Na lâmina de sub-redes do NSG, selecione **Associate,** selecione a rede virtual criada na implantação e selecione a sub-rede de gateway chamada **gw-subnet**. O NSG é aplicado à sub-rede.

6. Crie outro NSG como na etapa anterior, desta vez para a instância do Serviço de Aplicativo. Dê-lhe um nome. Adicione a regra de entrada para a porta 443 como você fez para o portal de aplicativos NSG.

   Se você tiver uma instância do App Service implantada em uma instância do App Service Environment, o que não é o caso deste aplicativo, você pode adicionar regras de entrada para permitir testes do Azure Service Health abrindo portas 454-455 nos grupos de segurança de entrada do seu App Service NSG. Aqui está a configuração:

   ![Adicionar regras para testes de saúde do Serviço Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Adicionar regras para testes azure Service Health (somente o App Service Environment)*

7. Nas regras de segurança de saída, crie uma nova regra de segurança de saída que permite que a instância do App Service se comunique com o banco de dados PostgreSQL. Configurá-lo assim:

   ![Regra para permitir conexões PostgreSQL de saída](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Adicionar uma regra para permitir conexões PostgreSQL de saída*

Para limitar a superfície de ataque, modifique as configurações da rede app service para permitir que apenas o gateway do aplicativo acesse o aplicativo.
Você faz isso entrando na guia de rede do App Service, selecionando a guia **Restrições de IP** e criando uma regra de permitir que apenas o IP do gateway do aplicativo acesse diretamente o serviço.

Você pode recuperar o endereço IP do gateway a partir de sua página de visão geral. Na guia **CIDR de endereço IP,** digite `<GATEWAY_IP_ADDRESS>/32`o endereço IP neste formato: .

![Permitir apenas o gateway](./media/secure-web-app/app-allow-gw-only.png)

*Permitir apenas o IP gateway para acessar o Serviço de Aplicativo*

#### <a name="implement-azure-active-directory-oauth"></a>Implementar o Azure Active Directory OAuth

Os documentos do Azure distribuídos na página do aplicativo web de amostra são recursos em nosso aplicativo que podem precisar de proteção. Você pode usar o Azure Active Directory (Azure AD) para implementar autenticação para aplicativos web, desktop e mobile usando diferentes fluxos de autenticação.
O aplicativo usa login com a **Microsoft,** que permite que o aplicativo leia perfis de usuários que foram adicionados à lista de usuários do Azure AD de um único inquilino.

No portal Azure, configure o aplicativo para usar as credenciais necessárias:

1. Selecione **O Diretório Ativo do Azure**ou pesquise-o usando a caixa de pesquisa.

2. Selecione **Novo registro**:

   ![Crie um registro](./media/secure-web-app/ad-auth-create.png)

   *Crie um registro de aplicativo AD do Azure*

3. Na página seguinte, digite o nome do aplicativo. Em **tipos de conta suportados,** selecione Contas somente neste diretório **organizacional**.
    Em **Redirecionar URI,** digite o domínio base em que o aplicativo será executado mais um com o ponto final do token. Por exemplo: *GATEWAY_HASH*.cloudapp.net/token.

   ![Configure o registro do aplicativo Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Configure o registro do aplicativo Azure AD*

4. Você é apresentado com uma tela que mostra o aplicativo cadastrado e suas informações. Você precisa adicionar essas informações na instância do Azure Key Vault.
   1. Copie o ID do aplicativo (cliente) `CLIENTID`e salve-o no Key Vault como .
   2. Copie o URI de redirecionamento que você `REDIRECTURI`inseriu na etapa anterior e salve-o como .
   3. Copie o nome padrão do azure AD, que tem o *nome*de `TENANT`formato .microsoftonline.com, e salve-o no Key Vault como .
   4. Vá para a guia **De si& segredos** do aplicativo Azure AD que você criou anteriormente e selecione **Novo segredo do cliente,** conforme mostrado na captura de tela a seguir. Defina uma data de validade e copie o valor `CLIENTSECRET`gerado e salve-o no Key Vault como .

      ![Segredo de autorização do Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Segredo de autorização do Azure AD*

   5. Gere uma chave secreta aleatória segura usando qualquer ferramenta de linha de comando/on-line. Guarde-o no `FLASKSECRETKEY`Key Vault como . A estrutura do aplicativo usa essa chave para criar sessões.
        Para aprender a gerar uma chave secreta, consulte [Sessões de Frascos](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Depois de configurar o login, você precisa adicionar os usuários ao link Azure AD para permitir que eles entrem no recurso. Para adicioná-los, vá para a guia **Usuários** no Azure AD, selecione **Todos os usuários**e selecione Novo **usuário** ou Novo **usuário convidado**. Para testes, você pode adicionar um usuário convidado e convidar o usuário para o diretório. Ou você pode adicionar um novo usuário se o domínio em que o aplicativo está sendo executado for validado. Neste exemplo, apenas usuários cadastrados no inquilino Azure AD podem ser cadastrados para acesso. Para obter informações sobre acesso ao login multilocatário, consulte a documentação.

   ![Adicionar usuários ao domínio padrão](./media/secure-web-app/ad-auth-add-user.png)

   *Adicionar usuários ao domínio padrão do Azure Active Directory*

Depois de adicionar a configuração e os segredos do Azure AD ao Key Vault, os usuários podem ser autenticados no aplicativo usando a autenticação Do Azure OAuth.
No código do aplicativo, isso é tratado pela Azure Active Directory Authenticy Library (ADAL).

Depois que os segredos estiverem no Key Vault e o aplicativo tiver acesso aos segredos e ao banco\/de dados, o serviço de aplicativo pode ser alcançado através da URL do aplicativo do gateway (https: /GATEWAY_HASH.cloudapp.net), que você pode obter a partir de sua lâmina.

Se, ao fazer login no Azure AD, você tiver um erro que diz "O usuário não está registrado no diretório em que você está tentando fazer login", você precisa adicionar o usuário. Para adicionar o usuário, vá para a guia **Usuários** do Azure AD e adicione o usuário manualmente inserindo seus dados ou convide o usuário inserindo seu endereço de e-mail como usuário convidado para o Azure AD na lâmina **Convidar convidado.**

#### <a name="deploy-application-insights"></a>Implantar Application Insights
Agora que o aplicativo está implantado e funcionando, você precisa lidar com erros que ocorrem dentro do aplicativo, juntamente com o registro e a coleta de dados.
O registro e a coleta de dados de rastreamento fornecem uma visão sobre eventos de auditoria que acontecem no aplicativo.

O Application Insights é um serviço que coleta logs que podem ser gerados pelos usuários ou pelo sistema.

Para criar uma instância de insights de aplicativos:

1. Procure **insights de aplicativos** usando a caixa de pesquisa no portal Azure.
2. Selecione **Application Insights**. Forneça os detalhes mostrados aqui para criar uma instância.

   ![Crie uma instância de insights de aplicativos](./media/secure-web-app/app-insights-data.png)

Depois que a implantação estiver concluída, você terá uma instância do Application Insights.

Depois de criar a instância Applications Insights, você precisa conscientizar o aplicativo sobre a chave de instrumentação que permite enviar logs para a nuvem. Você faz isso recuperando a chave do Application Insights e usando-a dentro das bibliotecas de aplicativos que o Azure fornece para insights de aplicativos. A melhor prática é armazenar chaves e segredos no Azure Key Vault para mantê-los seguros.

Para o aplicativo de exemplo básico, depois de criar a instância Applications Insights, você precisa conscientizar o aplicativo sobre a chave de instrumentação que permite enviar logs para a nuvem.
Em Key Vault, `APPINSIGHTSKEY` defina um segredo e defina seu valor como a chave de instrumentação. Isso permite que o aplicativo envie logs e métricas para o Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementar autenticação multifatorial para diretório ativo do Azure

Os administradores precisam garantir que as contas de assinatura no portal estejam protegidas. A assinatura é vulnerável a ataques porque gerencia os recursos que você criou. Para proteger a assinatura, habilite a Autenticação Multifatorial na guia Diretório Ativo do **Azure** da assinatura.

O Azure AD opera com base em políticas que são aplicadas a um usuário ou grupos de usuários que se encaixam em determinados critérios.
O Azure cria uma diretiva padrão especificando que os administradores precisam de autenticação de dois fatores para entrar no portal.
Depois de habilitar esta política, você pode ser solicitado a sair e entrar de volta no portal Azure.

Para habilitar o MFA para logins de admin:

1. Vá para a guia Diretório ativo do **Azure** no portal Azure
2. Na categoria de segurança, selecione o acesso condicional. Você verá esta tela:

   ![Acesso Condicional - Políticas](./media/secure-web-app/ad-mfa-conditional-add.png)

Se você não pode criar uma nova política:

1. Vá para a guia **MFA.**
2. Selecione o link de **avaliação azure** AD Premium Free para assinar a avaliação gratuita.

   ![Teste gratuito azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Retorne à tela de acesso condicional.

1. Selecione a nova guia de diretiva.
2. Insira o nome da política.
3. Selecione os usuários ou grupos para os quais deseja ativar o MFA.
4. Em **Controles de acesso,** selecione a guia **Conceder** e, em seguida, **selecione Exigir autenticação multifatorial** (e outras configurações, se quiser).

   ![Exigir MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Você pode habilitar a diretiva selecionando a caixa de seleção na parte superior da tela ou fazê-lo na guia **Acesso Condicional.** Quando a política é ativada, os usuários precisam que o MFA faça login no portal.

Há uma política de linha de base que requer MFA para todos os administradores do Azure. Você pode habilitá-lo imediatamente no portal. Habilitar essa diretiva pode invalidar a sessão atual e forçá-lo a fazer login novamente.

Se a política de linha de base não estiver habilitada:

1. Selecione **Exigir MFA para admins**.
2. Selecione **Usar a diretiva imediatamente**.

   ![Selecionar política de uso imediatamente](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Use o Azure Sentinel para monitorar aplicativos e recursos

À medida que um aplicativo cresce, torna-se difícil agregar todos os sinais de segurança e métricas recebidos dos recursos e torná-los úteis de forma orientada à ação.

O Azure Sentinel foi projetado para coletar dados, detectar os tipos de ameaças possíveis e fornecer visibilidade a incidentes de segurança.
Enquanto aguarda a intervenção manual, o Azure Sentinel pode contar com cartilhas pré-escritas para iniciar alertas e processos de gerenciamento de incidentes.

O aplicativo de exemplo é composto por vários recursos que o Azure Sentinel pode monitorar.
Para configurar o Azure Sentinel, primeiro você precisa criar um espaço de trabalho do Log Analytics que armazena todos os dados coletados dos vários recursos.

Para criar este espaço de trabalho:

1. Na caixa de pesquisa no portal Azure, procure por **Log Analytics**. Selecione **espaços de trabalho do Log Analytics**.

   ![Pesquisar espaços de trabalho do Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Pesquisar espaços de trabalho do Log Analytics*

2. Na página seguinte, **selecione Adicionar** e, em seguida, forneça um nome, grupo de recursos e localização para o espaço de trabalho.
   ![Criar um espaço de trabalho do Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Crie um espaço de trabalho do Log Analytics*

3. Use a caixa de pesquisa para procurar **o Azure Sentinel**.

   ![Pesquisar por Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Procure o Azure Sentinel*

4. Selecione **Adicionar** e, em seguida, selecione o espaço de trabalho log analytics que você criou anteriormente.

   ![Adicionar um espaço de trabalho do Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Adicionar um espaço de trabalho do Log Analytics*

5. Na página **Azure Sentinel - Conectores de dados,** em **Configuração,** selecione **conectores de dados**. Você vê uma matriz de serviços do Azure que você pode vincular à instância de armazenamento do Log Analytics para análise no Azure Sentinel.

   ![Conectores de dados do Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Adicione um conector de dados ao Azure Sentinel*

   Por exemplo, para conectar o gateway de aplicativo, dê estas etapas:

   1. Abra a lâmina de ocorrência do Gateway do aplicativo Azure.
   2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
   3. Selecione **Adicionar configuração de diagnóstico**.

      ![Adicionar diagnósticos do Gateway de aplicativos](./media/secure-web-app/sentinel-gateway-connector.png)

      *Adicionar diagnósticos do Gateway de aplicativos*

   4. Na página Configurações de **Diagnóstico,** selecione o espaço de trabalho log analytics que você criou e selecione todas as métricas que deseja coletar e envie para o Azure Sentinel. Clique em **Salvar**.

        ![Configurações do conector Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Configurações do conector Azure Sentinel*

  As métricas do recurso estão no Azure Sentinel, onde você pode consultar e investigá-las.

   Adicione as mesmas métricas nas configurações de diagnóstico do Azure Key Vault, o endereço IP público, o Banco de Dados Azure para PostgreSQL e quaisquer serviços que suportem logs de diagnóstico em sua conta.

Depois de configurar as métricas, o Azure Sentinel tem dados para analisar.

## <a name="evaluate-and-verify"></a>Avaliar e verificar

Depois de desenvolver e implantar a arquitetura, você precisa garantir que o código e os serviços implantados atendam aos padrões de segurança. Estas são algumas etapas que você pode tomar para verificar o software:

- Análise de código estático
- Verificação de vulnerabilidade
- Encontrar e corrigir vulnerabilidades em dependências de aplicativos

Estes são os blocos básicos de construção para as melhores práticas em desenvolvimento seguro.

### <a name="static-code-analysis"></a>Análise de código estático

Para o aplicativo de amostragem, a verificação com ferramentas de análise estática envolve encontrar vulnerabilidades no código do aplicativo usando técnicas como verificação de manchas e análise de fluxo de dados. As ferramentas de análise estática python dão mais confiança de que seu aplicativo está seguro.

**Linting**

PyFlakes, uma biblioteca de fiader Python, ajuda você a remover código morto e funções não utilizadas de aplicativos, como mostrado aqui:

![Flocos-de-py](./media/secure-web-app/pyflakes.png)

O Linting fornece dicas e possíveis alterações que podem tornar seu código mais limpo e menos propenso a erros durante o tempo de execução.

**PyLint**

PyLint forneceu o maior valor para este projeto. Ele executa verificações padrão de código, verificação de erros e dicas de refatoração para garantir que o código em execução no servidor esteja seguro. Ao usar pyLint para atualizar seu código, você pode eliminar bugs e melhorar a classificação PyLint, como mostram as imagens a seguir.

![Antes de PyLint](./media/secure-web-app/before-pylint.png)

*Antes de PyLint*

Depois de corrigir alguns dos erros de código encontrados pelas ferramentas de fiação, você tem mais confiança de que o código não é propenso a erros. Corrigir os erros reduz significativamente os riscos de segurança que podem ocorrer quando o código é implantado em ambientes de produção.

![Depois de Pylint](./media/secure-web-app/after-pylint.png)

*Depois de PyLint*

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A ferramenta [ZAP do OWASP](https://www.zaproxy.org/) é um scanner de vulnerabilidade de aplicativo web de código aberto que você pode usar para verificar as vulnerabilidades do aplicativo de amostra. A execução da ferramenta no aplicativo de amostra revela alguns possíveis erros e vetores de ataque.

![Ferramenta ZAP](./media/secure-web-app/zap-tool.png)

*Ferramenta ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Encontrar e corrigir vulnerabilidades nas dependências de aplicativos

Para encontrar e corrigir dependências de aplicativos, você pode usar [a verificação de dependência do OWASP](https://owasp.org/www-project-dependency-check/).

Segurança é uma aplicação semelhante que verifica dependências. Você pode encontrá-lo no [GitHub](https://github.com/pyupio/safety). Verificações de segurança para vulnerabilidades encontradas em bancos de dados de vulnerabilidades bem conhecidos.

![Segurança](./media/secure-web-app/pysafety.png)

*Segurança*

## <a name="next-steps"></a>Próximas etapas

Os seguintes artigos podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Design](secure-design.md)
- [Desenvolver](secure-develop.md)
- [Implantar](secure-deploy.md)
