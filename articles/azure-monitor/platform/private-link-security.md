---
title: Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor
description: Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: a7464216649d6b482893693a1f182af5cf6e77ac
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508957"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor

O [Link Privado do Azure](../../private-link/private-link-overview.md) permite vincular com segurança os serviços PaaS do Azure a sua rede virtual usando pontos de extremidade privados. Em muitos serviços, basta configurar um ponto de extremidade por recurso. Já o Azure Monitor é uma constelação de diferentes serviços interconectados que trabalham juntos para monitorar suas cargas de trabalho. Como resultado, criamos um recurso chamado de Azure Monitor escopo de link privado (AMPLS). O AMPLS permite que você defina os limites de sua rede de monitoramento e conecte-se à sua rede virtual. Este artigo aborda quando usar e como configurar um Escopo de Link Privado do Azure Monitor.

## <a name="advantages"></a>Vantagens

Com o Link Privado, você pode:

- Conectar-se de maneira privada ao Azure Monitor sem abrir nenhum acesso de rede pública
- Garantir que o acesso aos seus dados de monitoramento esteja restrito a redes privadas autorizadas
- Impedir a exportação de dados de suas redes privadas definindo recursos específicos do Azure Monitor que se conectam por meio de seu ponto de extremidade privado
- Conectar sua rede local privada ao Azure Monitor com segurança usando ExpressRoute e Link Privado
- Manter todo o tráfego dentro da rede de backbone do Microsoft Azure

