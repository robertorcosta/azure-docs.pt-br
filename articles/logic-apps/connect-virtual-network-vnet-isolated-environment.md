---
title: Conecte-se às redes virtuais do Azure com um ISE
description: Crie um ambiente de serviço de integração (ISE) que possa acessar redes virtuais (VNETs) do Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fedc1f6ce8fbaeaf0d2cae3a1b04169192868e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270687"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure, usando um ISE (Ambiente de Serviço de Integração)

Para cenários em que seus aplicativos lógicos e contas de integração precisam acessar uma [rede virtual do Azure,](../virtual-network/virtual-networks-overview.md)crie um [ *ambiente de serviço de integração* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Um ISE é um ambiente isolado que usa armazenamento dedicado e outros recursos que são mantidos separados do serviço "global" de aplicativos lógicos multilocatários. Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Um ISE também fornece seus próprios endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço público e multilocatário.

Quando você cria um ISE, o Azure *injeta* esse ISE na sua rede virtual Azure, que então implanta o serviço Logic Apps em sua rede virtual. Ao criar um aplicativo lógico ou uma conta de integração, selecione seu ISE como sua localização. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que aplicativos lógicos e contas de integração trabalhem juntos em um ISE, ambos devem usar o *mesmo ISE* que sua localização.

Um ISE aumentou os limites de duração de execução, retenção de armazenamento, throughput, tempo limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizados. Para obter mais informações, consulte [Limites e configuração para aplicativos azure logic](../logic-apps/logic-apps-limits-and-config.md). Para saber mais sobre as ISEs, consulte [os recursos da Rede Virtual do Azure a partir do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como concluir essas tarefas usando o portal Azure:

* Habilite o acesso ao seu ISE.
* Crie seu ISE.
* Adicione capacidade extra ao seu ISE.

Você também pode criar um ISE usando a API Logic Apps REST, incluindo a configuração de chaves gerenciadas pelo cliente:

* [Crie um ambiente de serviço de integração (ISE) usando a API Logic Apps REST](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configure chaves gerenciadas pelo cliente para criptografar dados em repouso para ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplicativos de lógica, gatilhos incorporados, ações incorporadas e conectores que são executados em seu ISE usam um plano de preços diferente do plano de preços baseado no consumo. Para saber como funcionam os preços e o faturamento dos ISEs, consulte o [modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter preços, consulte [preços da Logic Apps](../logic-apps/logic-apps-pricing.md).

* Uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Se ainda não tiver uma rede virtual, aprenda a [criar uma Rede virtual do Azure](../virtual-network/quick-create-portal.md).

  * Sua rede virtual precisa ter quatro sub-redes *vazias* para criar e implantar recursos em seu ISE. Cada sub-rede suporta um componente de Aplicativos lógicos diferente que é usado em seu ISE. Você pode criar essas sub-redes com antecedência, ou pode esperar até criar seu ISE onde você pode criar sub-redes ao mesmo tempo. Saiba mais sobre [os requisitos da sub-rede.](#create-subnet)

  * Os nomes de sub-rede precisam começar com um caractere alfabético `<` `>`ou `%` `&`um `\\` `?`sublinhado e não podem usar esses caracteres: , , , , , . . `/` 
  
  * Se você quiser implantar o ISE através de um modelo do Azure Resource Manager, primeiro certifique-se de delegar uma sub-rede vazia para microsoft.Logic/integrationServiceEnvironment. Você não precisa fazer esta delegação quando você implantar através do portal Azure.

  * Certifique-se de que sua rede virtual [habilita o acesso ao seu ISE para](#enable-access) que seu ISE possa funcionar corretamente e permanecer acessível.

  * Se você usa [o ExpressRoute](../expressroute/expressroute-introduction.md), que fornece uma conexão privada com os serviços de nuvem da Microsoft facilitadas pelo provedor de conectividade, você deve [criar uma tabela de rota](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincule essa tabela a cada sub-rede usada pelo seu ISE:

    **Nome**: <*nome da rota*><br>
    **Prefixo do endereço**: 0.0.0.0/0<br>
    **Próximo salto**: Internet

* Se você quiser usar servidores DNS personalizados para sua rede virtual Azure, [configure esses servidores seguindo essas etapas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar seu ISE em sua rede virtual. Para obter mais informações sobre o gerenciamento das configurações do servidor DNS, consulte [Criar, alterar ou excluir uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Se você alterar as configurações do servidor DNS ou DNS, você terá que reiniciar seu ISE para que o ISE possa pegar essas alterações. Para obter mais informações, consulte [Reiniciar seu ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Habilitar o acesso para o ISE

Quando você usa um ISE com uma rede virtual Azure, um problema de configuração comum é ter uma ou mais portas bloqueadas. Os conectores que você usa para criar conexões entre seu ISE e sistemas de destino também podem ter suas próprias necessidades de porta. Por exemplo, se você se comunicar com um sistema FTP usando o conector FTP, a porta que você usa no sistema FTP precisa estar disponível, por exemplo, porta 21 para envio de comandos.

Para garantir que seu ISE esteja acessível e que os aplicativos lógicos nesse ISE possam se comunicar em cada sub-rede em sua rede virtual, [abra as portas descritas nesta tabela para cada sub-rede](#network-ports-for-ise). Se as portas necessárias não estiverem disponíveis, o ISE não funcionará corretamente.

* Se você tiver várias instâncias ISE que precisam acessar outros pontos finais que tenham restrições de IP, implante um [Firewall Azure](../firewall/overview.md) ou um [dispositivo virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) de rede em sua rede virtual e rode o tráfego de saída através desse firewall ou dispositivo virtual de rede. Em seguida, você pode [configurar um único endereço IP, de saída, público, estático e previsível](connect-virtual-network-vnet-set-up-single-ip-address.md) que todas as instâncias ISE da sua rede virtual podem usar para se comunicar com sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais de firewall nesses sistemas de destino para cada ISE.

   > [!NOTE]
   > Você pode usar essa abordagem para uma única ISE quando seu cenário requer limitar o número de endereços IP que precisam de acesso. Considere se os custos adicionais para o firewall ou o aparelho de rede virtual fazem sentido para o seu cenário. Saiba mais sobre [os preços do Firewall Do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se você criou uma nova rede virtual e sub-redes do Azure sem quaisquer restrições, você não precisará configurar [os NSGs (Network Security Groups, grupos de segurança de rede)](../virtual-network/security-overview.md#network-security-groups) em sua rede virtual para controlar o tráfego entre sub-redes.

* Em uma rede virtual existente, você pode *opcionalmente* configurar NSGs [filtrando o tráfego de rede através de sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se você quiser ir por esse caminho, ou se já estiver usando NSGs, certifique-se de [abrir as portas nesta tabela](#network-ports-for-ise) na rede virtual onde você tem NSGs ou deseja configurar NSGs.

  > [!NOTE]
  > Se você usar [as regras de segurança do NSG,](../virtual-network/security-overview.md#security-rules)você precisa usar *os* protocolos TCP e UDP. As regras de segurança do NSG descrevem as portas que você deve abrir para os endereços IP que precisam de acesso a essas portas. Certifique-se de que quaisquer firewalls, roteadores ou outros itens que existam entre esses pontos finais também mantenham essas portas acessíveis a esses endereços IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Portas de rede usadas pelo seu ISE

Esta tabela descreve as portas em sua rede virtual Azure que seu ISE usa e onde essas portas são usadas. Para ajudar a reduzir a complexidade ao criar regras de segurança, as [tags de serviço](../virtual-network/service-tags-overview.md) na tabela representam grupos de prefixos de endereço IP para um serviço Específico do Azure.

> [!IMPORTANT]
> As portas de origem são efêmeras, por `*` isso certifique-se de defini-las para todas as regras. Quando observado, ise interno e ISE externo referem-se ao [ponto final selecionado na criação do ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Para obter mais informações, consulte [acesso endpoint](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Finalidade | Direção | Portas de destino | Marca de serviço de origem | Marca de serviço de destino | Observações |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Comunicação intersubnet dentro de sua rede virtual | Entrada e Saída | * | O espaço de endereço para a rede virtual que tem as sub-redes do seu ISE | O espaço de endereço para a rede virtual que tem as sub-redes do seu ISE | Necessário para que o tráfego flua *entre* as sub-redes em sua rede virtual. <p><p>**Importante**: Para que o tráfego flua entre os *componentes* em cada sub-rede, certifique-se de abrir todas as portas dentro de cada sub-rede. |
| Comunicação com seu aplicativo lógico | Entrada | 443 | ISE interna: <br>VirtualNetwork <p><p>ISE externo: <br>Internet <br>(veja a coluna **Notas)** | VirtualNetwork | Em vez de usar a tag de serviço da **Internet,** você pode especificar o endereço IP de origem para o computador ou serviço que chama qualquer gatilho de solicitação ou webhooks em seu aplicativo lógico. <p><p>**Importante**: Fechar ou bloquear esta porta impede chamadas HTTP para aplicativos lógicos que têm gatilhos de solicitação. |
| Histórico de execução de aplicativos lógicos | Entrada | 443 | ISE interna: <br>VirtualNetwork <p><p>ISE externo: <br>Internet <br>(veja a coluna **Notas)** | VirtualNetwork | Em vez de usar a tag de serviço da **Internet,** você pode especificar o endereço IP de origem para o computador ou serviço de onde deseja visualizar o histórico de execução do seu aplicativo lógico. <p><p>**Importante**: Embora fechar ou bloquear esta porta não o impeça de visualizar o histórico de execução, você não pode visualizar as entradas e saídas de cada etapa desse histórico de execução. |
| Designer de Aplicativos Lógicos - propriedades dinâmicas | Entrada | 454 | LogicAppsManagement | VirtualNetwork | As solicitações vêm dos aplicativos lógicos acessarendereços IP [de entrada de](../logic-apps/logic-apps-limits-and-config.md#inbound) ponto final para essa região. |
| Implantação do conector | Entrada | 454 | AzureConnectors | VirtualNetwork | Necessário para implantar e atualizar conectores. O fechamento ou bloqueio desta porta faz com que as implantações do ISE falhem e impede que as atualizações ou correções do conector. |
| Verificação de saúde da rede | Entrada | 454 | LogicApps | VirtualNetwork | As solicitações vêm do ponto final de acesso do Logic Apps para endereços IP [de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) e [saída](../logic-apps/logic-apps-limits-and-config.md#outbound) para essa região. |
| Dependência de Gerenciamento de Serviço de Aplicativo | Entrada | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Comunicação do Gerente de Tráfego do Azure | Entrada | ISE Interna: 454 <p><p>ISE externa: 443 | AzureTrafficManager | VirtualNetwork | |
| Gerenciamento de API - ponto de extremidade de gerenciamento | Entrada | 3443 | APIManagement | VirtualNetwork | |
| Implantação de políticas de conector | Entrada | 3443 | APIManagement | VirtualNetwork | Necessário para implantar e atualizar conectores. O fechamento ou bloqueio desta porta faz com que as implantações do ISE falhem e impede que as atualizações ou correções do conector. |
| Comunicação do seu aplicativo lógico | Saída | 80, 443 | VirtualNetwork | Varia de acordo com o destino | Os pontos finais para o serviço externo com o qual seu aplicativo lógico precisa se comunicar. |
| Azure Active Directory | Saída | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Gerenciamento de Conexão | Saída | 443 | VirtualNetwork  | AppService | |
| Publicar métricas e logs de diagnóstico | Saída | 443 | VirtualNetwork  | AzureMonitor | |
| Dependência de Armazenamento do Azure | Saída | 80, 443, 445 | VirtualNetwork | Armazenamento | |
| Dependência do Azure SQL | Saída | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Saída | 1886 | VirtualNetwork | AzureMonitor | Necessário para publicar o estado de saúde para a Saúde de Recursos |
| Dependência do Log para agente de monitoramento e política do Hub de Eventos | Saída | 5672 | VirtualNetwork | EventHub | |
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | Entrada <br>Saída | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Além disso, para que a ISE trabalhe com o Azure Cache for Redis, você deve abrir essas [portas de entrada e saída descritas no Cache Azure para Redis FAQ](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

1. No [portal Azure,](https://portal.azure.com)na caixa de pesquisa `integration service environments` principal do Azure, digite como seu filtro e selecione **Ambientes de Serviço de Integração**.

   ![Encontre e selecione "Ambientes de Serviços de Integração"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel **Ambientes de Serviço de Integração,** selecione **Adicionar**.

   ![Encontre e selecione "Ambientes de Serviços de Integração"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Forneça esses detalhes para o seu ambiente e, em seguida, selecione **'Revisar + criar',** por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Nome de assinatura do Azure*> | A assinatura do Azure a ser usada para o ambiente |
   | **Grupo de recursos** | Sim | <*Nome do grupo azure-resource*> | Um grupo de recursos novo ou existente do Azure onde você deseja criar seu ambiente |
   | **Nome do ambiente de serviço de integração** | Sim | <*nome do ambiente*> | Seu nome ISE, que pode conter apenas letras, números, hífens (`-`), sublinha (`_`) e períodos (`.`). |
   | **Local** | Sim | <*Região-datacenter azure*> | A região do datacenter do Azure na qual o ambiente será implantado |
   | **Sku** | Sim | **Premium** ou **Desenvolvedor (Sem SLA)** | O ISE SKU para criar e usar. Para obter diferenças entre essas SKUs, consulte [ISE SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: Esta opção está disponível apenas na criação da ISE e não pode ser alterada mais tarde. |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Desenvolvedor: <br>Não aplicável | Premium: <br>0 a 10 <p><p>Desenvolvedor: <br>Não aplicável | O número de unidades de processamento adicionais a serem utilizadas para este recurso ISE. Para adicionar capacidade após a criação, consulte [Adicionar capacidade ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Ponto final de acesso** | Sim | **Interno** ou **Externo** | O tipo de pontos finais de acesso a serem usados para o seu ISE. Esses pontos finais determinam se os gatilhos de solicitação ou webhook em aplicativos lógicos em seu ISE podem receber chamadas de fora da sua rede virtual. <p><p>Sua seleção também afeta a maneira como você pode visualizar e acessar entradas e saídas em seu aplicativo lógico executa o histórico. Para obter mais informações, consulte [o acesso ao ponto final da ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: Esta opção está disponível apenas na criação da ISE e não pode ser alterada mais tarde. |
   | **Rede virtual** | Sim | <*Nome de rede virtual do Azure*> | A rede virtual do Azure na qual você deseja injetar seu ambiente para que os aplicativos lógicos no ambiente possam acessar sua rede virtual. Se você não tiver uma rede, [crie uma rede virtual Azure primeiro](../virtual-network/quick-create-portal.md). <p><p>**Importante:** Você *só* pode executar esta injeção quando criar o seu ISE. |
   | **Sub-redes** | Sim | <*subnet-resource-list*> | Um ISE requer quatro sub-redes *vazias* para criar e implantar recursos em seu ambiente. Para criar cada sub-rede, [siga as etapas nesta tabela](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar e implantar recursos em seu ambiente, seu ISE precisa de quatro sub-redes *vazias* que não são delegadas a nenhum serviço. Cada sub-rede suporta um componente de Aplicativos lógicos diferente que é usado em seu ISE. Você *não pode* alterar esses endereços de sub-rede depois de criar seu ambiente. Cada sub-rede precisa atender a esses requisitos:

   * Tem um nome que começa com um caractere alfabético ou um sublinhado (sem `?` `/`números), e não usa esses caracteres: `<`, `>`, `%`, `&`, `\\`, . .

   * Usa o [formato CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço classe B.

   * Usa pelo `/27` menos um no espaço de endereço porque cada sub-rede requer 32 endereços no *mínimo*. Por exemplo: 

     * `10.0.0.0/28`tem apenas 16 endereços e é muito pequeno porque 2<sup>(32-28)</sup> é 2<sup>4</sup> ou 16.

     * `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32.

     * `10.0.0.0/24` tem 256 endereços porque 2<sup>(32-24)</sup> é 2<sup>8</sup> ou 256. No entanto, mais endereços não fornecem nenhum benefício adicional.

     Para saber mais sobre o cálculo de endereços, consulte [blocos IPv4 CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se você usar [o ExpressRoute,](../expressroute/expressroute-introduction.md)você terá que [criar uma tabela de rota](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo seu ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo do endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **Sub-redes,** selecione **Gerenciar a configuração da sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. No painel **Sub-redes,** selecione **Sub-rede**.

      ![Adicione quatro sub-redes vazias](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. No painel **Adicionar sub-rede**, forneça estas informações.

      * **Nome**: O nome da sua sub-rede
      * **Intervalo de endereços (bloco CIDR)**: O intervalo da sua sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Quando terminar, selecione **OK**.

   1. Repita essas etapas para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que você tentar criar não forem válidas, o portal Azure mostrará uma mensagem, mas não bloqueia seu progresso.

   Para obter mais informações sobre a criação de sub-redes, consulte [Adicionar uma sub-rede virtual](../virtual-network/virtual-network-manage-subnet.md).

1. Depois que o Azure validar com sucesso suas informações do ISE, selecione **Criar,** por exemplo:

   ![Após validação bem-sucedida, selecione "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure começa a implantar seu ambiente, que geralmente leva duas horas para terminar. Ocasionalmente, a implantação pode levar até quatro horas. Para verificar o status de implantação, na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Caso contrário, siga as instruções do portal Azure para a implantação de solução de problemas.

   > [!NOTE]
   > Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Este atraso significa que você pode ter que esperar antes de reutilizar essas sub-redes em outro ISE.
   >
   > Se você excluir sua rede virtual, o Azure geralmente leva até duas horas antes de liberar suas sub-redes, mas essa operação pode levar mais tempo. 
   > Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
   > Consulte [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para visualizar seu ambiente, **selecione Ir para recurso** se o Azure não for automaticamente para o seu ambiente após o término da implantação.

1. Para verificar a saúde da rede para o seu ISE, consulte [Gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Para começar a criar aplicativos lógicos e outros artefatos em seu ISE, consulte [Adicionar recursos aos ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Os conectores ISE gerenciados que ficam disponíveis depois de criar o ISE não aparecem automaticamente no seletor de conectores no Logic App Designer. Antes de usar esses conectores ISE, você tem que adicionar manualmente [esses conectores ao seu ISE para](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) que eles apareçam no Logic App Designer.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos aos ambientes de serviços de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
