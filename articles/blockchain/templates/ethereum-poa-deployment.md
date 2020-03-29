---
title: Implantar modelo de solução de consórcio ethereum Proof-of-Authority no Azure
description: Use a solução de consórcio Ethereum Proof-of-Authority para implantar e configurar uma rede ethereum de consórcio de vários membros no Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387392"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Implantar modelo de solução de consórcio de prova de autoridade do Ethereum no Azure

Você pode usar [o modelo de solução Azure de pré-visualização do Ethereum Proof-of-Authority](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) para implantar, configurar e governar uma rede ethereum de prova de autoridade de consórcio de vários membros com conhecimento mínimo do Azure e do Ethereum.

O modelo de solução pode ser usado por cada membro do consórcio para prover uma pegada de rede blockchain usando serviços de computação, rede e armazenamento do Microsoft Azure. A pegada de rede de cada membro do consórcio consiste em um conjunto de nonos validadores equilibrados de carga com os quais um aplicativo ou usuário pode interagir para enviar transações Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Escolha uma solução Azure Blockchain

Antes de optar por usar o modelo de solução de consórcio de prova de autoridade do Ethereum, compare seu cenário com os casos de uso comum das opções disponíveis do Azure Blockchain.

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de Solução | IaaS | Os modelos de solução são modelos do Azure Resource Manager que você pode usar para prover uma topologia de rede blockchain totalmente configurada. Os modelos implantam e configuram os serviços de computação, rede e armazenamento do Microsoft Azure para um determinado tipo de rede blockchain.
[Serviço do Azure Blockchain](../service/overview.md) | PaaS | O Azure Blockchain Service Preview simplifica a formação, o gerenciamento e a governança das redes blockchain de consórcio. Use o Azure Blockchain Service para soluções que exijam PaaS, gerenciamento de consórcioou privacidade de contratos e transações.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A versão prévia do Azure Blockchain Workbench é uma coleção de serviços e recursos do Azure desenvolvidos para ajudar você a criar e implantar aplicativos de blockchain para compartilhar processos empresariais e dados com outras organizações. Use o Azure Blockchain Workbench para prototipar uma solução blockchain ou uma prova de conceito de aplicação blockchain.

## <a name="solution-architecture"></a>Arquitetura da solução

Usando o modelo de solução Ethereum, você pode implantar uma rede de consórcio de prova de autoridade ethereum baseada em uma única ou várias regiões.

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Cada implantação de membro do consórcio inclui:

* Máquinas virtuais para executar os validadores de PoA
* Azure Load Balancer para distribuir solicitações de DApp, peering e governança
* Azure Key Vault para proteger as identidades do validador
* Armazenamento do Microsoft Azure para hospedar as informações de rede persistentes e leasing de coordenação
* Azure Monitor para agregar logs e estatísticas de desempenho
* Gateway de rede virtual para permitir conexões VPN entre redes virtuais privadas (opcional)

