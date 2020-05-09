---
title: O consórcio de malha de hiperrazãos no serviço de kubernetes do Azure (AKS)
description: Como implantar e configurar a rede do consórcio do Fabric do Microsoft Azure no serviço kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: da4ec99f1b9d73ab67a2312094feaa1a89aee394
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980211"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>O consórcio de malha de hiperrazãos no serviço de kubernetes do Azure (AKS)

Você pode usar a HLF (malha do Microsoft) no modelo AKS (serviço kubernetes do Azure) para implantar e configurar uma rede do consórcio de malha de multirazão no Azure.

Depois de ler este artigo, você irá:

- Obtenha o conhecimento prático da malha de hiperrazãor e os vários componentes que formam os blocos de construção da rede blockchain de malha do
- Saiba como implantar e configurar um consórcio de malha de multirazão no serviço kubernetes do Azure para seus cenários de produção.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arquitetura do consórcio de malha de multirazãor

Para criar uma rede de malha de multirazão no Azure, você precisa implantar o serviço de pedidos e a organização com nós pares. Os diferentes componentes fundamentais que são criados como parte da implantação do modelo são:

- **Nós do solicitante**: um nó que é responsável pela ordenação da transação no razão. Junto com outros nós, os nós ordenados formam o serviço de pedidos da rede de malha de terceiros.

- **Nós de mesmo nível**: um nó que hospeda principalmente razões e contratos inteligentes, esses elementos fundamentais da rede.

- **AC de malha**: a AC de malha é a autoridade de certificação (CA) para a malha de hiperrazões. A AC de malha permite que você inicialize e inicie o processo do servidor que hospeda a autoridade de certificação. Ele permite que você gerencie identidades e certificados. Cada cluster AKS implantado como parte do modelo terá um pod de AC de malha por padrão.

- **CouchDB ou LevelDB**: o banco de dados de estado mundial para os nós de par pode ser armazenado em LevelDB ou CouchDB. LevelDB é o banco de dados de estado padrão incorporado no nó de par e armazena o chaincode data como pares chave-valor simples e oferece suporte somente a consultas de chave, intervalo de chaves e chave composta. CouchDB é um banco de dados de estado alternativo opcional que dá suporte a consultas avançadas quando os valores de dados chaincode são modelados como JSON.

O modelo na implantação gira vários recursos do Azure em sua assinatura. Os diferentes recursos do Azure implantados são:

- **Cluster AKs**: cluster kubernetes do Azure configurado de acordo com os parâmetros de entrada fornecidos pelo cliente. O cluster AKS tem vários pods configurados para executar os componentes de rede da malha do limiar. Os pods diferentes criados são:

  - **Ferramentas de malha**: a ferramenta de malha é responsável por configurar os componentes de malha do limiar.
  - **Solicitante/peer pods**: os nós da rede HLF.
  - **Proxy**: um pod de proxy NGNIX por meio do qual os aplicativos cliente podem interagir com o cluster AKs.
  - **AC de malha**: o Pod que executa a AC de malha.
- **PostgreSQL**: uma instância do PostgreSQL é implantada para manter as identidades da AC da malha.

- **Cofre de chaves do Azure**: uma instância do cofre de chaves é implantada para salvar as credenciais de AC de malha e os certificados raiz fornecidos pelo cliente, que é usado no caso de repetição de implantação de modelo, para lidar com a mecânica do modelo.
- **Disco gerenciado do Azure**: o disco gerenciado do Azure é para armazenamento persistente para o banco de dados de estado mundial do nó de par e do razão.
- **IP público**: um ponto de extremidade IP público do cluster AKs implantado para fazer a interface com o cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuração de rede de Blockchain de malha de multirazão

