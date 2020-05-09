---
title: Conectar-se a redes virtuais do Azure com um ISE
description: Criar um ISE (ambiente do serviço de integração) que possa acessar redes virtuais do Azure (VNETs) de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 8fab8c51655c860bc63715a5313c18ac72d4b0cd
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871626"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure, usando um ISE (Ambiente de Serviço de Integração)

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md), crie um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente isolado que usa armazenamento dedicado e outros recursos que são mantidos separados do serviço de aplicativos lógicos multilocatários "globais". Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Um ISE também fornece seus próprios endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço público e de vários locatários.

Quando você cria um ISE, o Azure *injeta* esse ISE em sua rede virtual do Azure, que, em seguida, implanta o serviço de aplicativos lógicos em sua rede virtual. Ao criar um aplicativo lógico ou uma conta de integração, selecione o ISE como seu local. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que os aplicativos lógicos e as contas de integração trabalhem juntos em um ISE, ambos devem usar o *mesmo ISE* como seu local.

Um ISE aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md). Para saber mais sobre o ISEs, confira [acesso aos recursos de rede virtual do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como concluir essas tarefas usando o portal do Azure:

* Habilite o acesso para o ISE.
* Crie seu ISE.
* Adicione capacidade extra ao ISE.

Você também pode criar um ISE usando o [modelo de Azure Resource Manager início rápido de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) ou usando a API REST de aplicativos lógicos, incluindo a configuração de chaves gerenciadas pelo cliente:

* [Criar um ambiente do serviço de integração (ISE) usando a API REST de aplicativos lógicos](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

* Uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se ainda não tiver uma rede virtual, aprenda a [criar uma Rede virtual do Azure](../virtual-network/quick-create-portal.md).

  * Sua rede virtual precisa ter quatro sub-redes *vazias* para criar e implantar recursos no ISE. Cada sub-rede dá suporte a um componente de aplicativos lógicos diferente que é usado em seu ISE. Você pode criar essas sub-redes com antecedência, ou pode esperar até criar seu ISE, em que você pode criar sub-redes ao mesmo tempo. Saiba mais sobre [os requisitos de sub-rede](#create-subnet).

  * Os nomes de sub-rede precisam começar com um caractere alfabético ou um sublinhado e não podem usar esses `<`caracteres `>`: `%`, `&` `\\`,, `?`, `/`,,. 
  
  * Se você quiser implantar o ISE por meio de um modelo de Azure Resource Manager, primeiro certifique-se de delegar uma sub-rede vazia a Microsoft. Logic/integrationServiceEnvironment. Você não precisa fazer essa delegação ao implantar por meio do portal do Azure.

  * Verifique se sua rede virtual [permite o acesso ao ISE](#enable-access) para que o ISE possa funcionar corretamente e permanecer acessível.

  * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), que fornece uma conexão privada aos serviços de nuvem da Microsoft que é facilitada pelo provedor de conectividade, você deve [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo ISE:

    **Nome**: <*nome da rota*><br>
    **Prefixo de endereço**: 0.0.0.0/0<br>
    **Próximo salto**: Internet

* Se você quiser usar servidores DNS personalizados para sua rede virtual do Azure, [Configure esses servidores seguindo estas etapas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar o ISE em sua rede virtual. Para obter mais informações sobre como gerenciar configurações do servidor DNS, consulte [criar, alterar ou excluir uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Se você alterar as configurações do servidor DNS ou do servidor DNS, será necessário reiniciar o ISE para que o ISE possa pegar essas alterações. Para obter mais informações, consulte [reiniciar o ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Habilitar o acesso para o ISE

Quando você usa um ISE com uma rede virtual do Azure, um problema de configuração comum é ter uma ou mais portas bloqueadas. Os conectores que você usa para criar conexões entre o ISE e os sistemas de destino também podem ter seus próprios requisitos de porta. Por exemplo, se você se comunicar com um sistema FTP usando o conector de FTP, a porta que você usa em seu sistema FTP precisa estar disponível, por exemplo, a porta 21 para enviar comandos.

Para garantir que o ISE esteja acessível e que os aplicativos lógicos nesse ISE possam se comunicar em cada sub-rede em sua rede virtual, [abra as portas descritas nesta tabela para cada sub-rede](#network-ports-for-ise). Se qualquer porta necessária estiver indisponível, o ISE não funcionará corretamente.

* Se você tiver várias instâncias do ISE que precisam de acesso a outros pontos de extremidade que têm restrições de IP, implante um [Firewall do Azure](../firewall/overview.md) ou um [dispositivo de rede virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) em sua rede virtual e roteie o tráfego de saída por meio desse firewall ou dispositivo de rede virtual. Em seguida, você pode [configurar um endereço IP único, de saída, público, estático e previsível](connect-virtual-network-vnet-set-up-single-ip-address.md) que todas as instâncias do ISE em sua rede virtual possam usar para se comunicar com sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nesses sistemas de destino para cada ISE.

   > [!NOTE]
   > Você pode usar essa abordagem para um único ISE quando seu cenário exigir a limitação do número de endereços IP que precisam de acesso. Considere se os custos adicionais para o firewall ou dispositivo de rede virtual fazem sentido para seu cenário. Saiba mais sobre os [preços do firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se você tiver criado uma nova rede virtual do Azure e sub-redes sem nenhuma restrição, não será necessário configurar [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md#network-security-groups) em sua rede virtual para controlar o tráfego entre sub-redes.

* Para uma rede virtual existente, você pode, *opcionalmente* , configurar [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md#network-security-groups) para [filtrar o tráfego de rede entre sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se você quiser ir até essa rota ou se já estiver usando o NSGs, [abra as portas descritas nesta tabela](#network-ports-for-ise) para essas NSGs.

  Ao configurar [as regras de segurança do NSG](../virtual-network/security-overview.md#security-rules), você precisa usar *both* os protocolos **TCP** e **UDP** , ou pode selecionar **qualquer** em vez disso, para não precisar criar regras separadas para cada protocolo. As regras de segurança do NSG descrevem as portas que você deve abrir para os endereços IP que precisam de acesso a essas portas. Certifique-se de que todos os firewalls, roteadores ou outros itens existentes entre esses pontos de extremidade também mantenham essas portas acessíveis a esses endereços IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Portas de rede usadas pelo ISE

Esta tabela descreve as portas que o ISE exige para ser acessível e a finalidade dessas portas. Para ajudar a reduzir a complexidade quando você configura as regras de segurança, a tabela usa [marcas de serviço](../virtual-network/service-tags-overview.md) que representam grupos de prefixos de endereço IP para um serviço específico do Azure. Onde observado, *ISE interno* e *ISE externo* referem-se ao [ponto de extremidade de acesso selecionado durante a criação do ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Para obter mais informações, consulte [Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Para todas as regras, certifique-se de definir as portas `*` de origem como porque as portas de origem são efêmeras.

#### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Finalidade | Marca de serviço de origem ou endereços IP | Portas de origem | Marca de serviço de destino ou endereços IP | Portas de destino | Anotações |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação entre sub-redes na rede virtual | Espaço de endereço para a rede virtual com sub-redes do ISE | * | Espaço de endereço para a rede virtual com sub-redes do ISE | * | Necessário para o tráfego fluir *entre* as sub-redes em sua rede virtual. <p><p>**Importante**: para o tráfego fluir entre os *componentes* em cada sub-rede, certifique-se de abrir todas as portas em cada sub-rede. |
| Ambos: <p>Comunicação com seu aplicativo lógico <p><p>Histórico de execuções do aplicativo lógico| ISE interno: <br>**VirtualNetwork** <p><p>ISE externo: **Internet** ou ver **observações** | * | **VirtualNetwork** | 443 | Em vez de usar a marca de serviço de **Internet** , você pode especificar o endereço IP de origem para estes itens: <p><p>-O computador ou serviço que chama os gatilhos de solicitação ou WebHooks em seu aplicativo lógico <p>-O computador ou serviço do qual você deseja acessar o histórico de execuções de aplicativo lógico <p><p>**Importante**: fechar ou bloquear essa porta impede chamadas para aplicativos lógicos que têm gatilhos de solicitação ou WebHooks. Você também é impedido de acessar entradas e saídas para cada etapa no histórico de execuções. No entanto, você não é impedido de acessar o histórico de execuções de aplicativo lógico.|
| Designer de aplicativos lógicos – propriedades dinâmicas | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | As solicitações são provenientes dos [endereços IP de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) do ponto de extremidade de acesso dos aplicativos lógicos para essa região. |
| Implantação do conector | **AzureConnectors** | * | **VirtualNetwork** | 454 | Necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta faz com que implantações do ISE falhem e impeçam atualizações e correções do conector. |
| Verificação de integridade da rede | **LogicApps** | * | **VirtualNetwork** | 454 | As solicitações são provenientes dos [endereços IP de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) do ponto de extremidade de acesso dos aplicativos lógicos e dos [endereços IP de saída](../logic-apps/logic-apps-limits-and-config.md#outbound) para essa região. |
| Dependência de Gerenciamento de Serviço de Aplicativo | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Comunicação do Gerenciador de tráfego do Azure | **AzureTrafficManager** | * | **VirtualNetwork** | ISE interno: 454 <p><p>ISE externo: 443 ||
| Ambos: <p>Implantação de política de conector <p>Gerenciamento de API - ponto de extremidade de gerenciamento | **APIManagement** | * | **VirtualNetwork** | 3443 | Para a implantação da política de conector, o acesso à porta é necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta faz com que implantações do ISE falhem e impeçam atualizações e correções do conector. |
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, além de ver **observações**| Para que o ISE funcione com o cache do Azure para Redis, você deve abrir essas [portas de entrada e saída descritas pelo cache do Azure para perguntas frequentes do Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Finalidade | Marca de serviço de origem ou endereços IP | Portas de origem | Marca de serviço de destino ou endereços IP | Portas de destino | Anotações |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação entre sub-redes na rede virtual | Espaço de endereço para a rede virtual com sub-redes do ISE | * | Espaço de endereço para a rede virtual com sub-redes do ISE | * | Necessário para o tráfego fluir *entre* as sub-redes em sua rede virtual. <p><p>**Importante**: para o tráfego fluir entre os *componentes* em cada sub-rede, certifique-se de abrir todas as portas em cada sub-rede. |
| Comunicação de seu aplicativo lógico | **VirtualNetwork** | * | Varia de acordo com o destino | 80, 443 | O destino varia de acordo com os pontos de extremidade para o serviço externo com o qual seu aplicativo lógico precisa se comunicar. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Dependência de Armazenamento do Azure | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Gerenciamento de Conexão | **VirtualNetwork** | * | **AppService** | 443 ||
| Publicar logs de diagnóstico & métricas | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Dependência do SQL do Azure | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Necessário para a publicação do status de integridade para Resource Health. |
| Dependência do Log para agente de monitoramento e política do Hub de Eventos | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, além de ver **observações**| Para que o ISE funcione com o cache do Azure para Redis, você deve abrir essas [portas de entrada e saída descritas pelo cache do Azure para perguntas frequentes do Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

1. Na [portal do Azure](https://portal.azure.com), na caixa principal do Azure Search, insira `integration service environments` como seu filtro e selecione ambientes de **serviço de integração**.

   ![Localize e selecione "ambientes do serviço de integração"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel **ambientes do serviço de integração** , selecione **Adicionar**.

   ![Localize e selecione "ambientes do serviço de integração"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Forneça esses detalhes para seu ambiente e, em seguida, selecione **revisar + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-nome da assinatura*> | A assinatura do Azure a ser usada para o ambiente |
   | **Grupo de recursos** | Sim | <*Azure-Resource-Group-Name*> | Um grupo de recursos novo ou existente do Azure no qual você deseja criar seu ambiente |
   | **Nome do ambiente do serviço de integração** | Sim | <*nome do ambiente*> | O nome do ISE, que pode conter apenas letras, números, hifens`-`(), sublinhados`_`() e pontos (`.`). |
   | **Localidade** | Sim | <*Azure-datacenter-região*> | A região do datacenter do Azure na qual o ambiente será implantado |
   | **SKU** | Sim | **Premium** ou **desenvolvedor (sem SLA)** | A SKU do ISE a ser criada e usada. Para diferenças entre essas SKUs, consulte [SKUs do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: essa opção está disponível somente na criação do ISE e não pode ser alterada posteriormente. |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Desenvolvedor <br>Não aplicável | Premium: <br>0 a 10 <p><p>Desenvolvedor <br>Não aplicável | O número de unidades de processamento adicionais a serem usadas para este recurso do ISE. Para adicionar capacidade após a criação, consulte [adicionar capacidade do ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Ponto de extremidade de acesso** | Sim | **Interno** ou **externo** | O tipo de pontos de extremidade de acesso a ser usado para o ISE. Esses pontos de extremidade determinam se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual. <p><p>Sua seleção também afeta a maneira como você pode exibir e acessar entradas e saídas em seu histórico de execuções de aplicativo lógico. Para obter mais informações, consulte [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: você pode selecionar o ponto de extremidade de acesso somente durante a criação do ISE e não pode alterar essa opção posteriormente. |
   | **Rede virtual** | Sim | <*Azure-Virtual-Network-Name*> | A rede virtual do Azure na qual você deseja injetar seu ambiente para que os aplicativos lógicos no ambiente possam acessar sua rede virtual. Se você não tiver uma rede, [primeiro crie uma rede virtual do Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante**: você *só* pode executar essa injeção ao criar o ISE. |
   | **Sub-redes** | Sim | <*sub-rede-lista de recursos*> | Um ISE requer quatro sub-redes *vazias* para criar e implantar recursos em seu ambiente. Para criar cada sub-rede, [siga as etapas nesta tabela](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar e implantar recursos em seu ambiente, o ISE precisa de quatro sub-redes *vazias* que não são delegadas a nenhum serviço. Cada sub-rede dá suporte a um componente de aplicativos lógicos diferente que é usado em seu ISE. Você *não pode* alterar esses endereços de sub-rede depois de criar seu ambiente. Cada sub-rede precisa atender a esses requisitos:

   * Tem um nome que começa com um caractere alfabético ou um sublinhado (sem números) e não usa estes caracteres: `<`, `>` `%` `&` `\\`,,,, `?`, `/`.

   * Usa o [formato CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço da classe B.

   * Usa um `/27` no espaço de endereço porque cada sub-rede requer 32 endereços. Por exemplo, `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32. Mais endereços não fornecerão benefícios adicionais.  Para saber mais sobre como calcular endereços, consulte [blocos CIDR de IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), precisará [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a rota a seguir e vincular essa tabela a cada sub-rede usada pelo ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **sub-redes** , selecione **gerenciar configuração de sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. No painel **sub-redes** , selecione **sub-rede**.

      ![Adicionar quatro sub-redes vazias](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. No painel **Adicionar sub-rede**, forneça estas informações.

      * **Nome**: o nome da sua sub-rede
      * **Intervalo de endereços (bloco CIDR)**: o intervalo da sua sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Quando terminar, selecione **OK**.

   1. Repita essas etapas para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que você tentar criar não forem válidas, o portal do Azure mostrará uma mensagem, mas não bloqueará seu progresso.

   Para obter mais informações sobre como criar sub-redes, consulte [Adicionar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

1. Depois que o Azure valida com êxito as informações do ISE, selecione **criar**, por exemplo:

   ![Após a validação bem-sucedida, selecione "criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure inicia a implantação de seu ambiente, que geralmente leva dentro de duas horas para ser concluído. Ocasionalmente, a implantação pode levar até quatro horas. Para verificar o status da implantação, na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Caso contrário, siga as instruções de portal do Azure para solucionar problemas de implantação.

   > [!NOTE]
   > Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Esse atraso significa que você pode precisar esperar antes de reutilizar essas sub-redes em outro ISE.
   >
   > Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
   > Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
   > Consulte [excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para exibir seu ambiente, selecione **ir para o recurso** se o Azure não acessar automaticamente seu ambiente após a conclusão da implantação.

1. Para verificar a integridade da rede para o ISE, consulte [gerenciar o ambiente do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Para começar a criar aplicativos lógicos e outros artefatos em seu ISE, consulte [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Conectores do ISE gerenciados que se tornam disponíveis depois que você cria o ISE não aparecem automaticamente no seletor de conector no designer do aplicativo lógico. Antes de poder usar esses conectores do ISE, você precisa [adicionar manualmente esses conectores ao ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) para que eles apareçam no designer do aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos a ambientes do serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre a [rede virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
