---
title: Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor
description: Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 637e66956eadf57199d2e5191368d6355e2cd118
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941898"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor

O [Link Privado do Azure](../../private-link/private-link-overview.md) permite vincular com segurança os serviços PaaS do Azure a sua rede virtual usando pontos de extremidade privados. Em muitos serviços, basta configurar um ponto de extremidade por recurso. Já o Azure Monitor é uma constelação de diferentes serviços interconectados que trabalham juntos para monitorar suas cargas de trabalho. Como resultado, criamos um recurso chamado AMPLS (Escopo de Link Privado do Azure Monitor) com o qual você pode definir os limites da sua rede de monitoramento e conectar-se à sua rede virtual. Este artigo aborda quando usar e como configurar um Escopo de Link Privado do Azure Monitor.

## <a name="advantages"></a>Vantagens

Com o Link Privado, você pode:

- Conectar-se de maneira privada ao Azure Monitor sem abrir nenhum acesso de rede pública
- Garantir que o acesso aos seus dados de monitoramento esteja restrito a redes privadas autorizadas
- Impedir a exportação de dados de suas redes privadas definindo recursos específicos do Azure Monitor que se conectam por meio de seu ponto de extremidade privado
- Conectar sua rede local privada ao Azure Monitor com segurança usando ExpressRoute e Link Privado
- Manter todo o tráfego dentro da rede de backbone do Microsoft Azure