Para mais informações, confira [Principais benefícios do Link Privado](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Como ele funciona

Azure Monitor escopo de link privado (AMPLS) conecta pontos de extremidade privados (e os VNets em que estão contidos) a um ou mais recursos de Azure Monitor-Log Analytics espaços de trabalho e componentes Application Insights.

![Diagrama da topologia de recursos básica](./media/private-link-security/private-link-basic-topology.png)

> [!NOTE]
> Um único recurso do Azure Monitor pode pertencer a vários AMPLSs, mas não é possível conectar uma única VNet a mais de um AMPLS. 

### <a name="the-issue-of-dns-overrides"></a>O problema de substituições de DNS
Log Analytics e Application Insights usar pontos de extremidade globais para alguns de seus serviços, o que significa que eles atendem a solicitações direcionadas a qualquer espaço de trabalho/componente. Por exemplo, Application Insights usa um ponto de extremidade global para ingestão de logs e Application Insights e Log Analytics usar um ponto de extremidade global para solicitações de consulta.

Quando você configura uma conexão de link privado, o DNS é atualizado para mapear Azure Monitor pontos de extremidade para endereços IP privados do intervalo de IP de sua VNet. Essa alteração substitui qualquer mapeamento anterior desses pontos de extremidade, o que pode ter implicações significativas, revisado abaixo. 

## <a name="planning-based-on-your-network-topology"></a>Planejamento baseado em sua topologia de rede

Antes de configurar sua configuração de link privado Azure Monitor, considere sua topologia de rede e, especificamente, sua topologia de roteamento de DNS. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor link privado se aplica a todos os Azure Monitor recursos – é tudo ou nada
Como alguns pontos de extremidade Azure Monitor são globais, é impossível criar uma conexão de link privado para um componente ou espaço de trabalho específico. Em vez disso, quando você configura um link privado para um único componente de Application Insights, os registros DNS são atualizados para **todos os** componentes de Application insights. Qualquer tentativa de ingerir ou consultar um componente tentará passar pelo link privado e possivelmente falhará. Da mesma forma, a configuração de um link privado para um único espaço de trabalho fará com que todas as Log Analytics consultas passem pelo ponto de extremidade de consulta de link privado (mas não por solicitações de ingestão, que têm pontos de extremidade específicos do espaço de trabalho).

![Diagrama de substituições de DNS em uma única VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Isso é verdade não apenas para uma VNet específica, mas para todos os VNets que compartilham o mesmo servidor DNS (consulte [o problema de substituições de DNS](#the-issue-of-dns-overrides)). Portanto, por exemplo, a solicitação para ingerir logs para qualquer Application Insights componente sempre será enviada por meio da rota de link privado. Os componentes que não estão vinculados ao AMPLS falharão na validação do link privado e não passarão por ele.

**Efetivamente, isso significa que você deve conectar todos os Azure Monitor recursos em sua rede a um link privado (adicioná-los ao AMPLS) ou a nenhum deles.**

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor link privado se aplica a toda a sua rede
Algumas redes são compostas por vários VNets. Se esses VNets usarem o mesmo servidor DNS, eles substituirão os mapeamentos de DNS uns dos outros e possivelmente poderão interromper a comunicação entre si com o Azure Monitor (consulte [o problema de substituições de DNS](#the-issue-of-dns-overrides)). Por fim, somente a última VNet será capaz de se comunicar com Azure Monitor, já que o DNS mapeará Azure Monitor pontos de extremidade para IPs privados desse intervalo VNets (que talvez não possa ser acessado de outros VNets).

![Diagrama de substituições de DNS em vários VNets](./media/private-link-security/dns-overrides-multiple-vnets.png)

No diagrama acima, VNet 10.0.1. x se conecta primeiro ao AMPLS1 e mapeia os pontos de extremidade globais Azure Monitor para IPs de seu intervalo. Posteriormente, a VNet 10.0.2. x se conecta ao AMPLS2 e substitui o mapeamento DNS dos *mesmos pontos de extremidade globais* por IPS de seu intervalo. Como esses VNets não são emparelhados, a primeira VNet agora falha em atingir esses pontos de extremidade.

**VNets que usam o mesmo DNS devem ser emparelhados, seja diretamente ou por meio de uma VNet de Hub. VNets que não são emparelhadas também devem usar um servidor DNS diferente, encaminhadores DNS ou outro mecanismo para evitar conflitos de DNS.**

### <a name="hub-spoke-networks"></a>Redes hub-spoke
Topologias hub-spoke podem evitar o problema de substituições de DNS definindo um link privado na VNet do Hub (principal), em vez de configurar um link privado para cada VNet separadamente. Essa configuração faz sentido, especialmente se os recursos de Azure Monitor usados pelo VNets de spoke são compartilhados. 

![Hub-e-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Você pode intencionalmente preferir criar links privados separados para seu spoke VNets, por exemplo, para permitir que cada VNet acesse um conjunto limitado de recursos de monitoramento. Nesses casos, você pode criar um ponto de extremidade privado dedicado e AMPLS para cada VNet, mas também deve verificar se eles não compartilham o mesmo servidor DNS para evitar substituições de DNS.


### <a name="consider-limits"></a>Considerar limites

Conforme listado em [restrições e limitações](#restrictions-and-limitations), o objeto AMPLS tem vários limites, representados na topologia abaixo:
* Cada VNet se conecta a apenas **um** objeto AMPLS.
* O AMPLS B está conectado a pontos de extremidade privados de dois VNets (VNet2 e VNet3), usando duas das 10 conexões de ponto de extremidades privadas possíveis.
* O AMPLS A se conecta a dois espaços de trabalho e a um componente do Application Insight, usando três das 50 conexões de recursos Azure Monitor possíveis.
* Workspace2 conecta-se ao AMPLS A e AMPLS B, usando duas das 5 conexões de AMPLS possíveis.

![Diagrama de limites de AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Exemplo de conexão

Comece criando um recurso de Escopo de Link Privado do Azure Monitor.

1. No portal do Azure, acesse **Criar um recurso** e procure por **Escopo de Link Privado do Azure Monitor**.

   ![Localizar Azure Monitor escopo de link privado](./media/private-link-security/ampls-find-1c.png)

2. Selecione **criar**.
3. Escolha uma assinatura e um grupo de recursos.
4. Nomeie o AMPLS. É melhor usar um nome significativo e claro, como "AppServerProdTelem".
5. Selecione **Examinar + criar**. 

   ![Criar Azure Monitor escopo de link privado](./media/private-link-security/ampls-create-1d.png)

6. Permita que a validação seja aprovada e, em seguida, selecione **criar**.

### <a name="connect-azure-monitor-resources"></a>Conectar recursos do Azure Monitor

Conecte Azure Monitor recursos (Log Analytics espaços de trabalho e componentes do Application Insights) ao seu AMPLS.

1. Em seu escopo de vínculo privado Azure Monitor, selecione **Azure monitor recursos** no menu à esquerda. Selecione o botão **Adicionar**.
2. Adicione o workspace ou componente. A seleção do botão **Adicionar** abre uma caixa de diálogo onde você pode selecionar Azure monitor recursos. Você pode navegar por suas assinaturas e seus grupos de recursos ou digitar o nome deles para filtrá-los. Selecione o espaço de trabalho ou componente e selecione **aplicar** para adicioná-los ao seu escopo.

    ![Captura de tela com a experiência de usuário Selecionar um escopo](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> A exclusão de Azure Monitor recursos exige que você os Desconecte primeiro de todos os objetos AMPLS aos quais eles estão conectados. Não é possível excluir recursos conectados a um AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Conectar-se a um ponto de extremidade privado

Agora que os recursos estão conectados a seu AMPLS, crie um ponto de extremidade privado para conectar nossa rede. Você pode realizar essa tarefa no [Centro de Links Privados do portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) ou no Escopo de Link Privado do Azure Monitor, conforme feito neste exemplo.

1. Em seu recurso de escopo, selecione **conexões de ponto de extremidade privado** no menu de recursos à esquerda. Selecione **ponto de extremidade privado** para iniciar o processo de criação do ponto de extremidade. Você também pode aprovar conexões que foram iniciadas no centro de links privado aqui selecionando-as e selecionando **aprovar**.

    ![Captura de tela da experiência de usuário Conexões de Ponto de Extremidade Privado](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Escolha a assinatura, o grupo de recursos, o nome do ponto de extremidade e a região em que ele deve ser alocado. A região precisa ser a mesma da rede virtual em que ocorrerá a conexão.

3. Selecione **Avançar: Recurso**. 

4. Na tela de recursos,

   a. Escolha a **Assinatura** que contém seu recurso de Escopo Privado do Azure Monitor. 

   b. Em **tipo de recurso**, escolha **Microsoft.insights/privateLinkScopes**. 

   c. Na lista suspensa de **recursos**, escolha o escopo de Link Privado que você criou anteriormente. 

   d. Selecione **Avançar: configuração >**.
      ![Captura de tela com a opção Criar Ponto de Extremidade privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. No painel de configuração,

   a.    Escolha a **rede virtual** e a **sub-rede** que você deseja conectar aos recursos do Azure Monitor. 
 
   b.    Na opção **Integrar à zona DNS privada**, escolha **Sim** e aguarde enquanto ela cria automaticamente uma Zona DNS Privada. As zonas DNS reais podem ser diferentes do que é mostrado na captura de tela abaixo. 
   > [!NOTE]
   > Se você escolher **não** e preferir gerenciar manualmente os registros DNS, primeiro conclua a configuração de seu link privado, incluindo esse ponto de extremidade privado e a configuração AMPLS. Em seguida, configure o DNS de acordo com as instruções em [configuração de DNS do ponto de extremidade privado do Azure](../../private-link/private-endpoint-dns.md). Certifique-se de não criar registros vazios como preparação para a configuração do seu link privado. Os registros DNS que você cria podem substituir as configurações existentes e impactar a conectividade com o Azure Monitor.
 
   c.    Selecione **Examinar + criar**.
 
   d.    Aguarde a aprovação de validação. 
 
   e.    Selecione **Criar**. 

    ![Captura de tela com a opção Criar Ponto de Extremidade Privado2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Agora você criou um novo ponto de extremidade privado que está conectado a este AMPLS.

## <a name="configure-log-analytics"></a>Configurar a análise de logs

Vá para o portal do Azure. No menu de recursos do espaço de trabalho Log Analytics, há um item chamado **isolamento de rede** no lado esquerdo. Nesse menu, é possível controlar dois estados diferentes.

![Isolamento da rede de LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Escopos de link privado Azure Monitor conectados
Todos os escopos conectados a este espaço de trabalho aparecem nesta tela. Conectar-se a escopos (AMPLSs) permite o tráfego de rede da rede virtual conectada a cada AMPLS para alcançar este espaço de trabalho. A criação de uma conexão aqui tem o mesmo efeito que configurá-la no escopo, como fizemos para [conectar Azure monitor recursos](#connect-azure-monitor-resources). Para adicionar uma nova conexão, selecione **Adicionar** e selecione o escopo do link Azure monitor privado. Selecione **aplicar** para conectar-se. Observe que um espaço de trabalho pode se conectar a 5 objetos AMPLS, conforme mencionado em [restrições e limitações](#restrictions-and-limitations). 

### <a name="access-from-outside-of-private-links-scopes"></a>Acesso de fora dos escopos de links privados
As configurações na parte inferior dessa página controlam o acesso de redes públicas, o que significa que as redes não estão conectadas por meio dos escopos listados acima. A configuração **permitir acesso à rede pública para ingestão** para **não** bloqueia a ingestão de logs de computadores fora dos escopos conectados. A configuração **permitir acesso à rede pública para consultas** para **não** bloquear consultas provenientes de computadores fora dos escopos. Isso inclui consultas executadas por meio de pastas de trabalho, painéis, experiências de cliente baseadas em API, informações no portal do Azure e muito mais. As experiências em execução fora do portal do Azure e essa consulta Log Analytics dados também precisam estar em execução na VNET vinculada ao privado.

### <a name="exceptions"></a>Exceções
Restringir o acesso, conforme explicado acima, não se aplica ao Azure Resource Manager e, portanto, tem as seguintes limitações:
* O acesso a dados-ao bloquear/permitir consultas de redes públicas se aplica à maioria das experiências de Log Analytics, algumas experiências consultam dados por meio de Azure Resource Manager e, portanto, não poderão consultar dados, a menos que as configurações de vínculo privado sejam aplicadas ao Gerenciador de recursos também (o recurso será lançado em breve). Isso inclui, por exemplo, soluções de Azure Monitor, pastas de trabalho e ideias e o conector do LogicApp.
* Gerenciamento de espaço de trabalho-configurações de espaço de trabalho e alterações de configuração (incluindo a ativação dessas configurações de acesso ativadas ou desativadas) são gerenciadas Azure Resource Manager pelo Restrinja o acesso ao gerenciamento de espaço de trabalho usando as funções, permissões, controles de rede e auditoria apropriados. Para obter mais informações, confira [Funções, permissões e segurança do Azure Monitor](roles-permissions-security.md).

> [!NOTE]
> Os logs e as métricas carregados em um workspace por meio das [Configurações de Diagnóstico](diagnostic-settings.md) passam por um canal privado e seguro da Microsoft e não são controlados por essas configurações.

### <a name="log-analytics-solution-packs-download"></a>Download de Log Analytics Solution packs

Para permitir que o agente do Log Analytics baixe pacotes de soluções, adicione os nomes de domínio totalmente qualificados à sua lista de permissões do firewall. 


| Ambiente de nuvem | Recurso de agente | Portas | Direção |
|:--|:--|:--|:--|
|Público do Azure     | scadvisorcontent.blob.core.windows.net         | 443 | Saída
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Saída
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Saída

## <a name="configure-application-insights"></a>Configurar o Application Insights

Vá para o portal do Azure. No Azure Monitor recurso de componente Application Insights, é um isolamento de **rede** de item de menu no lado esquerdo. Nesse menu, é possível controlar dois estados diferentes.

![Isolamento da rede de IA](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Primeiro, você pode conectar esse recurso do Application Insights aos escopos de Link Privado do Azure Monitor aos quais você tem acesso. Selecione **Adicionar** e selecione o **escopo de link Azure monitor privado**. Selecione aplicar para conectar-se. Todos os escopos conectados são exibidos nessa tela. Fazer essa conexão permite que o tráfego de rede nas redes virtuais conectadas alcance esse componente e tenha o mesmo efeito que conectá-lo do escopo como fizemos para [conectar Azure monitor recursos](#connect-azure-monitor-resources). 

Em segundo lugar, você pode controlar como esse recurso pode ser acessado de um local externo ao dos escopos de link privado listados anteriormente. Se você definir a opção **Permitir acesso à rede pública para ingestão** como **Não**, os computadores ou SDKs fora dos escopos conectados não poderão carregar os dados para esse componente. Se você definir a opção **Permitir acesso à rede pública para consultas** como **Não**, os computadores fora dos escopos não poderão acessar dados nesse recurso do Application Insights. Esses dados incluem acesso a logs de APM, métricas e fluxo de métricas em tempo real, além de experiências baseadas nesses parâmetros, como pastas de trabalho, dashboards, experiências de clientes baseadas em API de consulta, insights no portal do Azure e muito mais. 

Observe que as experiências de consumo fora do portal também precisam estar em execução na VNET vinculada privada que inclui as cargas de trabalho monitoradas. 

Será necessário adicionar ao link privado recursos que hospedem as cargas de trabalho monitoradas. Confira esta [documentação](../../app-service/networking/private-endpoint.md) para saber como fazer isso nos Serviços de Aplicativos.

Essa restrição de acesso se aplica apenas aos dados no recurso Application Insights. Quaisquer alterações, inclusive de ativação/desativação das configurações de acesso, são gerenciadas pelo Azure Resource Manager. Como alternativa, é possível restringir o acesso ao Resource Manager usando as funções, permissões, controles de rede e auditoria apropriados. Para obter mais informações, confira [Funções, permissões e segurança do Azure Monitor](roles-permissions-security.md).

> [!NOTE]
> Para proteger totalmente o Application Insights baseado no workspace, é necessário bloquear o acesso ao recurso do Application Insights e ao workspace subjacente do Log Analytics.
>
> O diagnóstico de nível de código (criador de perfil/depurador) precisa que você forneça sua própria conta de armazenamento para dar suporte ao link privado. Aqui está a [documentação](../app/profiler-bring-your-own-storage.md) para saber como fazer isso.

## <a name="use-apis-and-command-line"></a>Usar APIs e linha de comando

Você pode automatizar o processo descrito anteriormente usando modelos de Azure Resource Manager, REST e interfaces de linha de comando.

Para criar e gerenciar escopos de link privado, use a [API REST](/rest/api/monitor/private%20link%20scopes%20(preview)) ou [CLI do Azure (AZ monitor privado-link-Scope)](/cli/azure/monitor/private-link-scope).

Para gerenciar o acesso à rede, use os sinalizadores `[--ingestion-access {Disabled, Enabled}]` e `[--query-access {Disabled, Enabled}]` nos [Workspaces do Log Analytics](/cli/azure/monitor/log-analytics/workspace) ou [Componentes do Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Coletar logs personalizados e log do IIS sobre o link privado

As contas de armazenamento são usadas no processo de ingestão de logs personalizados. Por padrão, são usadas contas de armazenamento gerenciadas por serviços. No entanto, para ingerir logs personalizados em links privados, você deve usar suas contas de armazenamento e associá-las aos workspaces do Log Analytics. Confira mais detalhes sobre como configurar essas contas usando a [linha de comando](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Para obter mais informações sobre como associar sua conta de armazenamento, confira [Contas de armazenamento de propriedade do cliente para ingestão de log](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrições e limitações

### <a name="ampls"></a>AMPLS
O objeto AMPLS tem vários limites que você deve considerar ao planejar sua configuração de link privado:

* Uma VNet só pode se conectar a um objeto AMPLS. Isso significa que o objeto AMPLS deve fornecer acesso a todos os Azure Monitor recursos aos quais a VNet deve ter acesso.
* Um recurso de Azure Monitor (espaço de trabalho ou componente Application Insights) pode se conectar a 5 AMPLSs no máximo.
* Um objeto AMPLS pode se conectar a 50 Azure Monitor recursos no máximo.
* Um objeto AMPLS pode se conectar a 10 pontos de extremidade privados no máximo.

Veja [considerar os limites](#consider-limits) para uma análise mais profunda desses limites e como planejar sua configuração de link privado de forma adequada.

### <a name="agents"></a>Agentes

As versões mais recentes dos agentes do Windows e do Linux devem ser usadas em redes privadas para habilitar a ingestão segura para Log Analytics espaços de trabalho. As versões mais antigas não conseguem carregar dados de monitoramento em uma rede privada.

**Agente Windows do Log Analytics**

Use o agente de Log Analytics versão 10.20.18038.0 ou posterior.

**Agente Linux do Log Analytics**

Use o agente na versão 1.12.25 ou posterior. Se isso não for possível, execute os comandos a seguir na sua VM.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portal do Azure

Para usar as experiências do portal do Azure Monitor, como Application Insights e Log Analytics, você precisa permitir que o portal do Azure e as extensões do Azure Monitor estejam acessíveis nas redes privadas. Adicione as [marcas de serviço](../../firewall/service-tags.md) **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** e **AzureFrontDoor. frontend** ao seu grupo de segurança de rede.

### <a name="programmatic-access"></a>Acesso Programático

Para usar API REST, [CLI](/cli/azure/monitor) ou PowerShell com o Azure Monitor em redes privadas, adicione as [marcas de serviço](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** e **AzureResourceManager** ao seu firewall.

A adição dessas marcas permite executar ações como: consulta de dados de log, criação e gerenciamento de workspaces do Log Analytics e componentes de IA.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Downloads do SDK do Application Insights em uma rede de distribuição de conteúdo

Agrupe o código JavaScript no seu script para que o navegador não tente baixar o código de uma CDN. Confira um exemplo no [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Configurações de DNS do navegador

Se você estiver se conectando aos recursos do Azure Monitor por meio de um link privado, o tráfego para esses recursos deverá passar pelo ponto de extremidade privado configurado em sua rede. Para habilitar o ponto de extremidade privado, atualize suas configurações de DNS conforme explicado em [conectar-se a um ponto de extremidade privado](#connect-to-a-private-endpoint). Alguns navegadores usam suas próprias configurações de DNS em vez daqueles que você definiu. O navegador pode tentar se conectar a Azure Monitor pontos de extremidade públicos e ignorar totalmente o link privado. Verifique se as configurações de navegadores não substituem ou armazenam configurações de DNS antigas. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [armazenamento privado](private-storage.md)