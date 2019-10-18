---
title: Conectar Operations Manager ao Azure Monitor | Microsoft Docs
description: Para manter seu investimento existente em System Center Operations Manager e usar recursos estendidos com o Log Analytics, você pode integrar Operations Manager ao seu espaço de trabalho.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: magoedte
ms.openlocfilehash: c8d6e949722e291eab4ac45f6abb610acfa10d68
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532393"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Conectar Operations Manager ao Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para manter seu investimento existente em [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) e usar recursos estendidos com o Azure monitor, você pode integrar Operations Manager ao seu espaço de trabalho do log Analytics. Isso permite que você aproveite as oportunidades de logs em Azure Monitor enquanto continua a usar Operations Manager para:

* Monitore a integridade dos seus serviços de ti com Operations Manager
* Manter a integração com suas soluções de ITSM dando suporte ao gerenciamento de incidentes e problemas
* Gerenciar o ciclo de vida dos agentes implantados em máquinas virtuais IaaS de nuvem pública e local que você monitora com Operations Manager

A integração com o System Center Operations Manager agrega valor à sua estratégia de operações de serviço usando a velocidade e a eficiência de Azure Monitor na coleta, no armazenamento e na análise de dados de log de Operations Manager. Azure Monitor consultas de log ajudam a correlacionar e trabalhar para identificar as falhas de problemas e identificando recorrências no suporte do processo de gerenciamento de problemas existente. A flexibilidade do mecanismo de consulta para examinar dados de desempenho, eventos e alertas, com painéis avançados e recursos de relatório para expor esses dados de maneiras significativas, demonstra a força que o Azure Monitor traz para complementar Operations Manager.

Os agentes que se reportam para o grupo de gerenciamento de Operations Manager coletam dados de seus servidores com base nas [fontes log Analytics de dados](agent-data-sources.md) e soluções que você habilitou em seu espaço de trabalho. Dependendo das soluções habilitadas, seus dados são enviados diretamente de um servidor de gerenciamento de Operations Manager para o serviço ou devido ao volume de dados coletados no sistema gerenciado por agente, são enviados diretamente do agente para um espaço de trabalho Log Analytics. O servidor de gerenciamento encaminha os dados diretamente para o serviço; Ele nunca é gravado no banco de dados operacional ou data warehouse. Quando um servidor de gerenciamento perde a conectividade com Azure Monitor, ele armazena os dados em cache localmente até que a comunicação seja restabelecida. Se o servidor de gerenciamento estiver offline devido à manutenção planejada ou à interrupção não planejada, outro servidor de gerenciamento no grupo de gerenciamento retomará a conectividade com Azure Monitor.  

