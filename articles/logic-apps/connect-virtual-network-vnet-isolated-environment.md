---
title: Conectar-se a redes virtuais do Azure com um ISE
description: Criar um ISE (ambiente de serviço de integração) que pode acessar redes virtuais do Azure virtual (VNETs) por meio dos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 2d7f53862a30287460ca72297231da468514646b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648160"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure, usando um ISE (Ambiente de Serviço de Integração)

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md), crie um [*ISE* (Ambiente de Serviço de Integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente isolado que usa armazenamento dedicado e outros recursos que são mantidos separadamente do serviço "global" multilocatário dos Aplicativos Lógicos. Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Um ISE também fornece a você seus endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos compartilhados pelos aplicativos lógicos no serviço público e multilocatário.

Quando você cria um ISE, o Azure o *injeta* na rede virtual do Azure, que, em seguida, implanta o serviço de Aplicativos Lógicos em sua rede virtual. Ao criar aplicativos lógicos e contas de integração, você seleciona o ISE como sua localização. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que os aplicativos lógicos e as contas de integração funcionem juntos em um ISE, ambos devem usar o *mesmo ISE* como local.

Um ISE aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, confira [Limites e configuração para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md). Para saber mais sobre ISEs, confira [Acesso aos recursos da Rede Virtual do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como concluir estas tarefas usando o portal do Azure:

* Habilitar o acesso para seu ISE.
* Criar seu ISE.
* Adicionar capacidade extra ao ISE.

Você também pode criar um ISE usando o [exemplo de modelo de início rápido do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) ou a API REST de Aplicativos Lógicos, incluindo a configuração de chaves gerenciadas pelo cliente:

* [Criar um ambiente do serviço de integração (ISE) usando a API REST de Aplicativos Lógicos](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano baseado em consumo. Para saber como funcionam o preço e a cobrança nos ISEs, confira o [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, confira [Preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

* Uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se ainda não tiver uma rede virtual, aprenda a [criar uma Rede virtual do Azure](../virtual-network/quick-create-portal.md).

  * Sua rede virtual precisa ter quatro sub-redes *vazias* para criar e implantar recursos em seu ISE. Cada sub-rede dá suporte a um componente de Aplicativos Lógicos diferente que é usado em seu ISE. Você pode criar essas sub-redes com antecedência ou aguardar até criar o ISE, onde poderá criar as sub-redes simultaneamente. Saiba mais sobre [requisitos de sub-rede](#create-subnet).

  * Os nomes de sub-rede precisam começar com um caractere alfabético ou um sublinhado e não podem usar estes caracteres: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Se você quiser implantar o ISE por meio de um modelo do Azure Resource Manager, primeiro delegue uma sub-rede vazia a Microsoft.Logic/integrationServiceEnvironment. Não é necessário fazer essa delegação ao implantar por meio do portal do Azure.

  * Verifique se a sua rede virtual [habilita o acesso ao ISE](#enable-access) para que o ISE possa funcionar corretamente e permanecer acessível.

  * O [ExpressRoute](../expressroute/expressroute-introduction.md) ajuda a estender suas redes locais até a nuvem da Microsoft e conectá-las aos serviços em nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Especificamente, o ExpressRoute é uma rede virtual privada que roteia o tráfego por uma rede privada, em vez da Internet pública. Os aplicativos lógicos podem se conectar a recursos locais que estão na mesma rede virtual ao se conectarem por meio do ExpressRoute ou de uma rede virtual privada. 
  
    Se você usar o ExpressRoute, deverá [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo ISE:

    **Nome**: <*nome da rota*><br>
    **Prefixo de endereço**: 0.0.0.0/0<br>
    **Próximo salto**: Internet

    Essa tabela de rotas é necessária para que os componentes de Aplicativos Lógicos se comuniquem com outros serviços dependentes do Azure, como o Armazenamento do Azure e o BD SQL do Azure.

* Se quiser usar servidores DNS personalizados para sua rede virtual do Azure, [configure esses servidores seguindo estas etapas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar o ISE na rede virtual. Para saber mais informações sobre como gerenciar as configurações de servidor DNS, confira [Criar, alterar ou excluir uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Se você alterar o servidor DNS ou as configurações desse servidor, terá que reiniciar o ISE para que ele possa utilizar essas alterações. Para obter mais informações, confira [Reiniciar o ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Habilitar o acesso para o ISE

Quando você usa um ISE com uma rede virtual do Azure, um problema comum de configuração é ter uma ou mais portas bloqueadas. Os conectores que você usa para criar conexões entre seu ISE e os sistemas de destino também poderão ter requisitos de porta próprios. Quando você se comunica com um sistema FTP usando o conector FTP, a porta usada nesse sistema FTP precisa estar disponível, por exemplo, a porta 21 para enviar comandos.

Para garantir que o ISE esteja acessível e que os aplicativos lógicos nesse ISE possam se comunicar em cada sub-rede em sua rede virtual, [abra as portas descritas nesta tabela em cada sub-rede](#network-ports-for-ise). Se alguma porta necessária estiver indisponível, o ISE não funcionará corretamente.

* Se você tiver várias instâncias do ISE que precisam de acesso a outros pontos de extremidade com restrições de IP, implante um [Firewall do Azure](../firewall/overview.md) ou uma [solução de virtualização de rede](../virtual-network/virtual-networks-overview.md#filter-network-traffic) em sua rede virtual e roteie o tráfego de saída por meio do firewall ou da solução de virtualização de rede. Você pode [configurar um endereço IP único, de saída, público, estático e previsível](connect-virtual-network-vnet-set-up-single-ip-address.md) que todas as instâncias do ISE em sua rede virtual possam usar para se comunicar com os sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nesses sistemas de destino para cada ISE.

   > [!NOTE]
   > Você poderá usar essa abordagem para um único ISE quando seu cenário exigir a limitação do número de endereços IP que precisam de acesso. Considere se os custos adicionais para o firewall ou solução de virtualização de rede fazem sentido para o seu cenário. Saiba mais sobre os [preços do Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se você tiver criado uma rede virtual do Azure e sub-redes sem nenhuma restrição, não precisará configurar [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md#network-security-groups) em sua rede virtual para controlar o tráfego entre sub-redes.

* Para uma rede virtual existente, você *tem a opção* de configurar [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md#network-security-groups) para [filtrar o tráfego de rede entre sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se você quiser seguir essa rota ou se já estiver usando NSGs, [abra as portas descritas nesta tabela](#network-ports-for-ise) para esses NSGs.

  Ao configurar [regras de segurança de NSG](../virtual-network/security-overview.md#security-rules), você precisará usar *ambos* os protocolos **TCP** e **UDP** ou poderá selecionar **Qualquer um** em vez disso, para não precisar criar regras separadas para cada protocolo. As regras de segurança de NSG descrevem as portas que você deve abrir para os endereços IP que precisam de acesso a essas portas. Todos os firewalls, roteadores ou outros itens existentes entre esses pontos de extremidade também devem manter essas portas acessíveis a esses endereços IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Portas de rede usadas pelo ISE

Esta tabela descreve as portas que o ISE exige que estejam acessíveis e a finalidade dessas portas. Para ajudar a reduzir a complexidade quando você configura regras de segurança, a tabela usa [marcas de serviço](../virtual-network/service-tags-overview.md) que representam grupos de prefixos de endereço IP para um serviço específico do Azure. Onde indicado, o *ISE interno* e o *ISE externo* referem-se ao [ponto de extremidade de acesso selecionado durante a criação do ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Para obter mais informações, confira [Acesso do ponto de extremidade](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Para todas as regras, defina as portas de origem como `*` porque elas são efêmeras.

#### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Finalidade | Endereços IP ou marca de serviço de origem | Portas de origem | Endereços IP ou marca de serviço de destino | Portas de destino | Observações |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação entre sub-redes na rede virtual | Espaço de endereço da rede virtual com sub-redes do ISE | * | Espaço de endereço da rede virtual com sub-redes do ISE | * | Necessário para que o tráfego flua *entre* as sub-redes de sua rede virtual. <p><p>**Importante**: Para que o tráfego flua entre os *componentes* em cada sub-rede, abra todas as portas em cada sub-rede. |
| Ambos: <p>Comunicação com o aplicativo lógico <p><p>Executa o histórico do aplicativo lógico| ISE interno: <br>**VirtualNetwork** <p><p>ISE externo: **Internet** ou confira as **Notas** | * | **VirtualNetwork** | 443 | Em vez de usar a marca de serviço de **Internet**, você pode especificar o endereço IP de origem para estes itens: <p><p>– O computador ou serviço que chama os gatilhos de solicitação ou webhooks em seu aplicativo lógico <p>– O computador ou serviço do qual você deseja acessar o histórico de execuções do aplicativo lógico <p><p>**Importante**: Fechar ou bloquear essa porta impede chamadas para aplicativos lógicos que têm gatilhos de solicitação ou webhooks. Você também é impedido de acessar entradas e saídas para cada etapa no histórico de execuções. No entanto, você não é impedido de acessar o histórico de execuções do aplicativo lógico.|
| Designer de Aplicativos Lógicos – propriedades dinâmicas | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | As solicitações são provenientes dos [endereços IP de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) do ponto de extremidade de acesso dos Aplicativos Lógicos para essa região. |
| Implantação do conector | **AzureConnectors** | * | **VirtualNetwork** | 454 | Necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta causa falha nas implantações do ISE e impedem atualizações e correções do conector. |
| Verificação da integridade da rede | **LogicApps** | * | **VirtualNetwork** | 454 | As solicitações são provenientes dos [endereços IP de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) e dos [endereços IP de saída](../logic-apps/logic-apps-limits-and-config.md#outbound) do ponto de extremidade de acesso dos Aplicativos Lógicos para essa região. |
| Dependência de Gerenciamento de Serviço de Aplicativo | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Comunicação do Gerenciador de Tráfego do Azure | **AzureTrafficManager** | * | **VirtualNetwork** | ISE interno: 454 <p><p>ISE externo: 443 ||
| Ambos: <p>Implantação de política do conector <p>Gerenciamento de API - ponto de extremidade de gerenciamento | **APIManagement** | * | **VirtualNetwork** | 3443 | Na implantação da política de conector, o acesso à porta é necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta causa falha nas implantações do ISE e impedem atualizações e correções do conector. |
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, veja também **Notas**| Para que o ISE funcione com o Cache do Azure para Redis, você deverá abrir essas [portas de entrada e saída descritas pelas perguntas frequentes do Cache do Azure para o Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Finalidade | Endereços IP ou marca de serviço de origem | Portas de origem | Endereços IP ou marca de serviço de destino | Portas de destino | Observações |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação entre sub-redes na rede virtual | Espaço de endereço da rede virtual com sub-redes do ISE | * | Espaço de endereço da rede virtual com sub-redes do ISE | * | Necessário para que o tráfego flua *entre* as sub-redes de sua rede virtual. <p><p>**Importante**: Para que o tráfego flua entre os *componentes* em cada sub-rede, abra todas as portas em cada sub-rede. |
| Comunicação a partir do aplicativo lógico | **VirtualNetwork** | * | Varia com base no destino | 80, 443 | O destino varia de acordo com os pontos de extremidade do serviço externo com o qual seu aplicativo lógico precisa se comunicar. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Dependência de Armazenamento do Azure | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Gerenciamento de Conexão | **VirtualNetwork** | * | **AppService** | 443 ||
| Publicar métricas e logs de diagnóstico | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Dependência SQL Azure | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Necessário para publicar o status da integridade no Resource Health. |
| Dependência do Log para agente de monitoramento e política do Hub de Eventos | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, confira também **Notas**| Para que o ISE funcione com o Cache do Azure para Redis, você deverá abrir estas [portas de entrada e saída descritas pelas perguntas frequentes do Cache do Azure para o Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

1. No [portal do Azure](https://portal.azure.com), na caixa principal de pesquisa, digite `integration service environments` como filtro e selecione **Ambientes do Serviço de Integração**.

   ![Localize e selecione "Ambientes de Serviço de Integração"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel **Ambiente de Serviço de Integração**, selecione **Adicionar**.

   ![Localize e selecione "Ambientes de Serviço de Integração"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Forneça esses detalhes para seu ambiente e, em seguida, selecione **Revisar + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para o ambiente |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | Um grupo de recursos novo ou existente do Azure no qual deseja criar seu ambiente |
   | **Nome do ambiente de serviço de integração** | Sim | <*environment-name*> | O nome do ISE, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`) e pontos (`.`). |
   | **Localidade** | Sim | <*Azure-datacenter-region*> | A região do datacenter do Azure na qual o ambiente será implantado |
   | **SKU** | Sim | **Premium** ou **Desenvolvedor (sem SLA)** | A SKU do ISE a ser criada e usada. Para diferenças entre essas SKUs, confira [SKUs do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: Essa opção está disponível somente na criação do ISE e não pode ser alterada posteriormente. |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Desenvolvedor: <br>Não aplicável | Premium: <br>0 a 10 <p><p>Desenvolvedor: <br>Não aplicável | O número de unidades de processamento adicionais a serem usadas no recurso do ISE. Para adicionar capacidade após a criação, confira [Adicionar capacidade do ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Ponto de extremidade de acesso** | Sim | **Interno** ou **Externo** | O tipo dos pontos de extremidade de acesso a serem usados no ISE. Esses pontos de extremidade determinam se os gatilhos de solicitação ou webhook em Aplicativos Lógicos no ISE podem receber chamadas de fora de sua rede virtual. <p><p>Sua seleção também afeta a maneira como você pode exibir e acessar entradas e saídas em seu histórico de execuções de aplicativo lógico. Para obter mais informações, confira [Acesso do ponto de extremidade do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: Você pode selecionar o ponto de extremidade de acesso somente durante a criação do ISE e não pode alterar essa opção posteriormente. |
   | **Rede virtual** | Sim | <*Azure-virtual-network-name*> | A rede virtual do Azure na qual você deseja injetar seu ambiente para que os aplicativos lógicos no ambiente possam acessar sua rede virtual. Se não tiver uma rede, [primeiro crie uma rede virtual do Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante**: Você pode executar essa injeção *apenas* quando cria seu ISE. |
   | **Sub-redes** | Sim | <*subnet-resource-list*> | Um ISE requer quatro sub-redes *vazias* para criar e implantar recursos em seu ambiente. Para criar cada sub-rede, [siga as etapas nesta tabela](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar e implantar recursos em seu ambiente, o ISE precisa de quatro sub-redes *vazias* não delegadas a nenhum serviço. Cada sub-rede dá suporte a um componente de Aplicativos Lógicos diferente que é usado em seu ISE. Você *não pode* alterar esses endereços de sub-rede depois de criar seu ambiente. Cada sub-rede precisa atender a estes requisitos:

   * Tem um nome que começa com um caractere alfabético ou um sublinhado (sem números) e não usa estes caracteres: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.

   * Usa o [formato CIDR (Roteamento entre Domínios sem Classe)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço de Classe B.

   * Usa um `/27` no espaço de endereço porque cada sub-rede requer 32 endereços. Por exemplo, `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32. Mais endereços não fornecerão benefícios adicionais.  Para saber mais sobre como calcular endereços, confira [blocos CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), deverá [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **Sub-redes**, selecione **Gerenciar configuração de sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. No painel **Sub-redes**, selecione **Sub-rede**.

      ![Adicionar quatro sub-redes vazias](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. No painel **Adicionar sub-rede**, forneça estas informações.

      * **Name**: o nome da sub-rede
      * **Intervalo de endereços (bloco CIDR)** : o intervalo da sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Quando terminar, selecione **OK**.

   1. Repita essas etapas para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que você tentar criar não forem válidas, o portal do Azure mostrará uma mensagem, mas não bloqueará seu progresso.

   Para saber mais sobre como criar sub-redes, confira [Adicionar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

1. Depois que o Azure validar com êxito suas informações do ISE, selecione **Criar**, por exemplo:

   ![Após a validação bem-sucedida, selecione "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure inicia a implantação de seu ambiente, que geralmente leva cerca de duas horas para ser concluída. Em alguns casos, a implantação pode levar até quatro horas. Para verificar o status da implantação, na barra de ferramentas do Azure, selecione o ícone de notificações, o que abre o painel de notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Caso contrário, siga as instruções do portal do Azure para solucionar problemas de implantação.

   > [!NOTE]
   > Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Esse atraso significa que você precisará esperar antes de reutilizar essas sub-redes em outro ISE.
   >
   > Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
   > Ao excluir redes virtuais, verifique se algum recurso ainda está conectado. 
   > Confira [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para exibir seu ambiente, selecione **Ir para o recurso** se o Azure não for automaticamente para seu ambiente após a conclusão da implantação.

1. Para verificar a integridade da rede do ISE, confira [Gerenciar seu ambiente do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Para começar a criar aplicativos lógicos e outros artefatos no ISE, confira [Adicionar recursos aos ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Os conectores do ISE gerenciados que se tornam disponíveis depois que você cria o ISE não aparecem automaticamente no seletor de conector no Designer do Aplicativo Lógico. Antes de poder usar esses conectores do ISE, você precisa [adicionar manualmente esses conectores ao ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) para que eles apareçam no Designer do Aplicativo Lógico.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
