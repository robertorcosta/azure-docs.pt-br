---
title: Consórcio Hyperledger Fabric no Azure Kubernetes Service (AKS)
description: Como implantar e configurar a rede de consórcios Hyperledger Fabric no Serviço Azure Kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476433"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Consórcio Hyperledger Fabric no Azure Kubernetes Service (AKS)

Você pode usar o modelo Hyperledger Fabric (HLF) no Azure Kubernetes Service (AKS) para implantar e configurar uma rede de consórcio Hyperledger Fabric no Azure.

Depois de ler este artigo, você irá:

- Obtenha conhecimento de trabalho da Hyperledger Fabric e dos vários componentes que formam os blocos de construção da rede blockchain Hyperledger Fabric.
- Saiba como implantar e configurar um consórcio Hyperledger Fabric no Azure Kubernetes Service para seus cenários de produção.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arquitetura do Hyperledger Fabric Consortium

Para construir a rede Hyperledger Fabric no Azure, você precisa implantar serviço de pedidos e organização com nós de pares. Os diferentes componentes fundamentais que são criados como parte da implantação do modelo são:

- **Nó sumido**: Um nó responsável pela ordenação de transações no livro razão. Juntamente com outros nódulos, os nós ordenados formam o serviço de encomenda da rede Hyperledger Fabric.

- **Os pares**: Um nó que hospeda principalmente livros contábeis e contratos inteligentes, esses elementos fundamentais da rede.

- **Fabric CA**: Fabric CA é a Autoridade de Certificado (CA) para malha Hyperledger. O Fabric CA permite que você inicie e inicie o processo do servidor que hospeda a autoridade do certificado. Ele permite que você gerencie identidades e certificados. Cada cluster AKS implantado como parte do modelo terá um pod Fabric CA por padrão.

- **CouchDB ou LevelDB**: O banco de dados de estado mundial para os nós pares pode ser armazenado no LevelDB ou no CouchDB. LevelDB é o banco de dados de estado padrão incorporado no nó de pares e armazena dados chaincode como pares simples de valor de chave e suporta apenas consultas de tecla, intervalo de chaves e tecidas compostas. CouchDB é um banco de dados de estado alternativo opcional que suporta consultas ricas quando os valores de dados chaincode são modelados como JSON.

O modelo de implantação gira vários recursos do Azure em sua assinatura. Os diferentes recursos do Azure implantados são:

- **Cluster AKS**: Cluster Azure Kubernetes configurado de acordo com os parâmetros de entrada fornecidos pelo cliente. O cluster AKS tem vários pods configurados para executar os componentes da rede Hyperledger Fabric. Os diferentes pods criados são:

  - **Ferramentas de tecido**: A ferramenta de tecido é responsável pela configuração dos componentes da malha Hyperledger.
  - **Ordenador/pods de pares:** Os nós da rede HLF.
  - **Proxy**: Um proxy do NGNIX através do qual os aplicativos clientes podem interagir com o cluster AKS.
  - **Malha CA**: A cápsula que executa o Fabric CA.
- **PostgreSQL**: Uma instância do PostgreSQL é implantada para manter as identidades da CA de malha.

- **Cofre Azure Key**: Uma instância de cofre chave é implantada para salvar as credenciais da Fabric CA e os certificados raiz fornecidos pelo cliente, que é usado em caso de repetição de implantação de modelo, isto é para lidar com a mecânica do modelo.
- **Disco gerenciado do Azure**: O disco Azure Managed é para armazenamento persistente para banco de dados de estado do mundo do nó de razão e pares.
- **IP público**: Um ponto final IP público do cluster AKS implantado para interligação com o cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuração da rede Hyperledger Fabric Blockchain