Para começar, você precisa de uma assinatura do Azure que possa suportar a implantação de várias máquinas virtuais e contas de armazenamento padrão. Se você não tiver uma assinatura do Azure, poderá [ criar uma conta gratuita do Azure ](https://azure.microsoft.com/free/).

Configure a rede Blockchain de malha do multirazão usando as seguintes etapas:

- [Implantar a organização do solicitante/par](#deploy-the-ordererpeer-organization)
- [Criar o consórcio](#build-the-consortium)

## <a name="deploy-the-ordererpeer-organization"></a>Implantar a organização do solicitante/par

Para começar a usar a implantação de componentes de rede do HLF, navegue até a [portal do Azure](https://portal.azure.com). Selecione **criar um recurso > Blockchain** > Pesquisar por **malha de Hiperrazões no serviço kubernetes do Azure**.

1. Selecione **criar** para iniciar a implantação do modelo. O **serviço criar malha de kubernetes no Azure** é exibido.

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Insira os detalhes do projeto na página **noções básicas** .

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Insira os seguintes detalhes:
    - **Assinatura**: escolha o nome da assinatura em que você deseja implantar os componentes de rede do HLF.
    - **Grupo de recursos**: Crie um novo grupo de recursos ou escolha um grupo de recursos vazio existente, o grupo de recursos manterá todos os recursos implantados como parte do modelo.
    - **Região**: escolha a região do Azure em que você deseja implantar o cluster kubernetes do Azure para os componentes do HLF. O modelo está disponível em todas as regiões em que AKS está disponível, certifique-se de escolher uma região em que sua assinatura não esteja atingindo o limite de cota da máquina virtual (VM).
    - **Prefixo do recurso**: prefixo para a nomenclatura de recursos que são implantados. O prefixo do recurso deve ter menos de seis caracteres e a combinação de caracteres deve incluir números e letras.
4. Selecione a guia **configurações de malha** para definir os componentes de rede HLF que serão implantados.

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Insira os seguintes detalhes:
    - **Nome da organização**: o nome da organização de malha, que é necessário para várias operações de plano de dados. O nome da organização deve ser exclusivo por implantação.
    - **Componente de rede de malha**: escolha o serviço de pedidos ou nós de mesmo nível com base no componente de rede Blockchain que você deseja configurar.
    - **Número de nós** – os dois tipos de nós a seguir:
        - Serviço de pedidos – selecione o número de nós para fornecer a tolerância a falhas para a rede. Somente 3, 5 e 7 são a contagem de nós do solicitante com suporte.
        - Nós de par-você pode escolher 1-10 nós com base em seu requisito.
    - **Banco de dados de estado mundial do nó par**: escolha entre LevelDB e CoucbDB. Esse campo é exibido quando o usuário escolhe o nó par na lista suspensa componente de rede de malha.
    - **Nome de usuário da malha**: Insira o nome de usuário que é usado para a autenticação da AC da malha.
    - **Senha da AC de malha**: Insira a senha para autenticação de AC de malha.
    - **Confirmar senha**: Confirme a senha da AC da malha.
    - **Certificados**: se você quiser usar seus próprios certificados raiz para inicializar a AC de malha, escolha carregar certificado raiz para a opção de AC de malha, senão, por padrão, a AC de malha cria certificados autoassinados.
    - **Certificado raiz**: carregue o certificado raiz (chave pública) com o qual a AC da malha precisa ser inicializada. Há suporte para certificados de formato. PEM, os certificados devem ser válidos no fuso horário UTC.
    - **Chave privada do certificado raiz**: carregue a chave privada do certificado raiz. Se você tiver um certificado. PEM, que tem a chave pública e privada combinada, carregue-o aqui também.


6. Selecione a guia **configurações de cluster do AKS** para definir a configuração do cluster kubernetes do Azure que é a infraestrutura subjacente na qual os componentes da rede de malha serão configurados.

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Insira os seguintes detalhes:
    - **Nome do cluster kubernetes**: o nome do cluster AKs que é criado. Este campo é preenchido previamente com base no prefixo de recurso fornecido, você pode alterar se necessário.
    - **Versão do kubernetes**: a versão do kubernetes que será implantada no cluster. Com base na região selecionada na guia **noções básicas** , as versões com suporte disponíveis podem ser alteradas.
    - **Prefixo DNS**: prefixo de nome DNS (sistema de nomes de domínio) para o cluster AKs. Você usará o DNS para se conectar à API do kubernetes ao gerenciar contêineres depois de criar o cluster.
    - **Tamanho do nó**: o tamanho do nó kubernetes, você pode escolher na lista de SKUs (unidades de manutenção de estoque) da VM disponíveis no Azure. Para obter um desempenho ideal, recomendamos o Standard DS3 v2.
    - **Contagem de nós**: a contagem do número de nós kubernetes a serem implantados no cluster. É recomendável manter essa contagem de nós pelo menos igual ou mais do que o número de nós HLF especificados nas configurações de malha.
    - **ID do cliente da entidade de serviço**: Insira a ID do cliente de uma entidade de serviço existente ou crie uma nova, que é necessária para a autenticação AKs. Consulte as etapas para [criar uma entidade de serviço](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Segredo do cliente da entidade de serviço**: Insira o segredo do cliente da entidade de serviço fornecida na ID do cliente da entidade de serviço.
    - **Confirmar segredo do cliente**: Confirme o segredo do cliente fornecido no segredo do cliente da entidade de serviço.
    - **Habilitar o monitoramento de contêiner**: escolha habilitar o monitoramento de AKs, que permite que os logs de AKs enviem por push para o espaço de trabalho de log Analytics especificado.
    - **Log Analytics espaço de trabalho**: o espaço de trabalho do log Analytics será preenchido com o espaço de trabalho padrão que é criado se o monitoramento estiver habilitado.

8. Depois de fornecer todos os detalhes acima, selecione a guia **revisar e criar** . A revisão e a criação dispara a validação para os valores fornecidos.
9. Depois que a validação for aprovada, você poderá selecionar **criar**.
A implantação geralmente leva 10-12 minutos, pode variar dependendo do tamanho e do número de nós AKS especificados.
10. Após a implantação bem-sucedida, você será notificado por meio das notificações do Azure no canto superior direito.
11. Selecione **ir para o grupo de recursos** para verificar todos os recursos criados como parte da implantação do modelo. Todos os nomes de recursos serão iniciados com o prefixo fornecido na configuração de **noções básicas** .

## <a name="build-the-consortium"></a>Criar o consórcio

Para criar o blockchain Consortium após implantar o serviço de pedidos e os nós de mesmo nível, você precisa executar as etapas a seguir em sequência. Azhlf (script do Azure HLF), que ajuda você a configurar o consórcio, a criação de operações de canal e chaincode.

> [!NOTE]
> Há uma atualização no script, essa atualização é para fornecer mais funcionalidade com o script HLF do Azure. Se você quiser se referir ao script antigo, [Consulte aqui](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Esse script é compatível com a malha de hiperrazãos no modelo do serviço kubernetes do Azure versão 2.0.0 e superior. Para verificar a versão da implantação, siga as etapas em [solucionar problemas](#troubleshoot).

> [!NOTE]
> O script do Azure HLF (azhlf) fornecido é para ajudar apenas com cenários de demonstração/DevTest. O canal e o consórcio criados por esse script têm políticas básicas de HLF para simplificar o cenário de demonstração/DevTest. Para a configuração de produção, é recomendável atualizar as políticas de HLF do canal/consórcio de acordo com as necessidades de conformidade da sua organização usando as APIs nativas do HLF.


Todos os comandos para executar o script HLF do Azure podem ser executados por meio da linha de comando do Azure bash. Interface (CLI). Você pode fazer logon na versão da Web do shell do Azure por meio de  ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) no canto superior direito do portal do Azure. No prompt de comando, digite bash e insira para mudar para a CLI do bash.

Consulte o [shell do Azure](https://docs.microsoft.com/azure/cloud-shell/overview) para obter mais informações.

![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


A imagem a seguir mostra o processo passo a passo para criar o Consórcio entre uma organização do solicitante e uma organização de mesmo nível. Comandos detalhados para executar essas etapas são capturados nas seções a seguir.

![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Siga os comandos abaixo para a configuração inicial do aplicativo cliente: 

1.  [Baixar arquivos do aplicativo cliente](#download-client-application-files)
2.  [Variáveis de ambiente de instalação](#setup-environment-variables)
3.  [Importar perfil de conexão da organização, usuário administrador e MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

Depois de concluir a configuração inicial, você pode usar o aplicativo cliente para obter as operações abaixo:  

- [Comandos de gerenciamento de canal](#channel-management-commands)
- [Comandos de gerenciamento do consórcio](#consortium-management-commands)
- [Comandos de gerenciamento do Chaincode](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Baixar arquivos do aplicativo cliente

A primeira configuração é baixar os arquivos do aplicativo cliente. Execute o comando abaixo para baixar todos os arquivos e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Esses comandos clonarão o código do aplicativo cliente do Azure HLF do repositório GitHub público, seguido do carregamento de todos os pacotes NPM dependentes. Após a execução bem-sucedida do comando, você poderá ver uma pasta node_modules no diretório atual. Todos os pacotes necessários são carregados na pasta node_modules.


### <a name="setup-environment-variables"></a>Configurar variáveis de ambiente

> [!NOTE]
> Todas as variáveis ambientais seguem a Convenção de nomenclatura de recursos do Azure.


**Definir as variáveis de ambiente abaixo para o cliente da organização do solicitante**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Definir as variáveis de ambiente abaixo para o cliente de organização par**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> Com base no número de pares de organizações em seu Consórcio, talvez seja necessário repetir os comandos de par e definir a variável de ambiente de acordo.

**Definir as variáveis de ambiente abaixo para configurar a conta de armazenamento do Azure**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Siga as etapas abaixo para a criação da conta de armazenamento do Azure. Se você já tiver a conta de armazenamento do Azure criada, ignore estas etapas

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Siga as etapas abaixo para uma criação de compartilhamento de arquivos na conta de armazenamento do Azure. Se você já tiver um compartilhamento de arquivos criado, ignore estas etapas

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Siga as etapas abaixo para gerar uma cadeia de conexão de compartilhamento de arquivos do Azure

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Importar perfil de conexão da organização, identidade do usuário administrador e MSP

Emita os comandos abaixo para buscar o perfil de conexão da organização, a identidade do usuário administrador e o MSP do cluster kubernetes do Azure e armazenar essas identidades no armazenamento local do aplicativo cliente, ou seja, no diretório "azhlfTool/Stores".

Para a organização do solicitante:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Para a organização par:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Comandos de gerenciamento de canal

> [!NOTE]
> Antes de começar com qualquer operação de canal, verifique se a configuração inicial do aplicativo cliente está concluída.  

Estes são os dois comandos de gerenciamento de canal:

1. [Comando Criar canal](#create-channel-command)
2. [Definindo o comando de pares de âncora](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Comando Criar canal

No cliente da organização do solicitante, emita o comando para criar um novo canal. Este comando criará um canal com apenas a organização do solicitante nele.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>Definindo o comando de pares de âncora
No cliente da organização par, emita o comando a seguir para definir os pares de âncora para a organização par no canal especificado.

>[!NOTE]
> Antes de executar esse comando, verifique se a organização par foi adicionada ao canal usando comandos de gerenciamento do consórcio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`é uma lista separada por espaços de nós pares a serem definidos como um par âncora. Por exemplo,

  - Defina `<anchorPeersList>` como "ponto1" se desejar definir somente o nó do ponto1 como um par âncora.
  - Defina `<anchorPeersList>` como "ponto1" "Ponto3" se você quiser definir o ponto1 e o nó Ponto3 como um par âncora.

### <a name="consortium-management-commands"></a>Comandos de gerenciamento do consórcio

>[!NOTE]
> Antes de começar com qualquer operação de consórcio, verifique se a configuração inicial do aplicativo cliente está concluída.  

Execute os comandos abaixo na ordem determinada para adicionar uma organização de mesmo nível em um canal e consórcio
1.  Do cliente de organização par, carregue a organização par MSP no armazenamento do Azure

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  No cliente da organização do solicitante, baixe a organização par MSP do armazenamento do Azure e, em seguida, emita o comando para adicionar a organização par no canal/consórcio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  No cliente da organização do solicitante, carregue o perfil de conexão do solicitante no armazenamento do Azure para que a organização par possa se conectar a nós do solicitante usando este perfil de conexão

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Em cliente da organização par, baixe o perfil de conexão do solicitante do armazenamento do Azure e, em seguida, emita o comando para adicionar nós de mesmo nível no canal

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Da mesma forma, para adicionar mais organizações pares no canal, atualize as variáveis de ambiente par de acordo com a organização par necessária e execute as etapas de 1 a 4.


### <a name="chaincode-management-commands"></a>Comandos de gerenciamento do Chaincode

>[!NOTE]
> Antes de começar com qualquer operação chaincode, verifique se a configuração inicial do aplicativo cliente está concluída.  

**Definir as variáveis de ambiente específicas do chaincode abaixo**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

As operações de chaincode abaixo podem ser executadas:  

- [Instalar o chaincode](#install-chaincode)  
- [Criar instância de chaincode](#instantiate-chaincode)  
- [Invocar chaincode](#invoke-chaincode)
- [Chaincode de consulta](#query-chaincode)


### <a name="install-chaincode"></a>Instalar o chaincode  

Execute o comando abaixo para instalar o chaincode na organização par.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Ele instalará o chaincode em todos os nós pares da organização par definida na variável de ambiente ORGNAME. Se houver duas ou mais organizações pares em seu canal e você quiser instalar o chaincode em todos eles, execute esse comando separadamente para cada organização de mesmo nível.  

Siga as etapas:  

1.  Defina `ORGNAME` e `USER_IDENTITY` como por peerOrg1 e o `./azhlf chaincode install` comando Issue.  
2.  Defina `ORGNAME` e `USER_IDENTITY` como por peerOrg2 e o `./azhlf chaincode install` comando Issue.  

### <a name="instantiate-chaincode"></a>Criar instância de chaincode  

No aplicativo de cliente de mesmo nível, execute o comando abaixo para criar uma instância de chaincode no canal.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Passe o nome da função de instanciação e a lista de `<instantiateFunc>` argumentos `<instantiateFuncArgs>` separados por espaço em e, respectivamente. Por exemplo, em chaincode_example02. go chaincode, para instanciar o chaincode `<instantiateFunc>` definido `init`como `<instantiateFuncArgs>` e para "a" "2000" "b" "1000".

> [!NOTE]
> Execute o comando de uma vez em qualquer organização de mesmo nível no canal. Depois que a transação for enviada com êxito para o solicitante, o solicitante distribuirá essa transação para todas as organizações pares no canal. Portanto, o chaincode é instanciado em todos os nós de mesmo nível em todas as organizações pares no canal.  


### <a name="invoke-chaincode"></a>Invocar chaincode  

Em cliente da organização par, execute o comando abaixo para invocar a função chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passe chamar o nome da função e a lista de argumentos `<invokeFunction>` separados `<invokeFuncArgs>` por espaço em e, respectivamente. Continuando com o exemplo de chaincode_example02. go chaincode, para executar a `<invokeFunction>` operação `invoke` Invoke `<invokeFuncArgs>` definida como e como "a" "b" "10".  

>[!NOTE]
> Execute o comando de uma vez em qualquer organização de mesmo nível no canal. Depois que a transação for enviada com êxito para o solicitante, o solicitante distribuirá essa transação para todas as organizações pares no canal. Portanto, o estado mundial é atualizado em todos os nós pares de todas as organizações pares no canal.  


### <a name="query-chaincode"></a>Chaincode de consulta  

Execute o comando abaixo para consultar o chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Passe o nome da função de consulta e a lista de `<queryFunction>` argumentos `<queryFuncArgs>` separados por espaço em e, respectivamente. Novamente, levando chaincode_example02. go chaincode como referência, para consultar o valor de "a" no estado do mundo `<queryFunction>` definido `query` como `<queryArgs>` e como "a".  

## <a name="troubleshoot"></a>Solucionar problemas

**Para verificar a versão do modelo em execução**

Execute os comandos abaixo para localizar a versão de sua implantação de modelo.

Defina as variáveis de ambiente abaixo de acordo com o grupo de recursos em que o modelo foi implantado.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Execute o comando abaixo para imprimir a versão do modelo
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```
