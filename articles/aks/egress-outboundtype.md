---
title: Personalize rotas definidas pelo usuário (UDR) no Azure Kubernetes Service (AKS)
description: Saiba como definir uma rota de saída personalizada no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 30b7b6bae92221b268d40977f5b299e9b0b267b0
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637819"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Personalize a saída de cluster com uma rota definida pelo usuário (Visualização)

O egresso de um cluster AKS pode ser personalizado para se adequar a cenários específicos. Por padrão, o AKS provisionará um Balanceador de Carga Padrão SKU a ser configurado e usado para saída. No entanto, a configuração padrão pode não atender aos requisitos de todos os cenários se os IPs públicos forem proibidos ou forem necessários saltos adicionais para a saída.

Este artigo percorre como personalizar a rota de saída de um cluster para suportar cenários de rede personalizados, como aqueles que não permitem IPs públicos e exige que o cluster se site atrás de um aparelho virtual de rede (NVA).

> [!IMPORTANT]
> Os recursos de pré-visualização do AKS são de autoatendimento e são oferecidos em uma base opt-in. As visualizações são fornecidas *como está* e *disponível* e são excluídas do contrato de nível de serviço (SLA) e garantia limitada. As visualizações aks são parcialmente cobertas pelo suporte ao cliente em uma base *de esforço* melhor. Portanto, os recursos não são feitos para uso de produção. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte da AKS](support-policies.md)
> * [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="prerequisites"></a>Pré-requisitos
* Azure CLI versão 2.0.81 ou superior
* Azure CLI Preview versão de extensão 0.4.28 ou superior
* Versão aPI `2020-01-01` de ou maior

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Instale a mais recente extensão Azure CLI AKS Preview
Para definir o tipo de saída de um cluster, você precisa da versão de extensão Azure CLI AKS Preview 0.4.18 ou posterior. Instale a extensão Azure CLI AKS Preview usando o comando az extension add e, em seguida, verifique se há atualizações disponíveis usando o seguinte comando de atualização de extensão az:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Limitações
* Durante a `outboundType` visualização, só pode ser definido no tempo de criação de cluster e não pode ser atualizado posteriormente.
* Durante a `outboundType` visualização, os clusters AKS devem usar o Azure CNI. Kubenet é configurável, o uso requer associações manuais da tabela de rota para a sub-rede AKS.
* A `outboundType` configuração requer clusters `vm-set-type` `VirtualMachineScaleSets` AKS com um de e `load-balancer-sku` . `Standard`
* A `outboundType` configuração `UDR` para um valor de requer uma rota definida pelo usuário com conectividade de saída válida para o cluster.
* A `outboundType` definição de `UDR` um valor de implica que o IP de origem de entrada encaminhado para o balanceador de carga pode **não corresponder ao** endereço de destino de saída do cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Visão geral dos tipos de saída em AKS

Um cluster AKS pode ser `outboundType` personalizado com um balanceador de carga exclusivo ou roteamento definido pelo usuário.

> [!IMPORTANT]
> O tipo de saída impacta apenas o tráfego de saída do seu cluster. Consulte [a configuração de controladores de ingestão](ingress-basic.md) para obter mais informações.

### <a name="outbound-type-of-loadbalancer"></a>Tipo de saída de loadBalancer

Se `loadBalancer` estiver definido, o AKS completa automaticamente a configuração seguinte. O balanceador de carga é usado para saída através de um IP público atribuído a AKS. Um tipo de `loadBalancer` saída suporta serviços kubernetes do tipo, `loadBalancer`que esperam saída do balanceador de carga criado pelo provedor de recursos AKS.

A configuração seguinte é feita pela AKS.
   * Um endereço IP público é provisionado para saída de cluster.
   * O endereço IP público é atribuído ao recurso balanceador de carga.
   * As piscinas de backend para o balanceador de carga são configuradas para nós de agente no cluster.

Abaixo está uma topologia de rede implantada em clusters `outboundType` `loadBalancer`AKS por padrão, que usam um de .

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo de saída do usuárioDefiniu-se

> [!NOTE]
> O uso do tipo de saída é um cenário avançado de rede e requer uma configuração de rede adequada.

Se `userDefinedRouting` estiver definido, o AKS não configurará automaticamente os caminhos de saída. Espera-se que o **usuário seja**feito a seguir.

O cluster deve ser implantado em uma rede virtual existente com uma sub-rede configurada. Uma rota definida pelo usuário (UDR) válida deve existir na sub-rede com conectividade de saída.

O provedor de recursos AKS implantará um Balanceador de carga padrão (SLB). O balanceador de carga não está configurado com nenhuma regra e [não incorre em uma carga até que uma regra seja colocada](https://azure.microsoft.com/pricing/details/load-balancer/). O AKS **não** provisionará automaticamente um endereço IP público para o frontend SLB. O AKS **não** configurará automaticamente o pool de back-end do balanceador de carga.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Implantar um cluster com o tipo de saída de UDR e Firewall Azure

Para ilustrar a aplicação de um cluster com tipo de saída usando uma rota definida pelo usuário, um cluster pode ser configurado em uma rede virtual com um Firewall Azure.

![Topologia bloqueada](media/egress-outboundtype/outboundtype-udr.png)

* Ingress é forçada a fluir através de filtros de firewall
   * Uma sub-rede isolada contém um balanceador de carga interno para roteamento em nomes de agentes
   * Nós de agentes estão isolados em uma sub-rede dedicada
* As solicitações de saída começam a partir de nós de agente para o IP interno do Azure Firewall usando uma rota definida pelo usuário
   * As solicitações dos nós de agente AKS seguem um UDR que foi colocado na sub-rede em que o cluster AKS foi implantado.
   * O Azure Firewall sai da rede virtual de um frontend IP público
   * O acesso ao plano de controle AKS é protegido por um NSG, que habilitou o endereço IP frontend do firewall
   * O acesso à internet pública ou a outros serviços do Azure flui para e a partir do endereço IP frontend do firewall

### <a name="set-configuration-via-environment-variables"></a>Definir configuração através de variáveis de ambiente

Defina um conjunto de variáveis de ambiente a serem usadas em criações de recursos.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Em seguida, defina os IDs de assinatura.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Provisão uma rede virtual com três sub-redes separadas, uma para o cluster, uma para o firewall e outra para entrada de serviço.

![Topologia de rede vazia](media/egress-outboundtype/empty-network.png)

Crie um grupo de recursos para manter todos os recursos.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Crie duas redes virtuais para hospedar o cluster AKS e o Firewall Azure. Cada um terá sua própria sub-rede. Vamos começar com a rede AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Criar e configurar um Firewall Azure com um UDR

As regras de entrada e saída do Firewall do Azure devem ser configuradas. O principal objetivo do firewall é permitir que as organizações configurem regras de tráfego granulares dentro e fora do Cluster AKS.

![Firewall e UDR](media/egress-outboundtype/firewall-udr.png)

Crie um recurso IP público padrão do SKU que será usado como o endereço frontend do Azure Firewall.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registre a extensão cli de visualização para criar um Firewall Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

O endereço IP criado anteriormente agora pode ser atribuído ao frontend do firewall.
> [!NOTE]
> A configuração do endereço IP público no Firewall do Azure pode levar alguns minutos.
> 
> Se os erros forem recebidos repetidamente no comando abaixo, exclua o firewall e o IP público existentes e provisione o Firewall IP público e azure através do portal ao mesmo tempo.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Quando o comando anterior tiver sido bem sucedido, salve o endereço IP do firewall frontend para configuração posterior.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Crie um UDR com um salto para o Firewall Do Azure

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão do Azure, poderá criar uma tabela de rotas para fazer isso.

Crie uma tabela de rota vazia para ser associada a uma determinada sub-rede. A tabela de rotas definirá o próximo salto como o Firewall Azure criado acima. Cada sub-rede pode ter zero ou uma tabela de rotas associada a ela.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Consulte [a documentação da tabela de rotas de rede virtual](../virtual-network/virtual-networks-udr-overview.md#user-defined) sobre como você pode substituir as rotas padrão do sistema do Azure ou adicionar rotas adicionais à tabela de rotas de uma sub-rede.

## <a name="adding-network-firewall-rules"></a>Adicionando regras de firewall de rede

> [!WARNING]
> Abaixo mostra um exemplo de adicionar uma regra de firewall. Todos os pontos finais de saída definidos nos [pontos finais de saída necessários](egress.md) devem ser habilitados pelas regras de firewall de aplicativos para que os clusters AKS funcionem. Sem esses pontos finais ativados, seu cluster não pode operar.

Abaixo está um exemplo de uma regra de rede e aplicativo. Adicionamos uma regra de rede que permite qualquer protocolo, endereço de origem, endereço de destino e portas de destino. Também adicionamos uma regra de aplicação para **alguns** dos pontos finais exigidos pela AKS.

Em um cenário de produção, você só deve habilitar o acesso aos pontos finais necessários para sua aplicação e [aqueles definidos no egresso exigido aKS](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Consulte [a documentação do Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview) para saber mais sobre o serviço de Firewall do Azure.

## <a name="associate-the-route-table-to-aks"></a>Associar a tabela de rotas à AKS

Para associar o cluster ao firewall, a sub-rede dedicada para a sub-rede do cluster deve referenciar a tabela de rotacriada acima. A associação pode ser feita emitindo um comando para a rede virtual que segura o cluster e o firewall para atualizar a tabela de rota da sub-rede do cluster.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Implantar AKS com tipo de UDR de saída para a rede existente

Agora, um cluster AKS pode ser implantado na configuração de rede virtual existente. Para definir um tipo de saída de cluster para roteamento definido pelo usuário, uma sub-rede existente deve ser fornecida ao AKS.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Crie um diretor de serviço com acesso à provisão dentro da rede virtual existente

Um principal de serviço é usado pela AKS para criar recursos de cluster. O principal de serviço passado no momento da criação é usado para criar recursos AKS subjacentes, como VMs, Storage e Load Balancers usados pela AKS. Se for concedida poucas permissões, não será capaz de provisionar um Cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Agora substitua o `APPID` e `PASSWORD` abaixo com o aplicativo principal de serviço e a senha principal do serviço gerada automaticamente pela saída de comando anterior. Vamos referenciar o ID de recurso VNET para conceder as permissões ao diretor do serviço para que a AKS possa implantar recursos nele.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Implantar AKS

Finalmente, o cluster AKS pode ser implantado na sub-rede existente que dedicamos para o cluster. A sub-rede de destino a ser implantada `$SUBNETID`é definida com a variável ambiente, . Não definimos a `$SUBNETID` variável nas etapas anteriores. Para definir o valor do ID da sub-rede, você pode usar o seguinte comando:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Definiremos o tipo de saída para seguir o UDR que existe na sub-rede, permitindo que a AKS pule a configuração e o provisionamento de IP para o balanceador de carga que agora pode ser estritamente interno.

O recurso AKS para [faixas IP autorizadas para servidor API](api-server-authorized-ip-ranges.md) pode ser adicionado para limitar o acesso do servidor API apenas ao ponto final público do firewall. O recurso de faixas IP autorizadas é denotado no diagrama como o NSG que deve ser passado para acessar o plano de controle. Ao habilitar o recurso de intervalo IP autorizado para limitar o acesso ao servidor de API, suas ferramentas de desenvolvedor devem usar uma caixa de salto da rede virtual do firewall ou você deve adicionar todos os pontos finais do desenvolvedor à faixa de IP autorizada.

> [!TIP]
> Recursos adicionais podem ser adicionados à implantação do cluster, como (Private Cluster)[]. Ao usar faixas IP autorizadas, uma caixa de salto será necessária dentro da rede de cluster para acessar o servidor de API.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Habilite o acesso do desenvolvedor ao servidor de API

Devido à configuração de intervalos IP autorizados para o cluster, você deve adicionar seus endereços IP de ferramentas de desenvolvedor à lista de clusters AKS de intervalos IP aprovados para acessar o servidor api. Outra opção é configurar uma caixa de salto com a ferramenta necessária dentro de uma sub-rede separada na rede virtual do Firewall.

Adicione outro endereço IP às faixas aprovadas com o seguinte comando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Use o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para se conectar ao seu recém-criado cluster Kubernetes. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Configurar o balanceador de carga interna

A AKS implantou um balanceador de carga com o cluster que pode ser configurado como um [balanceador de carga interno](internal-lb.md).

Para criar um balanceador de carga interno, crie um manifesto de serviço chamado internal-lb.yaml com o tipo de serviço LoadBalancer e a anotação azure-load-balancer-internal, conforme mostrado no exemplo a seguir:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Implantar o balanceador de carga interna usando a aplicação de kubectl e especificar o nome do seu manifesto YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Implantar um serviço Kubernetes

Uma vez que o tipo de saída do cluster é definido como UDR, associando os nós do agente como o pool de backend para o balanceador de carga não é concluído automaticamente pela AKS na hora de criação do cluster. No entanto, a associação de pool backend é tratada pelo provedor de nuvem Kubernetes Azure quando o serviço Kubernetes é implantado.

Implante o aplicativo de votação Do Azure copiando o `example.yaml`inhame abaixo para um arquivo chamado .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Implantar o serviço executando:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Adicionar uma regra de DNAT ao Firewall Do Azure

Para configurar a conectividade de entrada, uma regra DNAT deve ser escrita no Firewall Do Azure. Para testar a conectividade ao nosso cluster, uma regra é definida para o endereço IP público do firewall frontend para encaminhar para o IP interno exposto pelo serviço interno.

O endereço de destino pode ser personalizado, pois é a porta no firewall a ser acessada. O endereço traduzido deve ser o endereço IP do balanceador de carga interno. A porta traduzida deve ser a porta exposta para o serviço Kubernetes.

Você precisará especificar o endereço IP interno atribuído ao balanceador de carga criado pelo serviço Kubernetes. Recupere o endereço executando:

```bash
kubectl get services
```

O endereço IP necessário será listado na coluna EXTERNAL-IP, semelhante ao seguinte.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Limpar os recursos

> [!NOTE]
> Ao excluir o serviço interno do Kubernetes, se o balanceador de carga interno não estiver mais em uso por qualquer serviço, o provedor de nuvem do Azure excluirá o balanceador de carga interno. Na próxima implantação de serviço, um balanceador de carga será implantado se nenhum for encontrado com a configuração solicitada.

Para limpar os recursos do Azure, exclua o grupo de recursos AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Validar a conectividade

Navegue até o endereço IP frontend do Azure Firewall em um navegador para validar a conectividade.

Você deve ver uma imagem do aplicativo de votação Azure.

## <a name="next-steps"></a>Próximas etapas

Consulte [a visão geral da UDR em rede do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Veja [como criar, alterar ou excluir uma tabela de rotas](https://docs.microsoft.com/azure/virtual-network/manage-route-table).
