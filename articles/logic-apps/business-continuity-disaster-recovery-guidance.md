---
title: Continuidade dos negócios e recuperação de desastres
description: Projete sua estratégia para proteger dados, recuperar rapidamente de eventos disruptivos, restaurar recursos exigidos por funções críticas de negócios e manter a continuidade de negócios para aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437709"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuidade de negócios e recuperação de desastres para aplicativos azure logic

Para ajudar a reduzir o impacto e os efeitos que eventos imprevisíveis têm sobre seus negócios e clientes, certifique-se de que você tenha uma solução de [ *recuperação de desastres* (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) em vigor para que você possa proteger os dados, restaurar rapidamente os recursos que suportam funções críticas de negócios e manter as operações funcionando para manter a [ *continuidade de negócios* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Por exemplo, as interrupções podem incluir paralisações, perdas na infra-estrutura subjacente ou componentes, como armazenamento, rede ou recursos computacionais, falhas irrecuperáveis de aplicativos ou até mesmo uma perda completa do datacenter. Ao ter uma solução de continuidade de negócios e recuperação de desastres (BCDR) pronta, sua empresa ou organização pode responder mais rapidamente a interrupções, planejadas ou não planejadas e reduzir o tempo de inatividade de seus clientes.

Este artigo fornece orientações e estratégias bcdr que você pode aplicar ao construir fluxos de trabalho automatizados usando [o Azure Logic Apps](../logic-apps/logic-apps-overview.md). Os fluxos de trabalho de aplicativos lógicos ajudam você a integrar e orquestrar dados mais facilmente entre aplicativos, serviços em nuvem e sistemas locais, reduzindo a quantidade de código que você tem para escrever. Ao planejar o BCDR, certifique-se de considerar não apenas seus aplicativos lógicos, mas também esses recursos do Azure que você usa com seus aplicativos lógicos:

* [Conexões](../connectors/apis-list.md) que você cria de aplicativos lógicos para outros aplicativos, serviços e sistemas. Para obter mais informações, consulte [Conexões com recursos](#resource-connections) mais tarde neste tópico.

* [Gateways de dados no local,](../logic-apps/logic-apps-gateway-connection.md) que são recursos do Azure que você cria e usa em seus aplicativos lógicos para acessar dados em sistemas locais. Cada recurso de gateway representa uma instalação de [gateway de dados](../logic-apps/logic-apps-gateway-install.md) separada em um computador local. Para obter mais informações, consulte [gateways de dados on-premises](#on-premises-data-gateways) mais tarde neste tópico.

* [Contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde você define e armazena os artefatos que os aplicativos lógicos usam para cenários [de integração empresarial (Business-to-Business)](../logic-apps/logic-apps-enterprise-integration-overview.md) . Por exemplo, você pode [configurar a recuperação de desastres entre regiões para contas de integração](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Ambientes de serviço de integração (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde você cria aplicativos lógicos que são executados em uma instância de tempo de execução isolada de Aplicativos lógicos dentro de uma rede virtual Do Azure. Esses aplicativos lógicos podem então acessar recursos protegidos atrás de um firewall nessa rede virtual.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Locais primários e secundários

Cada aplicativo lógico precisa especificar o local que você deseja usar para implantação. Este local é uma região pública no Azure multi-inquilino global, como o "West US", ou um ambiente de serviço de integração (ISE) que você criou e implantou anteriormente em uma rede virtual Do Zure. Executar aplicativos lógicos em um ISE é semelhante à execução de aplicativos lógicos em uma região global do Azure, o que significa que sua estratégia de recuperação de desastres pode se aplicar a ambos os cenários. No entanto, as ISEs têm outras considerações, como a configuração do acesso aos recursos que estão disponíveis apenas para as ISEs.

> [!NOTE]
> Se o seu aplicativo lógico também funciona com artefatos B2B, como parceiros comerciais, acordos, esquemas, mapas e certificados, que são armazenados em uma conta de integração, tanto sua conta de integração quanto os aplicativos de lógica devem especificar o mesmo local.

Essa estratégia de recuperação de desastres se concentra na configuração do seu aplicativo de lógica primária para [*failover*](https://en.wikipedia.org/wiki/Failover) em um aplicativo de lógica de standby ou backup em um local alternativo onde o Azure Logic Apps também está disponível. Dessa forma, se o primário sofrer perdas, interrupções ou falhas, o secundário pode assumir o trabalho. Essa estratégia requer que seu aplicativo de lógica secundária e recursos dependentes já estejam implantados e prontos no local alternativo.

Se você seguir boas práticas de DevOps, você já usa [os modelos do Azure Resource Manager](../azure-resource-manager/management/overview.md) para definir e implantar seus aplicativos lógicos e seus recursos dependentes. Os modelos do Gerenciador de recursos oferecem o recurso de usar uma definição de implantação única e, em seguida, usar arquivos de parâmetros para fornecer os valores de configuração a serem usados para cada destino de implantação. Esse recurso significa que você pode implantar o mesmo aplicativo lógico em diferentes ambientes, por exemplo, desenvolvimento, teste e produção. Você também pode implantar o mesmo aplicativo lógico para diferentes regiões do Azure ou ISEs, que suporta estratégias de recuperação de desastres que usam [regiões emparelhadas](../best-practices-availability-paired-regions.md).

Para a estratégia de failover, seus aplicativos e locais lógicos devem atender a esses requisitos:

* A instância do aplicativo de lógica secundária tem acesso aos mesmos aplicativos, serviços e sistemas que a instância principal do aplicativo lógico.

* Ambas as instâncias lógicas do aplicativo têm o mesmo tipo de host. Assim, ou ambas as instâncias são implantadas em regiões no Azure multi-inquilino global, ou ambas as instâncias são implantadas em ISEs, que permitem que seus aplicativos lógicos acessem diretamente os recursos em uma rede virtual Do Zure. Para obter melhores práticas e mais informações sobre regiões emparelhadas para BCDR, consulte [Continuidade de negócios e recuperação de desastres (BCDR): Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

  Por exemplo, tanto os locais primários quanto os secundários devem ser ISEs quando o aplicativo de lógica primária é executado em um ISE e usa [conectores com versão ISE,](../connectors/apis-list.md#ise-connectors)ações HTTP para chamar recursos na rede virtual do Azure, ou ambos. Neste cenário, seu aplicativo de lógica secundária também deve ter uma configuração semelhante no local secundário como o aplicativo de lógica primária.

  > [!NOTE]
  > Para cenários mais avançados, você pode misturar tanto o Azure multi-inquilino quanto um ISE como locais. No entanto, certifique-se de considerar e entender as diferenças entre como os [aplicativos lógicos são executados em um ISE versus Azure multi-inquilino](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Se você usar ISEs, [certifique-se de que eles são dimensionados ou têm capacidade suficiente](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) para lidar com a carga.

#### <a name="example-multi-tenant-azure"></a>Exemplo: Azure multi-inquilino

Este exemplo mostra instâncias de aplicativos de lógica primária e secundária, que são implantados em regiões separadas no Azure multi-inquilino global para este cenário. Um único [modelo de Gerenciador de Recursos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) define tanto instâncias lógicas de aplicativos quanto os recursos dependentes exigidos por esses aplicativos lógicos. Arquivos de parâmetros separados especificam os valores de configuração a serem usados para cada local de implantação:

![Instâncias de aplicativos de lógica primária e secundária em locais separados](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exemplo: Ambiente de serviço de integração

Este exemplo mostra as instâncias anteriores do aplicativo lógico primário e secundário, mas implantado para ISEs separados. Um único modelo de Gerenciador de Recursos define as instâncias lógicas dos aplicativos, os recursos dependentes exigidos por esses aplicativos lógicos e os ISEs como locais de implantação. Arquivos de parâmetros separados definem os valores de configuração a serem usados para implantação em cada local:

![Aplicativos de lógica primária e secundária em diferentes locais](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Conexões com recursos

O Azure Logic Apps fornece [gatilhos e ações incorporadas, além de centenas de conectores gerenciados](../connectors/apis-list.md) que seu aplicativo lógico pode usar para trabalhar com outros aplicativos, serviços, sistemas e outros recursos, como contas de armazenamento Azure, bancos de dados SQL Server, contas de e-mail do Office 365 Outlook e assim por diante. Se seu aplicativo lógico precisa ter acesso a esses recursos, você cria conexões que autenticam o acesso a esses recursos. Cada conexão é um recurso separado do Azure que existe em um local específico e não pode ser usado por recursos em outros locais.

Para sua estratégia de recuperação de desastres, considere os locais onde existem recursos dependentes em relação às instâncias do aplicativo lógico:

* Sua instância primária e recursos dependentes existem em diferentes locais. Neste caso, sua instância secundária pode se conectar aos mesmos recursos ou pontos finais dependentes. No entanto, você deve criar conexões especificamente para sua instância secundária. Dessa forma, se sua localização primária ficar indisponível, as conexões secundárias não serão afetadas.

  Por exemplo, suponha que seu aplicativo de lógica primária se conecte a um serviço externo, como o Salesforce. Normalmente, a disponibilidade e a localização do serviço externo são independentes da disponibilidade do seu aplicativo lógico. Neste caso, sua instância secundária pode se conectar ao mesmo serviço, mas deve ter sua própria conexão.

* Tanto sua instância primária quanto recursos dependentes existem no mesmo local. Neste caso, os recursos dependentes devem ter backups ou versões replicadas em um local diferente para que sua instância secundária ainda possa acessar esses recursos.

  Por exemplo, suponha que seu aplicativo de lógica primária se conecte a um serviço que está no mesmo local ou região, por exemplo, O Banco de Dados SQL do Azure. Se toda essa região ficar indisponível, o serviço de banco de dados SQL do Azure nessa região também provavelmente não está disponível. Neste caso, você gostaria que sua instância secundária usasse um banco de dados replicado ou de backup, juntamente com uma conexão separada a esse banco de dados.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateway de dados local

Se o aplicativo de lógica for executado no Azure multi-inquilino e precisar de acesso a recursos locais, como bancos de dados sql server, você precisa instalar o [gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um computador local. Em seguida, você pode criar um recurso de gateway de dados no portal Dozure para que seu aplicativo lógico possa usar o gateway quando você criar uma conexão com o recurso.

O recurso do gateway de dados está associado a uma região de localização ou Azure, assim como o recurso do aplicativo lógico. Em sua estratégia de recuperação de desastres, certifique-se de que o gateway de dados permaneça disponível para o seu aplicativo lógico usar. Você pode [habilitar alta disponibilidade para o gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) quando tiver várias instalações de gateway.

> [!NOTE]
> Se o aplicativo de lógica for executado em um ambiente de serviço de integração (ISE) e usar apenas conectores com versão ISE para fontes de dados locais, você não precisará do gateway de dados porque os conectores ISE fornecem acesso direto ao recurso local.
>
> Se nenhum conector com versão ISE estiver disponível para o recurso local que você deseja, seu aplicativo lógico ainda pode criar a conexão usando um conector não-ISE, que é executado no Azure multi-inquilino global, não no seu ISE. No entanto, essa conexão requer o gateway de dados no local.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Funções ativas versus ativas-passivas

Você pode configurar seus locais primários e secundários para que as instâncias do aplicativo lógico nesses locais possam desempenhar essas funções:

| Função primária secundária | Descrição |
|------------------------|-------------|
| *Ativo* | As instâncias do aplicativo de lógica primária e secundária em ambos os locais lidam ativamente com as solicitações seguindo qualquer um desses padrões: <p><p>- *Saldo de carga*: Você pode ter ambas as instâncias ouvir um ponto final e carregar o tráfego de equilíbrio para cada instância, conforme necessário. <p>- *Consumidores concorrentes*: Você pode ter ambas as instâncias atuando como consumidores concorrentes para que as instâncias concorram por mensagens de uma fila. Se uma instância falhar, a outra instância assume a carga de trabalho. |
| *Ativo-passivo* | A instância do aplicativo lógico principal lida ativamente com toda a carga de trabalho, enquanto a instância secundária é passiva (desativada ou inativa). O secundário aguarda um sinal de que o primário está indisponível ou não está funcionando devido a interrupção ou falha e assume a carga de trabalho como instância ativa. |
| Combinação | Alguns aplicativos lógicos desempenham um papel ativo, enquanto outros aplicativos lógicos desempenham um papel ativo-passivo. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Exemplos ativos

Esses exemplos mostram a configuração ativa em que ambas as instâncias lógicas do aplicativo lidam ativamente com solicitações ou mensagens. Algum outro sistema ou serviço distribui as solicitações ou mensagens entre instâncias, por exemplo, uma dessas opções:

* Um balanceador de carga "físico", como um pedaço de hardware que roteia o tráfego

* Um balanceador de carga "macio", como [o Azure Load Balancer](../load-balancer/load-balancer-overview.md) ou [o Azure API Management](../api-management/api-management-key-concepts.md). Com o Gerenciamento de API, você pode especificar políticas que determinam como carregar o tráfego de entrada de saldo. Ou, você pode usar um serviço que suporta rastreamento de estado, por exemplo, [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Embora este exemplo mostre principalmente o Azure Load Balancer, você pode usar a opção que melhor se adequa às necessidades do seu cenário:

  ![Configuração "ativa" que usa um balanceador de carga ou serviço de estado](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Cada instância do aplicativo lógico age como um consumidor e ambas as instâncias competem por mensagens de uma fila:

  ![Configuração "ativa" que usa "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Exemplos passivos ativos

Este exemplo mostra a configuração ativo-passiva onde a instância principal do aplicativo lógico está ativa em um local, enquanto a instância secundária permanece inativa em outro local. Se o primário tiver uma interrupção ou falha, você pode ter um operador executar um script que ativa o secundário para assumir a carga de trabalho.

![Configuração "passivo-ativo" que usa "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinação com ativo ativo e ativo-passivo

Este exemplo mostra uma configuração combinada onde o local principal tem ambas as instâncias ativas do aplicativo lógico, enquanto o local secundário tem instâncias de aplicativos de lógica ativo-passiva. Se o local principal tiver uma interrupção ou falha, o aplicativo de lógica ativa no local secundário, que já está lidando com uma carga de trabalho parcial, pode assumir toda a carga de trabalho.

* No local principal, um aplicativo de lógica ativa ouve uma fila de ônibus de serviço do Azure para mensagens, enquanto outro aplicativo de lógica ativa verifica e-mails usando um gatilho de votação do Office 365 Outlook.

* No local secundário, um aplicativo de lógica ativa trabalha com o aplicativo lógico no local principal, ouvindo e competindo por mensagens da mesma fila de Ônibus de Serviço. Enquanto isso, um aplicativo de lógica passiva inativa espera em espera para verificar e-mails quando o local principal fica indisponível, mas é *desativado* para evitar a releitura de e-mails.

![Combinação "ativo-passivo" e "ativo-passivo" que usa gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Estado e histórico do aplicativo lógico

Quando seu aplicativo lógico é acionado e começa a ser executado, o estado do aplicativo é armazenado no mesmo local onde o aplicativo começou e é intransferível para outro local. Se uma falha ou interrupção acontecer, todas as instâncias de fluxo de trabalho em andamento serão abandonadas. Quando você tem um local primário e secundário configurado, novas instâncias de fluxo de trabalho começam a ser executados no local secundário.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Reduzir as instâncias abandonadas em andamento

Para minimizar o número de instâncias de fluxo de trabalho em andamento abandonadas, você pode escolher entre vários padrões de mensagem que você pode implementar, por exemplo:

* [Padrão de deslizamento de roteamento fixo](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Esse padrão de mensagem empresarial que divide um processo de negócios em estágios menores. Para cada etapa, você configura um aplicativo lógico que lida com a carga de trabalho para essa etapa. Para se comunicaruns com os outros, seus aplicativos de lógica usam um protocolo de mensagens assíncrona, como filas ou tópicos do Azure Service Bus. Quando você divide um processo em estágios menores, você reduz o número de processos de negócios que podem ficar presos em uma instância de aplicativo lógica falhada. Para obter informações mais gerais sobre esse padrão, consulte [padrões de integração corporativa - deslizamento de roteamento](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Este exemplo mostra um padrão de deslizamento de roteamento onde cada aplicativo lógico representa um estágio e usa uma fila de Ônibus de Serviço para se comunicar com o próximo aplicativo lógico no processo.

  ![Divida um processo de negócios em etapas representadas por aplicativos lógicos, que se comunicam entre si usando filas de ônibus de serviço do Azure](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se as instâncias de aplicativos de lógica primária e secundária seguirem o mesmo padrão de deslizamento de roteamento em suas localizações, você poderá implementar o [padrão de consumidores concorrentes](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) configurando [funções ativas](#roles) para essas instâncias.

* [Padrão de gerenciador de processos (corretor)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock sem padrão de tempo](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Acesso ao gatilho e corre história

Para obter mais informações sobre as execuções passadas do fluxo de trabalho do seu aplicativo lógico, você pode rever o gatilho do aplicativo e executa o histórico. O histórico de execução de um aplicativo lógico é armazenado no mesmo local ou região onde esse aplicativo lógico foi executado, o que significa que você não pode migrar esse histórico para um local diferente. Se sua instância primária falhar em uma instância secundária, você só poderá acessar o gatilho de cada instância e executar o histórico nos respectivos locais onde essas instâncias foram executadas. No entanto, você pode obter informações agnósticas de localização sobre o histórico do seu aplicativo lógico configurando seus aplicativos lógicos para enviar eventos de diagnóstico para um espaço de trabalho do Azure Log Analytics. Em seguida, você pode rever a saúde e o histórico através de aplicativos lógicos que são executados em vários locais.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Orientação do tipo de gatilho

O tipo de gatilho que você usa em seus aplicativos lógicos determina suas opções de como você pode configurar aplicativos lógicos em locais em sua estratégia de recuperação de desastres. Aqui estão os tipos de gatilho disponíveis que você pode usar em aplicativos lógicos:

* [Gatilho de recorrência](#recurrence-trigger)
* [Gatilho de sondagem](#polling-trigger)
* [Gatilho de solicitação](#request-trigger)
* [Gatilho de webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Gatilho de recorrência

O gatilho **De recorrência** é independente de qualquer serviço ou ponto final específico, e é acionado apenas com base em um cronograma especificado e nenhum outro critério, por exemplo:

* Uma frequência fixa e um intervalo, como a cada 10 minutos
* Um cronograma mais avançado, como a última segunda-feira de cada mês às 17:00

Quando seu aplicativo lógico começa com um gatilho de recorrência, você precisa configurar suas instâncias de aplicativo de lógica primária e secundária com as [funções ativas-passivas](#roles). Para reduzir o *Objetivo do Tempo de Recuperação* (RTO), que se refere à duração-alvo para restaurar um processo de negócios após uma interrupção ou desastre, você pode configurar suas instâncias lógicas do aplicativo com uma combinação de [funções ativa-passivas](#roles) e [funções passiva-ativas.](#roles) Nesta configuração, você divide a programação entre os locais.

Por exemplo, suponha que você tenha um aplicativo lógico que precisa ser executado a cada 10 minutos. Você pode configurar seus aplicativos e locais lógicos para que, se o local principal ficar indisponível, o local secundário possa assumir o trabalho:

![Combinação "ativo-passivo" e "passivo-ativo" que usa gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Na localização principal, configure [funções ativas-passivas](#roles) para esses aplicativos lógicos:

  * Para o aplicativo lógico *ativado ativo,* defina o gatilho de recorrência para iniciar no topo da hora e repita a cada 20 minutos, por exemplo, 9:00 AM, 9:20 AM, e assim por diante.

  * Para o aplicativo lógico *passivo* desativado, defina o gatilho de Recorrência para o mesmo horário, mas comece em 10 minutos após a hora e repita a cada 20 minutos, por exemplo, 9:10 AM, 9:30 AM, e assim por diante.

* No local secundário, [configure-se passivo-ativo](#roles) para esses aplicativos lógicos:

  * Para o aplicativo lógico *passivo* desativado, defina o gatilho de recorrência para o mesmo horário do aplicativo de lógica ativa no local principal, que está no topo da hora e repita a cada 20 minutos, por exemplo, 9:00 AM, 9:10 AM, e assim por diante.

  * Para o aplicativo lógico *ativado ativo,* defina o gatilho de recorrência para o mesmo horário do aplicativo de lógica passiva no local principal, que é começar em 10 minutos após a hora e repetir a cada 20 minutos, por exemplo, 9:10 AM, 9:20 AM, e assim por diante.

Agora, se um evento disruptivo acontecer no local principal, ative o aplicativo de lógica passiva no local alternativo. Dessa forma, se encontrar a falha leva tempo, essa configuração limita o número de recorrências perdidas durante esse atraso.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Gatilho de sondagem

Para verificar regularmente se novos dados para processamento estão disponíveis a partir de um serviço específico ou ponto final, seu aplicativo lógico pode usar um gatilho *de votação* que liga repetidamente para o serviço ou ponto final com base em um cronograma de recorrência fixo. Os dados que o serviço ou ponto final fornece podem ter qualquer um desses tipos:

* Dados estáticos, que descreve dados que estão sempre disponíveis para leitura
* Dados voláteis, que descreve dados que não estão mais disponíveis após a leitura

Para evitar ler repetidamente os mesmos dados, seu aplicativo lógico precisa lembrar quais dados foram lidos anteriormente mantendo o estado no lado do cliente ou no lado do servidor, serviço ou sistema.

* Aplicativos lógicos que trabalham com o uso do estado do lado do cliente usam gatilhos que podem manter o estado.

  Por exemplo, um gatilho que lê uma nova mensagem de uma caixa de entrada de e-mail requer que o gatilho possa lembrar a mensagem de leitura mais recente. Dessa forma, o gatilho inicia o aplicativo lógico somente quando a próxima mensagem não lida chegar.

* Aplicativos de lógica que trabalham com servidor, serviço ou estado do lado do sistema usam valores de propriedade ou configurações que estão no servidor, serviço ou lado do sistema.

  Por exemplo, um gatilho baseado em consulta que lê uma linha `isRead` de um banco `FALSE`de dados requer que a linha tenha uma coluna definida como . Toda vez que o gatilho lê uma linha, o `isRead` aplicativo `FALSE` lógico `TRUE`atualiza essa linha alterando a coluna de para .

  Essa abordagem do lado do servidor funciona da mesma forma para filas de ônibus de serviço ou tópicos que têm semântica de fila onde um gatilho pode ler e bloquear uma mensagem enquanto o aplicativo lógico processa a mensagem. Quando o aplicativo lógico termina o processamento, o gatilho exclui a mensagem da fila ou tópico.

De uma perspectiva de recuperação de desastres, ao configurar as instâncias primárias e secundárias do seu aplicativo lógico, certifique-se de que você contabilize esses comportamentos com base em se o aplicativo lógico rastreia o estado no lado do cliente ou no lado do servidor:

* Para um aplicativo lógico que funciona com o estado do lado do cliente, certifique-se de que seu aplicativo lógico não leia a mesma mensagem mais de uma vez. Apenas um local pode ter uma instância de aplicativo de lógica ativa a qualquer momento específico. Certifique-se de que a instância do aplicativo lógico no local alternativo esteja inativa ou desativada até que a instância primária falhe no local alternativo.

  Por exemplo, o gatilho do Office 365 Outlook mantém o estado do lado do cliente e rastreia o carimbo de data e hora do e-mail lido mais recentemente para evitar a leitura de uma duplicata.

* Para um aplicativo lógico que funciona com o estado do lado do servidor, você pode configurar suas instâncias lógicas do aplicativo para desempenhar [funções ativas](#roles) em que funcionam como consumidores concorrentes ou [funções ativas passivas](#roles) onde a instância alternativa aguarda até que a instância primária falhe no local alternativo.

  Por exemplo, a leitura de uma fila de mensagens, como uma fila de ônibus de serviço do Azure, usa o estado do lado do servidor porque o serviço de filas mantém bloqueios de mensagens para impedir que outros clientes leiam as mesmas mensagens.

  > [!NOTE]
  > Se o seu aplicativo lógico precisar ler mensagens em uma ordem específica, por exemplo, a partir de uma fila de Ônibus de Serviço, você pode usar o padrão de consumo concorrente, mas apenas quando combinado com as sessões de Service Bus, que também é conhecida como o padrão [ *de comboio seqüencial* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy). Caso contrário, você deve configurar suas instâncias lógicas do aplicativo com as funções ativas-passivas.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Gatilho de solicitação

O gatilho **Solicitação** torna seu aplicativo lógico callable de outros aplicativos, serviços e sistemas e é normalmente usado para fornecer esses recursos:

* Uma API rest direto para o seu aplicativo lógico que outros podem chamar

  Por exemplo, use o gatilho Solicitação para iniciar seu aplicativo lógico para que outros aplicativos lógicos possam chamar o gatilho usando a ação **Call workflow - Logic Apps.**

* Um [mecanismo de webhook](#webhook-trigger) ou callback para o seu aplicativo lógico

* Uma maneira que você pode executar manualmente operações ou rotinas do usuário para chamar seu aplicativo lógico, por exemplo, usando um script PowerShell que executa uma tarefa específica

Do ponto de vista da recuperação de desastres, o gatilho Solicitação é um receptor passivo porque o aplicativo lógico não faz nenhum trabalho e espera até que algum outro serviço ou sistema chame explicitamente o gatilho. Como um ponto final passivo, você pode configurar suas instâncias primárias e secundárias desta maneira:

* [Ativo:](#roles)Ambas as instâncias lidam ativamente com solicitações ou chamadas. O chamador ou roteador equilibra ou distribui tráfego entre essas instâncias.

* [Ativo-passivo](#roles): Apenas a instância primária está ativa e lida com todo o trabalho, enquanto a instância secundária aguarda até que a principal experiência interrupção ou falha. O chamador ou roteador determina quando chamar a instância secundária.

Como uma arquitetura recomendada, você pode usar o Azure API Management como um proxy para os aplicativos lógicos que usam gatilhos de solicitação. O Gerenciamento de API fornece [resiliência inter-regional incorporada e a capacidade de direcionar o tráfego através de vários pontos finais](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Gatilho de webhook

Um gatilho *webhook* fornece o recurso para que seu aplicativo lógico assine um serviço passando uma *URL de retorno* de chamada para esse serviço. Seu aplicativo lógico pode então ouvir e esperar que um evento específico aconteça nesse ponto final do serviço. Quando o evento acontece, o serviço chama o gatilho do webhook usando a URL de retorno de chamada, que então executa o aplicativo lógico. Quando ativado, o gatilho do webhook assina o serviço. Quando desativado, o gatilho cancela a assinatura do serviço.

De uma perspectiva de recuperação de desastres, configure instâncias primárias e secundárias que usam gatilhos webhook para desempenhar funções passivas ativas porque apenas uma instância deve receber eventos ou mensagens do ponto final subscrito.

## <a name="assess-primary-instance-health"></a>Avaliar a saúde das instâncias primárias

Para que sua estratégia de recuperação de desastres funcione, sua solução precisa de maneiras de executar essas tarefas:

* [Verifique a disponibilidade da instância primária](#check-primary-availability)
* [Monitore a saúde da instância primária](#monitor-primary-health)
* [Ativar a instância secundária](#activate-secondary)

Esta seção descreve uma solução que você pode usar de imediato ou como base para seu próprio design. Aqui está uma visão geral visual de alto nível para esta solução:

![Crie um aplicativo de lógica watchdog que monitora um aplicativo lógico de verificação de saúde no local principal](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Verifique a disponibilidade da instância primária

Para determinar se a instância primária está disponível, em execução e capaz de funcionar, você pode criar um aplicativo lógico de "verificação de saúde" que está no mesmo local da instância primária. Em seguida, você pode chamar este aplicativo de verificação de saúde a partir de um local alternativo. Se o aplicativo de verificação de saúde responder com sucesso, a infra-estrutura subjacente para o serviço Azure Logic Apps nessa região está disponível e funcionando. Se o aplicativo de verificação de saúde não responder, você pode assumir que o local não é mais saudável.

Para essa tarefa, crie um aplicativo básico de lógica de verificação de saúde que execute essas tarefas:

1. Recebe uma chamada do aplicativo watchdog usando o gatilho Solicitar.

1. Responda com um status indicando se o aplicativo lógico verificado ainda funciona usando a ação Resposta.

   > [!IMPORTANT]
   > O aplicativo de lógica de verificação de saúde deve usar uma ação de resposta para que o aplicativo responda de forma sincronizada, não assíncrona.

1. Opcionalmente, para determinar ainda mais se o local primário é saudável, você pode considerar a saúde de quaisquer outros serviços que interajam com o aplicativo de lógica de destino neste local. Basta expandir o aplicativo de lógica de verificação de saúde para avaliar a saúde desses outros serviços também.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Crie um aplicativo lógico watchdog

Para monitorar a saúde da instância primária e chamar o aplicativo de lógica de verificação de saúde, crie um aplicativo de lógica "cão de guarda" em um *local alternativo*. Por exemplo, você pode configurar o aplicativo de lógica watchdog para que, se a lógica de verificação de saúde falhar, o cão de guarda possa enviar um alerta para sua equipe de operações para que eles possam investigar a falha e por que a instância primária não responde.

> [!IMPORTANT]
> Certifique-se de que seu aplicativo de lógica watchdog está em um *local diferente do local principal*. Se o serviço Logic Apps no local principal tiver problemas, seu aplicativo de lógica watchdog pode não ser executado.

Para esta tarefa, no local secundário, crie um aplicativo de lógica watchdog que execute essas tarefas:

1. Execute com base em uma recorrência fixa ou programada usando o gatilho Recorrência.

   Você pode definir a recorrência como um valor abaixo do nível de tolerância para o seu Objetivo de Tempo de Recuperação (RTO).

1. Ligue para o aplicativo de lógica de verificação de saúde no local principal usando a ação HTTP, por exemplo:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Ative sua instância secundária

Para ativar automaticamente a instância secundária, você pode criar um aplicativo lógico que chama a API de gerenciamento, como o [conector Azure Resource Manager,](https://docs.microsoft.com/connectors/arm/) para ativar os aplicativos de lógica apropriados no local secundário. Você pode expandir seu aplicativo watchdog para chamar este aplicativo de lógica de ativação depois que um número específico de falhas acontecer.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Coletar dados de diagnóstico

Você pode configurar o registro para o aplicativo lógico executado e enviar os dados de diagnóstico resultantes para serviços como Azure Storage, Azure Event Hubs e Azure Log Analytics para mais tratamento e processamento.

* Se você quiser usar esses dados com o Azure Log Analytics, você pode disponibilizar os dados para os locais primários e **secundários,** configurando as configurações de Diagnóstico do seu aplicativo lógico e enviando os dados para vários espaços de trabalho do Log Analytics. Para obter mais informações, consulte [Configure os registros do Monitor do Azure e colete dados de diagnóstico para aplicativos azure logic](../logic-apps/monitor-logic-apps-log-analytics.md).

* Se você quiser enviar os dados para o Azure Storage ou a Azure Event Hubs, você pode disponibilizar os dados para os locais primários e secundários, configurando a geo-redundância. Para obter mais informações, consulte estes artigos:<p>

  * [Recuperação de desastres do Azure Blob Storage e failover da conta](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs recuperação de desastres geográficos](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de resiliência para o Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Lista de verificação de resiliência para serviços específicos do Azure](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Gerenciamento de dados para resiliência no Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Backup e recuperação de desastres para aplicativos Do Zure](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Recuperar-se de uma interrupção do serviço em toda a região](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [SLAs (Service Level Agreements, contratos de nível de serviço da Microsoft) para serviços do Azure](https://azure.microsoft.com/support/legal/sla/)