O diagrama a seguir mostra a conexão entre os servidores de gerenciamento e os agentes em um System Center Operations Manager grupo de gerenciamento e Azure Monitor, incluindo a direção e as portas.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se suas políticas de segurança de ti não permitirem que computadores em sua rede se conectem à Internet, os servidores de gerenciamento poderão ser configurados para se conectarem ao gateway de Log Analytics para receber informações de configuração e enviar os dados coletados dependendo das soluções habilitado. Para obter mais informações e etapas sobre como configurar seu grupo de gerenciamento de Operations Manager para se comunicar por meio de um gateway de Log Analytics para Azure Monitor, consulte [conectar computadores ao Azure monitor usando o gateway log Analytics](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, examine os requisitos a seguir.

* Azure Monitor só dá suporte a System Center Operations Manager 2016 ou posterior, Operations Manager 2012 SP1 UR6 ou posterior e Operations Manager 2012 R2 UR2 ou posterior. Foi adicionado suporte a proxy ao Operations Manager 2012 SP1 UR7 e Operations Manager 2012 R2 UR3.
* A integração do System Center Operations Manager 2016 com a nuvem do governo dos EUA requer um pacote de gerenciamento do Advisor atualizado incluído no pacote cumulativo de atualizações 2 ou posterior. O System Center Operations Manager 2012 R2 requer um pacote de gerenciamento do Advisor atualizado incluído com o pacote cumulativo de atualizações 3 ou posterior.
* Todos os agentes de Operations Manager devem atender aos requisitos mínimos de suporte. Verifique se os agentes estão na atualização mínima, caso contrário, a comunicação do agente do Windows pode falhar e gerar erros no log de eventos do Operations Manager.
* Um espaço de trabalho Log Analytics. Para obter mais informações, examine [log Analytics visão geral do espaço de trabalho](design-logs-deployment.md). 
* Você se autentica no Azure com uma conta que seja membro da [função colaborador de log Analytics](manage-access.md#manage-access-using-azure-permissions).

* Regiões com suporte – somente as seguintes regiões do Azure têm suporte pelo System Center Operations Manager para se conectar a um espaço de trabalho do Log Analytics:
    - Centro-Oeste dos EUA
    - Sudeste da Austrália
    - Oeste da Europa
    - Leste dos EUA
    - Sudeste Asiático
    - Leste do Japão
    - Sul do Reino Unido
    - Índia Central
    - Canadá Central
    - Oeste dos EUA 2

>[!NOTE]
>As alterações recentes nas APIs do Azure impedirão que os clientes consigam configurar a integração com êxito entre o grupo de gerenciamento e Azure Monitor pela primeira vez. Para os clientes que já tiverem integrado o grupo de gerenciamento com o serviço, você não será afetado, a menos que precise reconfigurar a conexão existente.  
>Um novo pacote de gerenciamento foi lançado para as seguintes versões do Operations Manager:
> - Para System Center Operations Manager 2019, esse pacote de gerenciamento é incluído na mídia de origem e instalado durante a instalação de um novo grupo de gerenciamento ou durante uma atualização.
>- Operations Manager pacote de gerenciamento 1801 também é aplicável para Operations Manager 1807.
>- Para System Center Operations Manager 1801, baixe o pacote de gerenciamento [aqui](https://www.microsoft.com/download/details.aspx?id=57173).
>- Para o System Center 2016-Operations Manager, baixe o pacote de gerenciamento [aqui](https://www.microsoft.com/download/details.aspx?id=57172).  
>- Para System Center Operations Manager 2012 R2, baixe o pacote de gerenciamento [aqui](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Rede

As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente de Operations Manager, os servidores de gerenciamento e o console de operações se comuniquem com Azure Monitor. O tráfego de cada componente é de saída de sua rede para Azure Monitor.

|Grupos | Número da porta| Ignorar inspeção HTTP|  
|---------|------|-----------------------|  
|**Agente**|||  
|\*.ods.opinsights.azure.com| 443 |Sim|  
|\*.oms.opinsights.azure.com| 443|Sim|  
|\*.blob.core.windows.net| 443|Sim|  
|\*. azure-automation.net| 443|Sim|  
|**Servidor de gerenciamento**|||  
|\*. service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Sim|  
|\*.ods.opinsights.azure.com| 443| Sim|  
|*. azure-automation.net | 443| Sim|  
|**Operations Manager Console para Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*. service.opinsights.azure.com| 443||  
|\*.live.com| 80 e 443||  
|\*.microsoft.com| 80 e 443||  
|\*.microsoftonline.com| 80 e 443||  
|\*.mms.microsoft.com| 80 e 443||  
|login.windows.net| 80 e 443||  
|portal.loganalytics.io| 80 e 443||
|api.loganalytics.io| 80 e 443||
|docs.loganalytics.io| 80 e 443||  

### <a name="tls-12-protocol"></a>Protocolo TLS 1,2

Para garantir a segurança dos dados em trânsito para Azure Monitor, é altamente recomendável que você configure o agente e o grupo de gerenciamento para usar pelo menos o protocolo TLS (segurança de camada de transporte) 1,2. Versões mais antigas do TLS/protocolo SSL (SSL) foram considerados vulneráveis e, embora ainda funcionem para permitir a compatibilidade com versões anteriores, elas não são **recomendadas**. Para obter informações adicionais, examine o [envio de dados com segurança usando o TLS 1,2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Conectando Operations Manager ao Azure Monitor

Execute a seguinte série de etapas para configurar seu grupo de gerenciamento de Operations Manager para se conectar a um dos seus espaços de trabalho do Log Analytics.

Durante o registro inicial de seu grupo de gerenciamento de Operations Manager com um espaço de trabalho Log Analytics, a opção de especificar a configuração de proxy para o grupo de gerenciamento não está disponível no console de operações.  O grupo de gerenciamento deve ser registrado com êxito no serviço antes que essa opção esteja disponível.  Para contornar isso, você precisa atualizar a configuração de proxy do sistema usando o netsh no sistema que executa o console de operações do para configurar a integração e todos os servidores de gerenciamento no grupo de gerenciamento.  

1. Abra um prompt de comando elevado.
   a. Vá para **Iniciar** e digite **cmd**.
   b. Clique com o botão direito do mouse em **prompt de comando** e selecione executar como administrador * *.
1. Digite o seguinte comando e pressione **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir as etapas a seguir para integrar com o Azure Monitor, você pode remover a configuração executando `netsh winhttp reset proxy` e, em seguida, usar a opção **Configurar servidor proxy** no console de operações para especificar o proxy ou o servidor de gateway de log Analytics.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Expanda o nó Operations Management Suite e clique em **conexão**.
1. Clique no link **Registrar-se no Operations Management Suite** .
1. Na página **Assistente de Integração do Operations Management Suite: autenticação**, insira o endereço de email ou o número de telefone e a senha da conta de administrador que está associada à sua assinatura do OMS e clique em **Entrar**.

   >[!NOTE]
   >O nome do Operations Management Suite foi desativado.

1. Depois que você for autenticado com êxito, na página **Assistente de integração do Operations Management Suite: selecionar espaço de trabalho** , será solicitado que você selecione seu locatário do Azure, sua assinatura e o espaço de trabalho do log Analytics. Se você tiver mais de um workspace, selecione aquele em que você deseja registrar-se com o grupo de gerenciamento do Operations Manager da lista suspensa e clique em **Avançar**.

   > [!NOTE]
   > Operations Manager só dá suporte a um espaço de trabalho de Log Analytics de cada vez. A conexão e os computadores que foram registrados para Azure Monitor com o espaço de trabalho anterior são removidos do Azure Monitor.
   >
   >
1. Na página **Assistente de Integração do Operations Management Suite: resumo**, confirme suas configurações e, se elas estiverem corretas, clique em **Criar**.
1. Na página **Assistente de integração do Operations Management Suite: concluir** , clique em **fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores gerenciados por agente

Depois de configurar a integração com seu espaço de trabalho do Log Analytics, ele estabelece apenas uma conexão com o serviço, nenhum dado é coletado dos agentes subordinados ao seu grupo de gerenciamento. Isso só acontecerá depois que você configurar quais computadores gerenciados por agente específicos coletam dados de log para Azure Monitor. Você pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contenha objetos de computador do Windows. Não é possível selecionar um grupo que contenha instâncias de outra classe, como discos lógicos ou bancos de dados SQL.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Expanda o nó Operations Management Suite e clique em **conexão**.
1. Clique no link **Adicionar um Computador/Grupo** no cabeçalho Ações no lado direito do painel.
1. Na caixa de diálogo **pesquisa de computador** , você pode pesquisar computadores ou grupos monitorados pelo Operations Manager. Selecione computadores ou grupos, incluindo o servidor de gerenciamento de Operations Manager para carregar Azure Monitor, clique em **Adicionar**e, em seguida, clique em **OK**.

Você pode exibir computadores e grupos configurados para coletar dados do nó Computadores Gerenciados no Operations Management Suite no workspace **Administração** do Console de operações. A partir daqui, você pode adicionar ou remover computadores e grupos conforme necessário.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Definir configurações de proxy no console de operações

Execute as etapas a seguir se um servidor proxy interno estiver entre o grupo de gerenciamento e Azure Monitor. Essas configurações são gerenciadas centralmente do grupo de gerenciamento e distribuídas para sistemas gerenciados por agente que estão incluídos no escopo para coletar dados de log para Azure Monitor.  Isso é benéfico quando determinadas soluções ignoram o servidor de gerenciamento e enviam dados diretamente para o serviço.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Expanda Operations Management Suite e clique em **conexões**.
1. Na exibição Conexão do OMS, clique em **Configurar Servidor Proxy**.
1. Na página **Assistente do Operations Management Suite: servidor proxy** , selecione **usar um servidor proxy para acessar o Operations Management Suite**e digite a URL com o número da porta, por exemplo, http://corpproxy:80 e clique em **concluir**.

Se o servidor proxy exigir autenticação, execute as etapas a seguir para configurar as credenciais e as configurações que precisam ser propagadas para os computadores gerenciados que os relatórios Azure Monitor no grupo de gerenciamento.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Em **configuração runas**, selecione **perfis**.
1. Abra o perfil **System Center Advisor executado como Proxy de perfil** .
1. No assistente de perfil executar como, clique em Adicionar para usar uma conta Executar como. Você pode criar uma [conta Executar como](https://technet.microsoft.com/library/hh321655.aspx) ou usar uma conta existente. Essa conta precisa ter permissões suficientes para passar pelo servidor proxy.
1. Para definir a conta a ser gerenciada, escolha **uma classe, grupo ou objeto selecionado**, clique em **selecionar..** . e, em seguida, clique em **grupo...** para abrir a caixa de **pesquisa de grupo** .
1. Procure e selecione o **Grupo de Servidores de Monitoramento do Microsoft System Center Advisor**. Clique em **OK** depois de selecionar o grupo para fechar a caixa de **pesquisa de grupo** .
1. Clique em **OK** para fechar a caixa **Adicionar uma conta Executar como** .
1. Clique em **Salvar** para concluir o assistente e salvar suas alterações.

Depois que a conexão é criada e você configura quais agentes coletarão e relatarão dados de log para Azure Monitor, a configuração a seguir será aplicada no grupo de gerenciamento, não necessariamente na ordem:

* A conta Executar como **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** é criada. Ele está associado ao perfil executar como **blob do perfil executar como do Microsoft System Center Advisor** e tem como alvo duas classes – **servidor de coleta** e **Operations Manager grupo de gerenciamento**.
* Dois conectores são criados.  O primeiro é chamado de **Microsoft. SystemCenter. Advisor. Dataconnecter** e é configurado automaticamente com uma assinatura que encaminha todos os alertas gerados de instâncias de todas as classes no grupo de gerenciamento para Azure monitor. O segundo conector é o **Advisor Connector**, que é responsável por se comunicar com Azure monitor e compartilhar dados.
* Os agentes e grupos que você selecionou para coletar dados no grupo de gerenciamento são adicionados ao **grupo de servidores de monitoramento do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações do pacote de gerenciamento

Após a conclusão da configuração, o grupo de gerenciamento Operations Manager estabelece uma conexão com Azure Monitor. O servidor de gerenciamento do sincroniza com o serviço Web e recebe informações de configuração atualizadas na forma de pacotes de gerenciamento para as soluções que você habilitou que se integram ao Operations Manager. Operations Manager verifica se há atualizações desses pacotes de gerenciamento e os importa automaticamente quando estão disponíveis. Há duas regras em particular que controlam esse comportamento:

* **Microsoft. SystemCenter. Advisor. MPUpdate** -atualiza os pacotes de gerenciamento de Azure monitor base. É executado a cada 12 horas por padrão.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - atualiza os pacotes de gerenciamento de solução habilitados no seu workspace. É executado a cada cinco (5) minutos por padrão.

Você pode substituir essas duas regras para impedir o download automático desabilitando-os ou modificar a frequência com que o servidor de gerenciamento é sincronizado com Azure Monitor para determinar se um novo pacote de gerenciamento está disponível e deve ser baixado. Siga as etapas [como substituir uma regra ou monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar o parâmetro de **frequência** com um valor em segundos para alterar a agenda de sincronização ou modificar o parâmetro **habilitado** para desabilitar as regras. Direcionar as substituições a todos os objetos da classe Grupo de Gerenciamento do Operations Manager.

Para continuar seguindo o processo de controle de alterações existente para controlar as versões do pacote de gerenciamento em seu grupo de gerenciamento de produção, você pode desabilitar as regras e habilitá-las durante horários específicos quando as atualizações forem permitidas. Se você tiver um grupo de gerenciamento de qualidade ou desenvolvimento em seu ambiente e ele tiver conectividade com a Internet, você poderá configurar esse grupo de gerenciamento com um espaço de trabalho Log Analytics para dar suporte a esse cenário. Isso permite que você examine e avalie as versões iterativas dos pacotes de gerenciamento do Azure Monitor antes de liberá-los para o grupo de gerenciamento de produção.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Alternar um grupo de Operations Manager para um novo espaço de trabalho Log Analytics

1. Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. Na portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **log Analytics** e, em seguida, crie um espaço de trabalho.  
1. Abra o console do Operations Manager com uma conta que seja membro da função Administradores do Operations Manager e selecione o workspace **Administração**.
1. Expanda Log Analytics e selecione **conexões**.
1. Selecione o link **Reconfigurar o Operation Management Suite** no meio do painel.
1. Siga o **Assistente de integração de log Analytics** e insira o endereço de email ou o número de telefone e a senha da conta de administrador que está associada ao seu novo espaço de trabalho do log Analytics.

   > [!NOTE]
   > O **Assistente de integração do Operations Management Suite: a página Selecionar espaço de trabalho** apresenta o espaço de trabalho existente que está em uso.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Validar a integração de Operations Manager com o Azure Monitor

Há algumas maneiras diferentes de verificar se Azure Monitor Operations Manager integração foi bem-sucedida.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Para confirmar a integração do portal do Azure

1. Na portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada.
1. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho aplicável.  
1. Selecione **Configurações avançadas**, selecione **fontes conectadas**e, em seguida, selecione **System Center**.
1. Na tabela da seção System Center Operations Manager, você deve ver o nome do grupo de gerenciamento listado com o número de agentes e status de quando os dados foram recebidos pela última vez.

   ![OMS-configurações-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração do console de operações

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Clique em **Pacotes de Gerenciamento** e, na caixa de texto **Procurar por:** , digite **Advisor** ou **Intelligence**.
1. Dependendo das soluções que você habilitou, você verá um pacote de gerenciamento correspondente listado nos resultados da pesquisa.  Por exemplo, se você habilitou a solução Gerenciamento de Alertas, o pacote de gerenciamento Microsoft System Center Advisor Gerenciamento de Alertas está na lista.
1. Da exibição **Monitoramento**, navegue até a exibição **Operations Management Suite\Estado de Integridade**.  Selecione um servidor de gerenciamento no painel **estado do servidor de gerenciamento** e, no painel exibição de **detalhes** , confirme se o valor do URI do **serviço de autenticação** de propriedade corresponde à ID do espaço de trabalho log Analytics.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Remover a integração com o Azure Monitor

Quando você não precisar mais da integração entre o grupo de gerenciamento do Operations Manager e o espaço de trabalho do Log Analytics, haverá várias etapas necessárias para remover corretamente a conexão e a configuração do grupo de gerenciamento. O procedimento a seguir atualiza seu espaço de trabalho Log Analytics excluindo a referência do seu grupo de gerenciamento, exclui os conectores de Azure Monitor e, em seguida, exclui os pacotes de gerenciamento que dão suporte à integração com o serviço.  

Pacotes de gerenciamento para as soluções que você habilitou que se integram com Operations Manager e os pacotes de gerenciamento necessários para dar suporte à integração com Azure Monitor não podem ser facilmente excluídos do grupo de gerenciamento. Isso ocorre porque alguns dos pacotes de gerenciamento Azure Monitor têm dependências em outros pacotes de gerenciamento relacionados. Para excluir pacotes de gerenciamento com uma dependência em outros pacotes de gerenciamento, baixe o script [remover um pacote de gerenciamento com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do TechNet Script Center.  

1. Abra o Shell de comando Operations Manager com uma conta que seja membro da função Administradores de Operations Manager.

    > [!WARNING]
    > Verifique se você não tem pacotes de gerenciamento personalizados com o Word Advisor ou IntelligencePack no nome antes de continuar, caso contrário, as etapas a seguir os excluirão do grupo de gerenciamento.
    >

1. No prompt de shell de comando, digite `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Em seguida, digite `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Para remover todos os pacotes de gerenciamento restantes que têm uma dependência em outros pacotes de gerenciamento do System Center Advisor, use o script *RecursiveRemove. ps1* que você baixou do TechNet Script Center anteriormente.  

    > [!NOTE]
    > A etapa para remover os pacotes de gerenciamento do Advisor com o PowerShell não excluirá automaticamente os pacotes de gerenciamento internos do Microsoft System Center Advisor ou do Microsoft System Center Advisor.  Não tente excluí-los.  
    >  

1. Abra o Console de operações do Operations Manager com uma conta que seja membro da função Administradores do Operations Manager.
1. Em **Administração**, selecione o nó **pacotes de gerenciamento** e, na caixa **Procurar:** , digite **Advisor** e verifique se os pacotes de gerenciamento a seguir ainda estão importados no seu grupo de gerenciamento:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor interno

1. No portal do Azure, clique no bloco **configurações** .
1. Selecione **Fontes Conectadas**.
1. Na tabela na seção System Center Operations Manager, você verá o nome do grupo de gerenciamento que deseja remover do workspace. Na coluna **Últimos Dados**, clique em **Remover**.  

    > [!NOTE]
    > O link **remover** não estará disponível até depois de 14 dias se não houver nenhuma atividade detectada do grupo de gerenciamento conectado.  
    >

1. Será exibida uma janela solicitando que você confirme se deseja continuar com a remoção.  Clique em **Sim** para confirmar.

Para excluir os dois conectores-Microsoft. SystemCenter. Advisor. DataConnector e o conector do Advisor, salve o script do PowerShell abaixo em seu computador e execute-o usando os seguintes exemplos:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> O computador do qual você executa esse script, se não for um servidor de gerenciamento, deve ter o Shell de comando Operations Manager instalado, dependendo da versão do seu grupo de gerenciamento.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

No futuro, se você planeja reconectar seu grupo de gerenciamento a um espaço de trabalho do Log Analytics, você precisará importar novamente o arquivo do pacote de gerenciamento do `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`. Dependendo da versão do System Center Operations Manager implantada em seu ambiente, você pode encontrar esse arquivo no seguinte local:

* Na mídia de origem na pasta `\ManagementPacks` do System Center 2016-Operations Manager e superior.
* Do pacote cumulativo de atualizações mais recente aplicado ao seu grupo de gerenciamento. Para Operations Manager 2012, a pasta de origem é `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` e para 2012 R2, ela está localizada em `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Próximos passos

Para adicionar funcionalidade e coletar dados, consulte [Adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md).