Para começar, você precisa de uma assinatura do Azure que possa suportar a implantação de várias máquinas virtuais e contas de armazenamento padrão. Se você não tiver uma assinatura do Azure, poderá [ criar uma conta gratuita do Azure ](https://azure.microsoft.com/free/).

Configurar rede Hyperledger Fabric Blockchain usando as seguintes etapas:

- [Implantar a organização do pedidor/peer](#deploy-the-ordererpeer-organization)
- [Construa o consórcio](#build-the-consortium)
- [Executar operações nativas de HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Implantar a organização do pedidor/peer

Para começar com a implantação dos componentes da rede HLF, navegue até o [portal Azure](https://portal.azure.com). Selecione **Criar um recurso > blockchain** > procurar por malha **Hyperledger no Azure Kubernetes Service**.

1. Selecione **criar** para iniciar a implantação do modelo. O **Create Hyperledger Fabric no Azure Kubernetes Service** é exibido.

    ![Malha Hyperledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Digite os **detalhes** do projeto na página Básico.

    ![Malha Hyperledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Insira os seguintes detalhes:
    - **Assinatura**: Escolha o nome da assinatura onde deseja implantar os componentes da rede HLF.
    - **Grupo de recursos**: Crie um novo grupo de recursos ou escolha um grupo de recursos vazio existente, o grupo de recursos manterá todos os recursos implantados como parte do modelo.
    - **Região**: Escolha a região azure onde deseja implantar o cluster Azure Kubernetes para os componentes HLF. O modelo está disponível em todas as regiões onde o AKS está disponível Certifique-se de escolher uma região onde sua assinatura não está atingindo o limite de cotas da Máquina Virtual (VM).
    - **Prefixo de**recursos : Prefixo para nomeação de recursos que são implantados. O prefixo de recurso deve ter menos de seis caracteres de comprimento e a combinação de caracteres deve incluir números e letras.
4. Selecione a guia **Configurações de** malha para definir os componentes de rede HLF que serão implantados.

    ![Malha Hyperledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Insira os seguintes detalhes:
    - **Nome da organização**: O nome da organização Fabric, que é necessária para várias operações de data plane. O nome da organização precisa ser único por implantação. 
    - **Componente de rede de malha**: Escolha serviço de pedidos ou nós peer com base no componente de rede Blockchain que deseja configurar.
    - **Número de nós** - Os dois tipos de nós:
        - Serviço de encomenda - selecione o número de nós para fornecer tolerância de falha à rede. Apenas 3,5 e 7 são a contagem de nós ordenados suportados.
        - Nós pares - você pode escolher 1-10 nodes com base na sua exigência.
    - **Banco de dados do estado do mundo do nó peer :** Escolha entre LevelDB e CoucbDB. Este campo é exibido quando o usuário escolhe o nó de ponto em componente de rede de malha suspenso.
    - **Nome de usuário da malha**: Digite o nome de usuário usado para a autenticação do Fabric CA.
    - **Senha da MALHA CA**: Digite a senha para autenticação de Fabric CA.
    - **Confirmar senha**: Confirme a senha do Fabric CA.
    - **Certificados**: Se você quiser usar seus próprios certificados raiz para inicializar o Fabric CA, escolha Enviar certificado raiz para a opção Fabric CA, senão, por padrão, a Fabric CA cria certificados auto-assinados.
    - **Certificado raiz**: Certificado raiz de upload (chave pública) com o qual a Fabric CA precisa ser inicializada. Os certificados do formato .pem são suportados, os certificados devem ser válidos no fuso horário UTC.
    - **Tecla privada do Certificado Raiz**: Carregue a chave privada do certificado raiz. Se você tem um certificado .pem, que tem chave pública e privada combinada, carregue-o aqui também.


6. Selecione a guia **Configurações do cluster AKS** para definir a configuração do cluster Azure Kubernetes, que é a infra-estrutura subjacente na qual os componentes da rede Fabric serão configurados.

    ![Malha Hyperledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Insira os seguintes detalhes:
    - **Nome do cluster Kubernetes**: O nome do cluster AKS que é criado. Este campo é pré-preenchido com base no prefixo de recurso fornecido, você pode alterar se necessário.
    - **Versão Kubernetes**: A versão dos Kubernetes que será implantada no cluster. Com base na região selecionada na guia Básico, as **versões** suportadas disponíveis podem mudar.
    - **Prefixo DNS**: Prefixo de nome de nome de domínio (DNS) para cluster AKS. Você usará o DNS para se conectar à API do Kubernetes ao gerenciar contêineres após a criação do cluster.
    - **Tamanho do nó**: O tamanho do nó Kubernetes, você pode escolher na lista de SKUs (Unidade de Manutenção de Estoque vm) disponível no Azure. Para um desempenho ideal, recomendamos o Standard DS3 v2.
    - **Contagem de**nó : A contagem do número de nódulos Kubernetes a serem implantados no cluster. Recomendamos manter essa contagem de nós pelo menos igual ou superior ao número de nós HLF especificados nas configurações de Malha.
    - **ID do cliente principal do serviço**: Digite o ID do cliente de um principal de serviço existente ou crie um novo, que é necessário para a autenticação AKS. Veja, passos para [criar o diretor de serviço.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Serviço principal cliente secreto**: Digite o segredo do cliente do principal serviço fornecido no serviço principal iD do cliente.
    - **Confirme o segredo**do cliente : Confirme o segredo do cliente fornecido no principal segredo do cliente do serviço.
    - **Habilitar o monitoramento de contêineres**: Escolha ativar o monitoramento aks, o que permite que os logs AKS empurrem para o espaço de trabalho do Log Analytics especificado.
    - **Espaço de trabalho do Log Analytics**: O espaço de trabalho do Log Analytics será preenchido com o espaço de trabalho padrão criado se o monitoramento estiver ativado.

8. Depois de fornecer todos os detalhes acima, selecione **Revisar e criar** guia. A revisão e a criação desencadeiam a validação dos valores fornecidos.
9. Uma vez que a validação passe, você pode selecionar **criar**.
A implantação geralmente leva de 10 a 12 minutos, podendo variar dependendo do tamanho e número de nomes AKS especificados.
10. Após a implantação bem-sucedida, você é notificado através de notificações do Azure no canto superior direito.
11. Selecione **Ir ao grupo de recursos** para verificar todos os recursos criados como parte da implantação do modelo. Todos os nomes dos recursos começarão com o prefixo fornecido na configuração **Basics.**

## <a name="build-the-consortium"></a>Construa o consórcio

Para construir o post de consórcio blockchain implantando o serviço de pedidos e os nós de pares, você precisa realizar as etapas abaixo em seqüência. **Construa o** script da rede (byn.sh), que ajuda você a configurar o consórcio, criar canal e instalar chaincode.

> [!NOTE]
> O script Build Your Network (byn) fornecido é estritamente usado para cenários de demonstração/devteste. Para a configuração de grau de produção, recomendamos o uso das APIs nativas hlf.

Todos os comandos para executar o script byn podem ser executados através da Interface de Linha de Comando (CLI) do Azure Bash. Você pode fazer login na versão web shell do Azure através ![Malha Hyperledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) opção no canto superior direito do portal Azure. No prompt de comando, digite bash e digite para trocar para bash CLI.

Consulte [a shell do Azure](https://docs.microsoft.com/azure/cloud-shell/overview) para obter mais informações.

![Malha Hyperledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Baixe byn.sh e arquivo fabric-admin.yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Definir abaixo variáveis de ambiente no shell Azure CLI Bash**:

Defina informações de canal e informações da organização do ordenador

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Definir informações da organização de pares

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Crie um compartilhamento de arquivos do Azure para compartilhar vários certificados públicos entre organizações peer e orderer.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Comandos de Gerenciamento de Canais**

Vá para o cluster aks organization do pedido e emita comando para criar um novo canal

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Comandos de Gestão de Consórcios**

Execute abaixo os comandos na ordem dada para adicionar uma organização de pares em um canal e consórcio.

1. Vá para o Cluster AKS da Organização de Pares e carregue seu MSP (Member Service Provide, serviço de membro) em um armazenamento de arquivos Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Vá para o cluster AKS Organization e adicione a organização por pares em canal e consórcio.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Volte para a organização de pares e emita comando para juntar os pares no canal.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Da mesma forma, para adicionar mais organizações de pares no canal, atualize as variáveis do ambiente AKS de acordo com a organização de pares necessária e execute as etapas 1 a 3.

**Comandos de gerenciamento de chaincode**

Execute o comando abaixo para executar a operação relacionada ao chaincode. Esses comandos executam todas as operações em um código de cadeia de demonstração. Este código de momo tem duas variáveis "a" e "b". Na instanciação do chaincode, "a" é inicializado com 1000 e "b" é inicializado com 2000. Em cada invocação do chaincode, 10 unidades são transferidas de "a" para "b". A operação de consulta no chaincode mostra o estado mundial da variável "a".

Execute os seguintes comandos executados no cluster AKS da organização de pares.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Comandos de operação Chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Executar operações nativas de HLF

Para ajudar os clientes a começar a executar comandos nativos hyperledger na rede HLF no AKS. A aplicação da amostra é fornecida que usa tecido NodeJS SDK para executar as operações HLF. Os comandos são fornecidos para criar uma nova identidade de usuário e instalar seu próprio chaincode.

### <a name="before-you-begin"></a>Antes de começar

Siga os comandos abaixo para a configuração inicial do aplicativo:

- Baixar arquivos de aplicativos
- Gerar perfil de conexão e perfil de admin
- Importar identidade do usuário de admin

Após concluir a configuração inicial, você pode usar o SDK para alcançar as operações abaixo:

- Geração de identidade do usuário
- Operações chaincode

Os comandos acima mencionados podem ser executados a partir do Azure Cloud Shell.

### <a name="download-application-files"></a>Baixar arquivos de aplicativos

A primeira configuração para executar o aplicativo é baixar todos os arquivos do aplicativo em uma pasta.

**Criar pasta de aplicativo e entrar na pasta:**

```bash
mkdir app
cd app
```
Execute abaixo o comando para baixar todos os arquivos e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Este comando leva tempo para carregar todos os pacotes. Após a execução bem sucedida `node_modules` do comando, você pode ver uma pasta no diretório atual. Todos os pacotes necessários `node_modules` estão carregados na pasta.

### <a name="generate-connection-profile-and-admin-profile"></a>Gerar perfil de conexão e perfil de admin

Criar `profile` diretório dentro `app` da pasta

```bash
cd app
mkdir ./profile
```
Defina essas variáveis de ambiente no shell de nuvem do Azure

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Execute abaixo o comando para gerar perfil de conexão e perfil de admin da organização

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Ele criará perfil de conexão e admin `profile` `<orgname>-ccp.json` da `<orgname>-admin.json` organização na pasta de perfil com nome e, respectivamente.

Da mesma forma, gerar perfil de conexão e perfil de admin para cada ordenador e organização de pares.


### <a name="import-admin-user-identity"></a>Importar identidade do usuário de admin

O último passo é importar a identidade do usuário de admin da organização na carteira.

```bash
npm run importAdmin -- -o <orgName>

```
O comando acima executa importAdmin.js para importar a identidade do usuário do admin para a carteira. O script lê a identidade do administrador do `<orgname>-admin.json` perfil de administrador e importa-a em carteira para execução de operações HLF.

Os scripts usam a carteira do sistema de arquivos para armazenar as identidades. Ele cria uma carteira de acordo com o caminho especificado no campo ".wallet" no perfil de conexão. Por padrão, o campo ".wallet" é inicializado com `<orgname>`, o que significa que uma pasta nomeada `<orgname>` é criada no diretório atual para armazenar as identidades. Se você quiser criar carteira em algum outro caminho, modifique o campo ".wallet" no perfil de conexão antes de executar o usuário de admin de inscrição e quaisquer outras operações HLF.

Da mesma forma, importe a identidade do usuário do admin para cada organização.

Consulte a ajuda de comando para obter mais detalhes sobre os argumentos passados no comando.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Geração de identidade do usuário

Execute abaixo os comandos na ordem dada para gerar novas identidades de usuário para a organização HLF.

> [!NOTE]
> Antes de começar com as etapas de geração de identidade do usuário, certifique-se de que a configuração inicial do aplicativo seja feita.

Definir abaixo variáveis de ambiente no shell de nuvem azul

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registre-se e inscreva-se novo usuário

Para registrar e cadastrar o novo usuário, execute o comando abaixo que executa registerUser.js. Ele salva a identidade de usuário gerada na carteira.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> A identidade do usuário do admin é usada para emitir o comando register para o novo usuário. Portanto, é obrigatório ter a identidade do usuário administrador na carteira antes de executar este comando. Caso contrário, este comando falhará.

Indique ajuda de comando para obter mais detalhes sobre os argumentos aprovados no comando

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operações chaincode


> [!NOTE]
> Antes de começar com qualquer operação de chaincode, certifique-se de que a configuração inicial do aplicativo seja feita.

Definir abaixo variáveis de ambiente específicas do chaincode no shell do Azure Cloud:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

As operações abaixo do chaincode podem ser realizadas:

- Instalar chaincode
- Código de corrente instanciado
- Invocar chaincode
- Código de cadeia de consulta

### <a name="install-chaincode"></a>Instalar chaincode

Execute o comando abaixo para instalar chaincode na organização de pares.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Ele instalará chaincode em todos os nós de `ORGNAME` pares da organização definidos na variável ambiente. Se houver duas ou mais organizações de pares em seu canal e você quiser instalar chaincode em todas elas, execute os comandos separadamente para cada organização de pares.

Siga os passos:

- Definir `ORGNAME` `<peerOrg1Name>` e `installCC` emitir comando.
- Definir `ORGNAME` `<peerOrg2Name>` e `installCC` emitir comando.

  Execute-o para cada organização de pares.

Consulte a ajuda de comando para obter mais detalhes sobre os argumentos passados no comando.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Código de corrente instanciado

Execute abaixo o comando para instanciar o código de corrente no peer.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Passe o nome da função de instanciação `<instantiateFunc>` e `<instantiateFuncArgs>` a lista separada de comuma de argumentos dentro e respectivamente. Por exemplo, no [fabrcar chaincode,](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)para `<instantiateFunc>` instanciar o código de corrente definido para `"Init"` e `<instantiateFuncArgs>` para esvaziar a seqüência de cordas `""`.

> [!NOTE]
> Execute o comando por uma vez a partir de qualquer organização de um par no canal.
> Uma vez que a transação é submetida com sucesso ao pedidor, o ordenador distribui essa transação para todas as organizações pares do canal. Assim, o chaincode é instanciado em todos os nós de pares em todas as organizações de pares do canal.

Indique ajuda de comando para obter mais detalhes sobre os argumentos aprovados no comando

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Invocar chaincode

Execute o comando abaixo para invocar a função chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Passe o nome da função e a lista `<invokeFunction>` `<invokeFuncArgs>` separada de comuma de discussão dentro e respectivamente. Continuando com o exemplo fabcar chaincode, para `<invokeFunction>` `"initLedger"` invocar `<invokeFuncArgs>` `""`a função initLedger definida para e para .

> [!NOTE]
> Execute o comando por uma vez a partir de qualquer organização de um par no canal.
> Uma vez que a transação é submetida com sucesso ao pedidor, o ordenador distribui essa transação para todas as organizações pares do canal. Assim, o Estado mundial é atualizado em todos os nós pares de todas as organizações pares do canal.

Indique ajuda de comando para obter mais detalhes sobre os argumentos aprovados no comando

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Código de cadeia de consulta

Execute abaixo o comando para consultar chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Passar nome da função de consulta e comma lista separada de argumentos dentro `<queryFunction>` e `<queryFuncArgs>` respectivamente. Mais uma `fabcar` vez, tomando o chaincode como referência, `<queryFunction>` `"queryAllCars"` para `<queryArgs>` `""`consultar todos os carros do estado mundial definidos para e para .

Indique ajuda de comando para obter mais detalhes sobre os argumentos aprovados no comando

```bash
npm run queryCC -- -h

```