Por padrão, o RPC e pontos de extremidade de emparelhamento são acessíveis pela IP público para permitir a conectividade simplificada entre assinaturas e nuvens. Para controles de acesso ao nível de aplicativo, você pode usar os [contratos de permissão da Parity](https://wiki.parity.io/Permissioning). As redes implantadas atrás de VPNs, que aproveitam os gateways VNet para conectividade entre assinaturas, são suportadas. Como as implantações de VPN e VNet são mais complexas, você pode querer começar com um modelo IP público ao prototipar uma solução.

Os recipientes docker são usados para confiabilidade e modularidade. O Azure Container Registry é usado para hospedar e servir imagens em versão como parte de cada implantação. As imagens de contêiner consistem em:

* Orquestrador - Gera identidades e contratos de governança. Armazena identidades em uma loja de identidade.
* Cliente paridade - Aluga identidade da loja de identidade. Descobre e conecta-se aos pares.
* EthStats Agent - Coleta registros e estatísticas locais via RPC e empurra informações para o Azure Monitor.
* Governança DApp - Interface web para interagir com contratos de Governança.

### <a name="validator-nodes"></a>Nódulos validadores

No protocolo de prova de autoridade, os nós do validador tomam o lugar dos nós de mineração tradicional. Cada validador tem uma identidade Ethereum única que permite que ele participe do processo de criação de blocos. Cada membro do consórcio pode provisionar dois ou mais nós de validador em cinco regiões, para redundância geográfica. Os nós do validador se comunicam com outros nós de mineração para chegar a consenso sobre o estado da razão distribuído de forma subjacente. Para garantir uma participação justa na rede, cada membro do consórcio está proibido de usar mais validadores do que o primeiro membro da rede. Por exemplo, se o primeiro membro implantar três validadores, cada membro só poderá ter até três validadores.

### <a name="identity-store"></a>Armazenamento de identidade

Uma loja de identidade é implantada na assinatura de cada membro que detém com segurança as identidades geradas do Ethereum. Para cada validador, o contêiner de orquestração gera uma chave privada Ethereum e armazena-a no Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Implantar rede de consórcio Ethereum

Nesta caminhada, vamos supor que você está criando uma rede de consórcio suitiva multipartidária do Ethereum. O fluxo a seguir é um exemplo de uma implantação multipartidária:

1. Três membros geram uma conta de Ethereum usando MetaMask
1. *O membro A* implanta o Ethereum PoA, fornecendo seu endereço público Ethereum
1. *Membro de A* fornece a URL do consórcio para o *Membro B* e *Membro C*
1. *Implantação do Membro B* e *Membro C*, Ethereum PoA, fornecendo seu En Público Ethereum e URL de consórcio do *Membro A*
1. *Membro A* vota na *Membro B* como um administrador
1. *Membro de A* e *Membro B* ambos os votam *Membro C* como um administrador

As próximas seções mostram como configurar a pegada do primeiro membro na rede.

### <a name="create-resource"></a>Criar recurso

No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** no canto superior esquerdo.

Selecione **o Blockchain** > **Ethereum Proof-of-Authority Consortium (pré-visualização)**.

### <a name="basics"></a>Noções básicas

Em **Basics,** especifique valores para parâmetros padrão para qualquer implantação.

![Noções básicas](./media/ethereum-poa-deployment/basic-blade.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Crie uma nova rede ou junte-se à rede existente | Você pode criar uma nova rede de consórcios ou participar de uma rede de consórcios pré-existente. A adesão a uma rede existente requer parâmetros adicionais. | Criar Novo
Endereço de Email | Você recebe uma notificação por e-mail quando sua implantação é concluída com informações sobre sua implantação. | Um endereço de e-mail válido
Nome de usuário da VM | Nome de usuário do administrador de cada VM implantado | 1-64 caracteres alfanuméricos
Tipo de autenticação | O método para autenticar para a máquina virtual. | Senha
Senha | A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. Todas as VMs têm inicialmente a mesma senha. Você pode alterar a senha após o provisionamento. | 12 a 72 caracteres 
Subscription | A assinatura para a qual deseja implantar a rede consortium |
Grupo de recursos| O grupo de recursos para o qual implantar a rede do consórcio. | myResourceGroup
Location | A região do Azure para o grupo de recursos. | Oeste dos EUA 2

Selecione **OK**.

### <a name="deployment-regions"></a>Regiões de implantação

Em *Regiões de Implantação,* especifique o número de regiões e locais para cada um. Você pode implantar em no máximo cinco regiões. A primeira região deve corresponder à localização do grupo de recursos da seção *Basics.* Para desenvolvimento ou redes de teste, você pode usar uma única região por membro. Para produção, implante em duas ou mais regiões para alta disponibilidade.

![regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de regiões|Número de regiões para implantar a rede consortium| 2
Primeira região | Primeira região para implantar a rede consortium | Oeste dos EUA 2
Segunda região | Segunda região a implantar a rede de consórcios. Regiões adicionais são visíveis quando o número de regiões é duas ou maior. | Leste dos EUA 2

Selecione **OK**.

### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

Em *tamanho e desempenho da rede,* especifique entradas para o tamanho da rede de consórcio. O tamanho do armazenamento do nó do validador dita o tamanho potencial da blockchain. O tamanho pode ser alterado após a implantação.

![Tamanho e desempenho da rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de nós do validador balanceador de carga | O número de nós validadores para provisão como parte da rede. | 2
Desempenho do armazenamento de nó do validador | O tipo de disco gerenciado para cada um dos nós validadores implantados. Para obter detalhes sobre preços, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Tamanho da máquina virtual do nó de mineração | O tamanho de máquina virtual usado para nós de mineração. Para obter detalhes sobre preços, consulte [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Padrão D2 v3

A máquina virtual e o nível de armazenamento afetam o desempenho da rede.  Use a tabela a seguir para ajudar a escolher a eficiência de custos:

SKU da máquina virtual|Camada de armazenamento|Price|Produtividade|Latency
---|---|---|---|---
F1|SSD Standard|low|low|high
D2_v3|SSD Standard|média|média|média
F16s|SSD Premium|high|high|low

Selecione **OK**.

### <a name="ethereum-settings"></a>Configurações de Ethereum

Em *Configurações do Ethereum,* especifique as configurações relacionadas ao Ethereum.

![Configurações de Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
ID do Membro do Consortium | O ID associado a cada membro participante da rede de consórcios. É usado para configurar espaços de endereço IP para evitar colisão. Para uma rede privada, o ID do membro deve ser único em diferentes organizações da mesma rede.  É necessária uma ID de membro exclusiva mesmo quando a mesma organização implanta em várias regiões. Anote o valor deste parâmetro, pois você precisa compartilhá-lo com outros membros associados para garantir que não haja colisão. O intervalo válido é de 0 a 255. | 0
ID da Rede | A ID de rede para a rede de Ethereum consortium que está sendo implantada. Cada rede Ethereum tem sua própria ID de rede, sendo que 1 indica a ID para a rede pública. A faixa válida é de 5 a 999.999.999 | 10101010
Endereço do administrador Ethereum | O endereço da conta Ethereum usado para participar da governança do PoA. Você pode usar metamask para gerar um endereço Ethereum. |
Opções avançadas | Opções avançadas para configurações do Ethereum | Habilitar
Implantar usando IP público | Se o VNet privado for selecionado, a rede será implantada atrás de um VNet Gateway e removerá o acesso ao peering. Para o VNet privado, todos os membros devem usar um VNet Gateway para que a conexão seja compatível. | IP público
Limite de gás de bloco | O limite de gás do bloco inicial da rede. | 50000000
Período para selar o bloco novamente (s) | A frequência com que serão criados blocos vazios quando não existirem transações na rede. Uma frequência superior terá uma finalidade mais rápida, mas os custos de armazenamento serão maiores. | 15
Contrato de permissão de transação | Código de bytes para o contrato de permissão de transação. Restringe a implantação e execução de contratos inteligentes a uma lista permitida de contas Ethereum. |

Selecione **OK**.

### <a name="monitoring"></a>Monitoramento

O monitoramento permite configurar um recurso de log para sua rede. O agente de monitoramento coleta e supera métricas e logs úteis da sua rede, fornecendo a capacidade de verificar rapidamente os problemas de saúde da rede ou depuração.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Monitoramento | Opção para habilitar o monitoramento | Habilitar
Conecte-se aos logs existentes do Azure Monitor | Opção para criar uma nova instância de logs do Azure Monitor ou participar de uma instância existente | Criar Novo
Location | A região onde a nova instância é implantada | Leste dos EUA
ID do espaço de trabalho de análise de log existente (Conecte-se aos logs existentes do Monitor Azure = Junte-se à existência)|ID do espaço de trabalho da instância de logs existentes do Azure Monitor||NA
Chave principal de análise de log existente (Conecte-se aos logs existentes do Monitor Azure = Junte-se à existência)|A chave principal usada para se conectar à instância de logs do Azure Monitor existente||NA

Selecione **OK**.

### <a name="summary"></a>Resumo

Clique no resumo para revisar as entradas especificadas e executar a validação básica de pré-implantação. Antes de implantar, você pode baixar o modelo e os parâmetros.

Selecione **Criar** para implantar.

Se a implantação incluir o VNet Gateways, a implantação pode levar de 45 a 50 minutos.

## <a name="deployment-output"></a>Saída de implantação

Uma vez concluída a implantação, você pode acessar os parâmetros necessários usando o portal Azure.

### <a name="confirmation-email"></a>Email de confirmação

Se você fornecer um endereço de e-mail[(Basics Section),](#basics)um e-mail é enviado que inclui as informações de implantação e links para esta documentação.

![email de implantação](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Uma vez que a implantação tenha sido concluída com sucesso e todos os recursos tenham sido provisionados, você poderá visualizar os parâmetros de saída em seu grupo de recursos.

1. Vá para o seu grupo de recursos no portal.
1. Selecione **Visão geral > implantações**.

    ![Visão geral do grupo de recursos](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecione a implantação **microsoft-azure-blockchain.azure-blockchain-ether-....**
1. Selecione a seção **Saídas.**

    ![Saídas de implantação](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Aumentando o consórcio

Para expandir seu consórcio, primeiro você deve se conectar a rede física. Se estiver implantando atrás de uma VPN, consulte a seção [Conectando o VNet Gateway](#connecting-vnet-gateways) configurar a conexão de rede como parte da implantação do novo membro. Uma vez que sua implantação seja concluída, use o [DApp de governança](#governance-dapp) para se tornar um administrador de rede.

### <a name="new-member-deployment"></a>Nova implantação de membro

Compartilhe as informações a seguir com o membro da junção. As informações são encontradas no seu e-mail pós-implantação ou na saída de implantação do portal.

* URL de dados do Consortium
* O número de nódulos que você implantou
* ID do recurso do gateway da VNET (no caso de uso da VPN)

O membro de implantação deve usar o mesmo modelo de solução de consórcio Ethereum Proof-of-Authority ao implantar sua presença de rede usando a seguinte orientação:

* Selecione **Ingressar Existente**
* Escolha o mesmo número de nodos validadores que o resto dos membros na rede para garantir uma representação justa
* Use o mesmo endereço Ethereum de administrador
* Use a *URL* de dados do consórcio fornecida nas configurações do *Ethereum*
* Se o resto da rede estiver por trás de uma VPN, selecione **VNet privada** na seção avançada

### <a name="connecting-vnet-gateways"></a>Conectar-se a gateways de rede virtual

Esta seção só é necessária se você for implantado usando um VNet privado. Você pode pular esta seção se estiver usando endereços IP públicos.

Para uma rede privada, os diferentes membros estão conectados através de conexões de gateway VNet. Antes que um membro possa entrar na rede e ver o tráfego de transações, um membro existente deve fazer uma configuração final em seu gateway VPN para aceitar a conexão. Os nós ethereum do membro que se junta não serão executados até que uma conexão seja estabelecida. Para reduzir as chances de um único ponto de falha, crie conexões de rede redundantes no consórcio.

Depois que o novo membro implanta, o membro existente deve concluir a conexão bi-direcional, configurando uma conexão de gateway de rede virtual para o novo membro. O membro existente precisa:

* O Gateway VNet ResourceID do membro de conexão. Ver [saída de implantação](#deployment-output).
* A chave de conexão compartilhada.

O membro existente deve executar o seguinte script do PowerShell para concluir a conexão. Você pode usar o Azure Cloud Shell localizado na barra de navegação superior direita no portal.

![cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Monitoramento do serviço

Você pode localizar seu portal do Azure Monitor seguindo o link no e-mail de implantação ou localizando o parâmetro na saída de implantação [OMS_PORTAL_URL].

O portal exibirá primeiro estatísticas de rede de alto nível como uma visão geral.

![Categorias de monitor](./media/ethereum-poa-deployment/monitor-categories.png)

A **seleção 'Visão geral do nó'** mostra as estatísticas de infra-estrutura por nó.

![Estatísticas do nó](./media/ethereum-poa-deployment/node-stats.png)

A seleção de **estatísticas de rede** mostra as estatísticas da rede Ethereum.

![Estatísticas da rede](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Amostra de consultas kusto

Você pode consultar os registros de monitoramento para investigar falhas ou alerta de limite de configuração. As seguintes consultas são exemplos que você pode executar na ferramenta *Pesquisa de log:*

Blocos de lista que foram relatados por mais de uma consulta validador podem ser úteis para ajudar a encontrar garfos de cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Obtenha uma contagem média de pares para um nó validador especificado com média superior a baldes de 5 minutos.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Acesso SSH

Por motivos de segurança, o acesso de porta SSH é negado por uma regra de segurança de grupo de rede por padrão. Para acessar as instâncias da máquina virtual na rede PoA, você precisa alterar a seguinte regra de segurança para *Permitir*.

1. Vá para a seção **Visão geral** do grupo de recursos implantado no portal Azure.

    ![visão geral ssh](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selecione o **Grupo de Segurança de Rede** para a região da VM que deseja acessar.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selecione a regra **allow-ssh.**

    ![ssh-permitir](./media/ethereum-poa-deployment/ssh-allow.png)

1. **Alterar ação** para **permitir**

    ![habilitar permissão ssh](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selecione **Salvar**. As alterações podem levar alguns minutos para serem aplicadas.

Você pode se conectar remotamente às máquinas virtuais para os números do validador via SSH com seu nome de usuário e senha/SSH fornecidos. O comando SSH para acessar o primeiro nó do validador está listado na saída de implantação do modelo. Por exemplo: 

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Para chegar a nós adicionais de transação, aumente o número da porta por um.

Se você for implantado em mais de uma região, mude o comando para o nome DNS ou endereço IP do balanceador de carga nessa região. Para encontrar o nome DNS ou endereço IP das outras regiões, encontre o recurso com a convenção ** \* \* \* \* \*de nomeação -lbpip-reg\# ** e visualize suas propriedades de endereço DNS e IP.

## <a name="azure-traffic-manager-load-balancing"></a>Balanceamento de carga do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure ajuda a reduzir o tempo de inatividade e a melhorar a capacidade de resposta da rede PoA roteando o tráfego de entrada em várias implantações em diferentes regiões. Verificações de saúde incorporadas e redirecionamento automático ajudam a garantir a alta disponibilidade dos pontos finais do RPC e do DApp de Governança. Esse recurso é útil se você tiver implantado em várias regiões e estiver pronto para produção.

Use o Gerenciador de tráfego para melhorar a disponibilidade da rede PoA com failover automático. Você também pode usar o Gerenciador de Tráfego para aumentar a capacidade de resposta de suas redes, encaminhando os usuários finais para o local do Azure com menor latência de rede.

Se você optar por criar um perfil do Gerenciador de Tráfego do Microsoft Azure, você pode usar o nome DNS do perfil para acesso à sua rede. Uma vez que outros membros do consórcio foram adicionados à rede, o Gerenciador de Tráfego do Microsoft Azure também pode ser usado para balancear a carga entre seus validadores implantados.

### <a name="creating-a-traffic-manager-profile"></a>Criando perfil do Gerenciador de Tráfego

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** no canto superior esquerdo.
1. Pesquisar o **perfil do Gerenciador de Tráfego**.

    ![Procure o Gerente de Tráfego do Azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Dê ao perfil um nome exclusivo e selecione o Grupo de recursos usado para a implantação do PoA.

1. Selecione **Criar** para implantar.

    ![Criar gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Uma vez implantado, selecione a instância no grupo de recursos. O nome DNS para acessar o gerenciador de tráfego pode ser encontrado na guia Visão Geral.

    ![Localize o Gerenciador de tráfego DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Escolha a guia **Endpoints** e selecione o botão **Adicionar.**
1. Forneça um nome exclusivo para o ponto de extremidade.
1. Para **o tipo de recurso Target,** escolha endereço IP **público**.
1. Escolha o endereço IP público do balanceador de carga da primeira região.

    ![Roteamento do gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região na rede implantada. Uma vez que os pontos finais estejam no status **ativado,** eles são automaticamente carregados e região equilibrada no nome DNS do gerenciador de tráfego. Agora você pode usar este nome DNS no lugar do parâmetro [CONSORTIUM_DATA_URL] em outras etapas do artigo.

## <a name="data-api"></a>API de dados

Cada membro do consórcio hospeda as informações necessárias para que outras pessoas se conectem à rede. Para permitir a facilidade de conectividade, cada membro hospeda um conjunto de informações de conexão no ponto final da API de dados.

O membro existente fornece o [CONSORTIUM_DATA_URL] antes da implantação do membro. Após a implantação, um membro de junção irá recuperar informações da interface do JSON no ponto de extremidade seguir:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta contém informações úteis para a união de membros (bloco Gênesis, contrato validador DE contrato ABI, bootnodes) e informações úteis ao membro existente (endereços validadores). Você pode usar essa padronização para estender o consórcio entre provedores de nuvem. Esta API retorna uma resposta formatada JSON com a seguinte estrutura:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>DApp de Governança

O cerne da prova de autoridade é descentralizado de governança. Uma vez que a prova de autoridade depende de uma lista permitida de autoridades de rede para manter a rede saudável, é importante fornecer um mecanismo justo para fazer modificações nesta lista de permissões. Cada implantação vem com um conjunto de contratos inteligentes e portal para a governança on-chain desta lista permitida. Quando uma alteração proposta atinge um voto maioria por membros consortium, a alteração é imposta. A votação permite que novos participantes de consenso sejam adicionados ou comprometidos, sendo removidos de forma transparente que estimule uma rede honesta.

O DApp de governança é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) pré-implantados e um aplicativo web que são usados para governar as autoridades da rede. As autoridades estão divididas em identidades de admin e nós validadores.
Os admins têm o poder de delegar a participação de consenso a um conjunto de nódulos validadores. Os administradores também podem votar outros administradores para dentro ou fora da rede.

![DApp de Governança](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governança descentralizada:** As mudanças nas autoridades de rede são administradas por meio de votação em cadeia por administradores selecionados.
* **Delegação validador:** As autoridades podem gerenciar seus nós de validação que estão configurados em cada implantação de PoA.
* **Histórico de alterações auditáveis:** Cada alteração é registrada na blockchain fornecendo transparência e auditabilidade.

### <a name="getting-started-with-governance"></a>Introdução à governança

Para realizar qualquer tipo de transação através do DApp de Governança, você precisa usar uma carteira Ethereum. A abordagem mais simples é usar uma carteira no navegador, como [metaMask;](https://metamask.io) no entanto, como esses contratos inteligentes são implantados na rede, você também pode automatizar suas interações com o contrato de Governança.

Depois de instalar o MetaMask, navegue até o DApp de Governança no navegador.  Você pode localizar a URL através do portal Azure na saída de implantação.  Se você não tiver uma carteira no navegador instalada, você não poderá realizar nenhuma ação; no entanto, você pode visualizar o estado do administrador.  

### <a name="becoming-an-admin"></a>Tornando-se um administrador

Se você é o primeiro membro implantado na rede, então você automaticamente se torna um admin e seus nós de paridade são listados como validadores. Se você está se juntando à rede, você precisa ser votado como um candidato por maioria (maior que 50%) do conjunto de admin existente. Se você optar por não se tornar um admin, seus nós ainda sincronizam e validam o blockchain; no entanto, eles não participam do processo de criação do bloco. Para iniciar o processo de votação para se tornar um administrador, **selecione Nomear** e digite seu endereço e alias e ethereum.

![Nomear](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidatos

Selecionar a guia **Candidatos** mostra o conjunto atual de administradores de candidatos.  Uma vez que um candidato atinge a maioria dos votos pelos atuais admins, o candidato é promovido a um admin.  Para votar em um candidato, selecione a linha e selecione **Vote em**. Se você mudar de idéia em uma votação, selecione o candidato e selecione **O voto Rescind .**

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administradores

A guia **Admins** mostra o conjunto atual de admins e fornece a capacidade de votar contra.  Uma vez que um admin perde mais de 50% de suporte, eles são removidos como um admin na rede. Todos os nós validadores que o admin possui perdem o status de validador e se tornam nós de transação na rede. Um admin pode ser removido por qualquer número de razões; no entanto, cabe ao consórcio concordar com uma política antecipadamente.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validadores

A seleção da guia **Validadores** exibe os ánotas de paridade implantados atuais para a instância e seu status atual (tipo Nó). Cada membro do consórcio tem um conjunto diferente de validadores nesta lista, uma vez que esta visão representa o atual membro do consórcio implantado. Se a instância for recém-implantada e você não tiver adicionado seus validadores, você terá a opção **de Adicionar validadores**. A adição de validadores escolhe automaticamente um conjunto de nodes de paridade balanceados regionalmente e os atribui ao seu conjunto de validadores. Se você tiver implantado mais nós do que a capacidade permitida, os nós restantes se tornarão nós de transação na rede.

O endereço de cada validador é atribuído automaticamente por meio do [repositório de identidades](#identity-store) no Azure.  Se um nó cair, ele renuncia à sua identidade, permitindo que outro nó em sua implantação tome seu lugar. Esse processo garante que sua participação de consenso esteja altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome do consórcio

Qualquer admin pode atualizar o nome do consórcio.  Selecione o ícone de engrenagem no canto superior esquerdo para atualizar o nome do consórcio.

### <a name="account-menu"></a>Menu da conta

No canto superior direito, está o alias e o ícone de identificação da sua conta Ethereum.  Se você é um admin, você tem a capacidade de atualizar seu alias.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Desenvolvimento do Ethereum<a id="tutorials"></a>

Para compilar, implantar e testar contratos inteligentes, aqui estão algumas opções que você pode considerar para o desenvolvimento do Ethereum:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) - Ambiente de desenvolvimento Ethereum baseado no cliente
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Ambiente de desenvolvimento ethereum baseado em navegador e local

### <a name="compile-deploy-and-execute-smart-contract"></a>Compilar, implantar e executar um contrato inteligente

No exemplo a seguir, você cria um contrato inteligente simples. Você usa a Truffle para compilar e implantar o contrato inteligente na sua rede blockchain. Uma vez implantado, você chama uma função de contrato inteligente através de uma transação.

#### <a name="prerequisites"></a>Pré-requisitos

* Instale o [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python é necessário para Truffle e Web3. Selecione a opção de instalação para incluir Python em seu caminho.
* Instale truffle v5.0.5 `npm install -g truffle@v5.0.5`. O Truffle exige a instalação de várias ferramentas, incluindo [Node.js](https://nodejs.org) e [Git](https://git-scm.com/). Para obter mais informações, consulte [a documentação da Trufa](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Criar um projeto do Truffle

Antes de compilar e implantar um contrato inteligente, você precisa criar um projeto Truffle.

1. Abra um prompt de comando ou shell.
1. Crie uma pasta chamada `HelloWorld`.
1. Mude o diretório `HelloWorld` para a nova pasta.
1. Inicialize um novo projeto Truffle usando o comando `truffle init`.

    ![Crie um novo projeto truffle](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Adicionar um contrato inteligente

Crie seus contratos **inteligentes** no subdiretório de contratos do seu projeto Truffle.

1. Crie um arquivo `postBox.sol` no nome no subdiretório de **contratos** do seu projeto Truffle.
1. Adicione o seguinte código solidity ao **postBox.sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Implantar contrato inteligente usando Trufa

Os projetos de trufas contêm um arquivo de configuração para detalhes de conexão de rede blockchain. Modifique o arquivo de configuração para incluir as informações de conexão da sua rede.

> [!WARNING]
> Nunca envie sua chave privada Ethereum pela rede. Certifique-se de que cada transação é assinada localmente primeiro e a transação com sinal é enviada pela rede.

1. Você precisa da frase mnemônica para a [conta de administração do Ethereum usada ao implantar sua rede blockchain](#ethereum-settings). Se você usou metamask para criar a conta, você pode recuperar o mnemônico do MetaMask. Selecione o ícone da conta do administrador no canto superior direito da extensão MetaMask e selecione **Configurações > Segurança & Privacidade > Revelar palavras de sed .**
1. Substitua o `truffle-config.js` conteúdo do seu projeto Trufa pelo seguinte conteúdo. Substitua o ponto final do espaço reservado e os valores mnemônicos.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Já que estamos usando o provedor Truffle HD Wallet, `npm install truffle-hdwallet-provider --save`instale o módulo em seu projeto usando o comando .

Trufa usa scripts de migração para implantar contratos inteligentes em uma rede blockchain. Você precisa de um script de migração para implantar seu novo contrato inteligente.

1. Adicione uma nova migração para implantar o novo contrato. Criar `2_deploy_contracts.js` arquivo no subdiretório **de migrações** do projeto Trufa.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Implantar na rede PoA usando o comando Truffle migrar. No prompt de comando no diretório do projeto Truffle, execute:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Chame uma função de contrato inteligente

Agora que seu contrato inteligente foi implantado, você pode enviar uma transação para chamar uma função.

1. No diretório do projeto Truffle, crie `sendtransaction.js`um novo arquivo chamado .
1. Adicione o seguinte conteúdo para **enviartransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Execute o script usando o comando Truffle execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Executar script para função de chamada via transação](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Suporte de WebAssembly (WASM)

O Suporte de WebAssembly já está habilitado para você em redes de PoA implantados recentemente. Ele permite desenvolvimento inteligente contrato em qualquer linguagem que transpila ao Assembly de Web (Rust, C, C++). Para obter mais informações, consulte: [Visão geral da paridade do WebAssembly](https://wiki.parity.io/WebAssembly-Home) e tutorial da [Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>Perguntas frequentes

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Notei que há muitas transações na rede que eu não enviei. De onde elas vêm?

Não é seguro para desbloquear a [API pessoal](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Os bots ouvem as contas do Ethereum desbloqueadas e tentam drenar os fundos. O bot pressupõe que essas contas contêm real e tentativa pra ser o primeiro a extrair o saldo. Não habilite a API pessoal na rede. Em vez disso, pré-assine as transações usando manualmente uma carteira como MetaMask ou programáticamente.

### <a name="how-to-ssh-onto-a-vm"></a>Como o SSH em uma VM?

A porta SSH não é exposta por motivos de segurança. Siga [este guia para habilitar a porta SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como configurar um nó de transação ou membro de auditoria?

Os nós de transação são um conjunto de clientes de paridade que são associados à rede, mas não estão participando de consenso. Esses nós ainda podem ser usados para enviar transações Ethereum e ler o estado de contrato inteligente. Esse mecanismo funciona para fornecer auditabilidade aos membros do consórcio não-autoridade na rede. Para isso, siga os passos no [Crescimento do Consórcio.](#growing-the-consortium)

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Por que as transações de MetaMask estão demorando muito tempo?

Para garantir que as transações são recebidas na ordem correta, cada transação Ethereum vem com um incremento nonce. Se você usou uma conta no MetaMask em uma rede diferente, você precisa redefinir o valor nonce. Clique no ícone de configurações (três barras), Configurações, Conta de redefinição. O histórico de transações será limpo e agora você pode reenviar a transação.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>É necessário especificar o valor de gás no MetaMask?

O Ether não tem nenhuma finalidade no consórcio de prova de autoridade. Portanto, não há necessidade de especificar taxa de gás ao enviar transações no MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que deverei fazer se minha implantação falhar devido à falha de provisionamento do Azure OMS?

O monitoramento é um recurso opcional. Em alguns casos raros em que sua implantação falha devido à incapacidade de provisionar com sucesso o recurso do Azure Monitor, você pode reimplantar sem o Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>As implantações de IP público são compatíveis com as implantações de rede privada?

Não. Peering requer comunicação bidirecional, de modo que toda a rede deve ser pública ou privada.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual é a taxa de transferência de transação esperada da prova de autoridade?

A taxa de transferência de transação será altamente dependente dos tipos de transações e da topologia de rede. Usando transações simples, obtivemos um parâmetro de comparação de 400 transações por segundo com uma rede implantada em várias regiões.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como assinar eventos do contrato inteligente?

Agora, a prova de autoridade do Ethereum dá suporte a soquetes da Web.  Verifique a saída de implantação para localizar a URL e a porta do soquete web.

## <a name="next-steps"></a>Próximas etapas

Para obter mais soluções azure Blockchain, consulte a [documentação do Azure Blockchain](https://docs.microsoft.com/azure/blockchain/).