Para mais informações, confira [Principais benefícios do Link Privado](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Como ele funciona

O Escopo de Link Privado do Azure Monitor é um recurso de agrupamento que conecta um ou mais pontos de extremidade privado (e, consequentemente, as redes virtuais nas quais eles estão contidos) a um ou mais recursos do Azure Monitor. Os recursos incluem workspaces do Log Analytics e componentes do Application Insights.

![Diagrama da topologia de recursos](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Um único recurso do Azure Monitor pode pertencer a vários AMPLSs, mas não é possível conectar uma única VNet a mais de um AMPLS. 

## <a name="planning-based-on-your-network"></a>Planejamento com base na sua rede

Antes de configurar seus recursos do AMPLS, verifique seus requisitos de isolamento de rede. Avalie o acesso de suas redes virtuais à Internet pública e as restrições de acesso de cada um dos seus recursos do Azure Monitor (ou seja, componentes do Application Insights e workspaces do Log Analytics).

> [!NOTE]
> As redes hub-spoke, ou qualquer outra topologia de redes emparelhadas, podem configurar um link privado entre a VNet do Hub (principal) e os recursos relevantes do Azure Monitor, em vez de configurar um link privado em cada VNet. Isso faz sentido especialmente se os recursos de Azure Monitor usados por essas redes forem compartilhados. No entanto, se você quiser permitir que cada VNet acesse um conjunto separado de recursos de monitoramento, crie um link privado para um AMPLS dedicado para cada rede.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Avaliar quais redes virtuais devem se conectar a um Link Privado

Para começar, analise quais de suas VNets (redes virtuais) têm acesso restrito à Internet. As VNets com internet gratuita podem não exigir um Link Privado para acessar os recursos do Azure Monitor. Os recursos de monitoramento aos quais suas VNets se conectam podem restringir o tráfego recebido e exigir uma conexão de Link Privado (para consulta ou ingestão de log). Nesses casos, mesmo que a VNet tenha acesso à Internet pública, ela precisa se conectar a esses recursos por meio de um Link Privado e de um AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Avaliar quais recursos do Azure Monitor devem ter um Link Privado

Revise cada um dos recursos do Azure Monitor:

- O recurso deve permitir a ingestão de logs de recursos localizados apenas em VNets específicas?
- O recurso deve ser consultado apenas por clientes localizados em VNETs específicas?

Se a resposta for sim para qualquer uma dessas perguntas, defina as restrições conforme explicado em [Configurar workspaces do Log Analytics](#configure-log-analytics) e [Configurar componentes do Application Insights](#configure-application-insights) e associe esses recursos a um ou vários AMPLS(s). As redes virtuais que devem acessar esses recursos de monitoramento precisam ter um ponto de extremidade privado que se conecte ao AMPLS relevante.
Lembre-se: você pode conectar os mesmos workspaces ou aplicativos a vários AMPLS, para permitir que eles sejam acessados por redes diferentes.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Agrupar recursos de monitoramento por acessibilidade de rede

Como cada VNet pode se conectar a apenas um recurso de AMPLS, você deve agrupar recursos de monitoramento que deverão estar acessíveis para as mesmas redes. A maneira mais simples de gerenciar esse agrupamento é criando um AMPLS por VNet e selecionando os recursos que se conectarão a essa rede. No entanto, para reduzir recursos e melhorar a capacidade de gerenciamento, convém reutilizar um AMPLS nas redes.

Por exemplo, se você quiser que as redes virtuais internas VNet1 e VNet2 se conectem ao Workspace1 e Workspace2 e ao componente Application Insights3, você deverá associar os três recursos ao mesmo AMPLS. Se você quiser que a VNet3 acesse apenas o Workspace1, será necessário criar outro recurso de AMPLS, associar o Workspace1 a ele e conectar a VNet3 conforme mostrado nos seguintes diagramas:

![Diagrama da topologia do AMPLS A](./media/private-link-security/ampls-topology-a-1.png)

![Diagrama da topologia do AMPLS B](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>Considerar limites

Há vários limites que você deve considerar ao planejar sua configuração de link privado:

* Uma VNet só pode se conectar a um objeto AMPLS. Isso significa que o objeto AMPLS deve fornecer acesso a todos os Azure Monitor recursos aos quais a VNet deve ter acesso.
* Um recurso de Azure Monitor (espaço de trabalho ou componente Application Insights) pode se conectar a 5 AMPLSs no máximo.
* Um objeto AMPLS pode se conectar a 50 Azure Monitor recursos no máximo.
* Um objeto AMPLS pode se conectar a 10 pontos de extremidade privados no máximo.

Na topologia abaixo:
* Cada VNet se conecta a apenas **um** objeto AMPLS.
* O AMPLS B está conectado a pontos de extremidade privados de dois VNets (VNet2 e VNet3), usando 2/10 (20%) de suas possíveis conexões de ponto de extremidade privado.
* O AMPLS A se conecta a dois espaços de trabalho e a um componente do Application Insight, usando 3/50 (6%) de suas conexões de recursos Azure Monitor possíveis.
* Workspace2 conecta-se ao AMPLS A e AMPLS B, usando 2/5 (40%) de suas conexões AMPLS possíveis.

![Diagrama de limites de AMPLS](./media/private-link-security/ampls-limits.png)

> [!NOTE]
> Em algumas topologias de rede (principalmente hub-spoke), você pode alcançar rapidamente o limite de 10 VNets para um único AMPLS. Nesses casos, é aconselhável usar uma conexão de link privado compartilhado em vez de separar aquelas. Crie um único ponto de extremidade privado na rede de Hub, vincule-o ao seu AMPLS e emparelhe as redes relevantes para a rede de Hub.

![Hub-e-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

## <a name="example-connection"></a>Exemplo de conexão

Comece criando um recurso de Escopo de Link Privado do Azure Monitor.

1. No portal do Azure, acesse **Criar um recurso** e procure por **Escopo de Link Privado do Azure Monitor**.

   ![Localizar Azure Monitor escopo de link privado](./media/private-link-security/ampls-find-1c.png)

2. Clique em **Criar**.
3. Escolha uma assinatura e um grupo de recursos.
4. Nomeie o AMPLS. Recomendamos usar um nome que esclareça a finalidade e o limite de segurança definidos para o uso do Escopo, de modo que ninguém viole acidentalmente os limites de segurança da rede. Por exemplo, "TelemProdServAplic".
5. Clique em **Examinar + Criar**. 

   ![Criar Azure Monitor escopo de link privado](./media/private-link-security/ampls-create-1d.png)

6. Espere a validação ser aprovada e clique em **Criar**.

### <a name="connect-azure-monitor-resources"></a>Conectar recursos do Azure Monitor

Conecte Azure Monitor recursos (Log Analytics espaços de trabalho e componentes do Application Insights) ao seu AMPLS.

1. No Escopo de Link Privado do Azure Monitor, clique em **Recursos do Azure Monitor**, no menu à esquerda. Clique no botão **Adicionar** .
2. Adicione o workspace ou componente. Clique em **Adicionar** para exibir a caixa de diálogo na qual você poderá selecionar os recursos do Azure Monitor. Você pode navegar por suas assinaturas e seus grupos de recursos ou digitar o nome deles para filtrá-los. Selecione o workspace ou componente e clique em **Aplicar** para adicioná-los ao seu escopo.

    ![Captura de tela com a experiência de usuário Selecionar um escopo](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> A exclusão de Azure Monitor recursos exige que você os Desconecte primeiro de todos os objetos AMPLS aos quais eles estão conectados. Não é possível excluir recursos conectados a um AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Conectar-se a um ponto de extremidade privado

Agora que os recursos estão conectados a seu AMPLS, crie um ponto de extremidade privado para conectar nossa rede. Você pode realizar essa tarefa no [Centro de Links Privados do portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) ou no Escopo de Link Privado do Azure Monitor, conforme feito neste exemplo.

1. No seu recurso de escopo, clique em **Conexões de ponto de extremidade privado**, no menu de recursos à esquerda. Clique em **Ponto de extremidade privado** para iniciar o processo de criação. Você também pode aprovar as conexões iniciadas no Centro de Links Privados aqui. Basta selecioná-las e clicar em **Aprovar**.

    ![Captura de tela da experiência de usuário Conexões de Ponto de Extremidade Privado](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Escolha a assinatura, o grupo de recursos, o nome do ponto de extremidade e a região em que ele deve ser alocado. A região precisa ser a mesma da rede virtual em que ocorrerá a conexão.

3. Clique em **Avançar: Recurso**. 

4. Na tela de recursos,

   a. Escolha a **Assinatura** que contém seu recurso de Escopo Privado do Azure Monitor. 

   b. Em **tipo de recurso**, escolha **Microsoft.insights/privateLinkScopes**. 

   c. Na lista suspensa de **recursos**, escolha o escopo de Link Privado que você criou anteriormente. 

   d. Clique em **Avançar: Configuração >** .
      ![Captura de tela com a opção Criar Ponto de Extremidade privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. No painel de configuração,

   a.    Escolha a **rede virtual** e a **sub-rede** que você deseja conectar aos recursos do Azure Monitor. 
 
   b.    Na opção **Integrar à zona DNS privada**, escolha **Sim** e aguarde enquanto ela cria automaticamente uma Zona DNS Privada. As zonas DNS reais podem ser diferentes do que é mostrado na captura de tela abaixo. 
   > [!NOTE]
   > Se você escolher **não** e preferir gerenciar manualmente os registros DNS, primeiro conclua a configuração de seu link privado, incluindo esse ponto de extremidade privado e a configuração AMPLS. Em seguida, configure o DNS de acordo com as instruções em [configuração de DNS do ponto de extremidade privado do Azure](../../private-link/private-endpoint-dns.md). Certifique-se de não criar registros vazios como preparação para a configuração do seu link privado. Os registros DNS que você cria podem substituir as configurações existentes e impactar a conectividade com o Azure Monitor.
 
   c.    Clique em **Revisar + Criar**.
 
   d.    Aguarde a aprovação de validação. 
 
   e.    Clique em **Criar**. 

    ![Captura de tela com a opção Criar Ponto de Extremidade Privado2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Você criou um ponto de extremidade privado conectado ao Escopo de Link Privado do Azure Monitor.

## <a name="configure-log-analytics"></a>Configurar a análise de logs

Vá para o portal do Azure. No recurso de espaço de trabalho Log Analytics há um **isolamento de rede** de item de menu no lado esquerdo. Nesse menu, é possível controlar dois estados diferentes.

![Isolamento da rede de LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Escopos de link privado Azure Monitor conectados
Todos os escopos conectados a este espaço de trabalho aparecem nesta tela. Conectar-se a escopos (AMPLSs) permite o tráfego de rede da rede virtual conectada a cada AMPLS para alcançar este espaço de trabalho. A criação de uma conexão aqui tem o mesmo efeito que configurá-la no escopo, como fizemos para [conectar Azure monitor recursos](#connect-azure-monitor-resources). Para adicionar uma nova conexão, clique em **Adicionar** e selecione o escopo do link Azure monitor privado. Clique em **Aplicar** para conectá-lo. Observe que um espaço de trabalho pode se conectar a 5 objetos AMPLS, conforme explicado em [considerar os limites](#consider-limits). 

### <a name="access-from-outside-of-private-links-scopes"></a>Acesso de fora dos escopos de links privados
As configurações na parte inferior dessa página controlam o acesso de redes públicas, o que significa que as redes não estão conectadas por meio dos escopos listados acima. Se você definir a opção **Permitir acesso à rede pública para ingestão** como **Não**, os computadores fora dos escopos conectados não poderão carregar dados nesse workspace. Se você definir **permitir acesso de rede pública para consultas** como **não**, os computadores fora dos escopos não poderão acessar dados nesse espaço de trabalho, o que significa que não será possível consultar os dados do espaço de trabalho. Isso inclui consultas em pastas de trabalho, painéis, experiências de cliente baseadas em API, informações no portal do Azure e muito mais. As experiências em execução fora do portal do Azure e essa consulta Log Analytics dados também precisam estar em execução na VNET vinculada ao privado.

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

Vá para o portal do Azure. No recurso do componente do Application Insights do Azure Monitor, há um item de menu do lado esquerdo chamado **Isolamento de Rede**. Nesse menu, é possível controlar dois estados diferentes.

![Isolamento da rede de IA](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Primeiro, você pode conectar esse recurso do Application Insights aos escopos de Link Privado do Azure Monitor aos quais você tem acesso. Clique em **Adicionar** e selecione o **Escopo de Link Privado do Azure Monitor**. Clique em Aplicar para conectá-lo. Todos os escopos conectados são exibidos nessa tela. Essa conexão permite que o tráfego nas redes virtuais conectadas acessem esse componente. Essa conexão tem o mesmo efeito que o executado por meio do escopo, como fizemos em [Conectar recursos do Azure Monitor](#connect-azure-monitor-resources). 

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

## <a name="collect-custom-logs-over-private-link"></a>Coletar logs personalizados sobre o link privado

As contas de armazenamento são usadas no processo de ingestão de logs personalizados. Por padrão, são usadas contas de armazenamento gerenciadas por serviços. No entanto, para ingerir logs personalizados em links privados, você deve usar suas contas de armazenamento e associá-las aos workspaces do Log Analytics. Confira mais detalhes sobre como configurar essas contas usando a [linha de comando](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Para obter mais informações sobre como associar sua conta de armazenamento, confira [Contas de armazenamento de propriedade do cliente para ingestão de log](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrições e limitações

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
