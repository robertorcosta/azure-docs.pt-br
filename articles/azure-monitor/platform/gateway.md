---
title: Conecte computadores usando o gateway Log Analytics | Microsoft Docs
description: Conecte seus dispositivos e computadores monitorados pelo Gerente de Operações usando o gateway Log Analytics para enviar dados para o serviço Azure Automation e Log Analytics quando eles não tiverem acesso à Internet.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298348"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Conecte computadores sem acesso à Internet usando o gateway Log Analytics no Azure Monitor

>[!NOTE]
>À medida que o Microsoft Operations Management Suite (OMS) passa para o Microsoft Azure Monitor, a terminologia está mudando. Este artigo refere-se ao OMS Gateway como o gateway Azure Log Analytics. 
>

Este artigo descreve como configurar a comunicação com o Azure Automation e o Azure Monitor usando o gateway Log Analytics quando computadores conectados diretamente ou que são monitorados pelo Operations Manager não têm acesso à Internet. 

O gateway Log Analytics é um proxy avançado HTTP que suporta tunelamento HTTP usando o comando HTTP CONNECT. Esse gateway envia dados para o Azure Automation e um espaço de trabalho do Log Analytics no Azure Monitor em nome dos computadores que não podem se conectar diretamente à internet. 

O gateway do Log Analytics dá suporte a:

* Reportando até os mesmos espaços de trabalho do Log Analytics configurados em cada agente por trás dele e que são configurados com o Azure Automation Hybrid Runbook Workers.  
* Computadores Windows nos quais o Microsoft Monitoring Agent está diretamente conectado a um espaço de trabalho do Log Analytics no Azure Monitor.
* Computadores Linux nos quais um agente log analytics para Linux está diretamente conectado a um espaço de trabalho do Log Analytics no Azure Monitor.  
* System Center Operations Manager 2012 SP1 com UR7, Operations Manager 2012 R2 com UR3, ou um grupo de gerenciamento no Operations Manager 2016 ou posterior que esteja integrado com o Log Analytics.  

Algumas políticas de segurança de TI não permitem conexão à internet para computadores de rede. Esses computadores desconectados podem ser dispositivos de ponto de venda (POS) ou servidores que suportam serviços de TI, por exemplo. Para conectar esses dispositivos ao Azure Automation ou a um espaço de trabalho do Log Analytics para que você possa gerenciá-los e monitorá-los, configure-os para se comunicar diretamente com o gateway Log Analytics. O gateway Log Analytics pode receber informações de configuração e encaminhar dados em seu nome. Se os computadores estiverem configurados com o agente Log Analytics para se conectar diretamente a um espaço de trabalho do Log Analytics, os computadores se comunicarão com o gateway Log Analytics.  

O gateway Log Analytics transfere dados dos agentes diretamente para o serviço. Ele não analisa nenhum dos dados em trânsito e o gateway não armazena dados de cache quando perde conectividade com o serviço. Quando o gateway não consegue se comunicar com o serviço, o agente continua a executar e faz fila os dados coletados no disco do computador monitorado. Quando a conexão é restaurada, o agente envia os dados armazenados em cache coletados para o Azure Monitor.

Quando um grupo de gerenciamento do Operations Manager é integrado ao Log Analytics, os servidores de gerenciamento podem ser configurados para se conectar ao gateway Log Analytics para receber informações de configuração e enviar dados coletados, dependendo da solução que você habilitou .  Os agentes do Gerente de Operações enviam alguns dados para o servidor de gerenciamento. Por exemplo, os agentes podem enviar alertas do Operations Manager, dados de avaliação de configuração, dados de espaço de instância e dados de capacidade. Outros dados de alto volume, como registros de IIS (Internet Information Services, serviços de informação da Internet), dados de desempenho e eventos de segurança, são enviados diretamente para o gateway Log Analytics. 

Se um ou mais servidores gateway do Operations Manager forem implantados para monitorar sistemas não confiáveis em uma rede de perímetro ou em uma rede isolada, esses servidores não poderão se comunicar com um gateway log analytics.  Os servidores Gateway do Gerenciador de Operações podem reportar apenas a um servidor de gerenciamento.  Quando um grupo de gerenciamento do Operations Manager é configurado para se comunicar com o gateway Log Analytics, as informações de configuração de proxy são distribuídas automaticamente para todos os computadores gerenciados por agentes configurados para coletar dados de log para o Azure Monitor, mesmo que a configuração esteja vazia.

Para fornecer alta disponibilidade para grupos de gerenciamento de operações diretamente conectados ou que se comunicam com um espaço de trabalho do Log Analytics através do gateway, use o NLB (Network Load Balanceing, balanceamento de carga de rede) para redirecionar e distribuir tráfego em vários servidores de gateway. Dessa forma, se um servidor gateway for para baixo, o tráfego será redirecionado para outro nó disponível.  

O computador que executa o gateway Log Analytics requer que o agente Log Analytics Windows identifique os pontos finais de serviço com os quais o gateway precisa se comunicar. O agente também precisa direcionar o gateway para reportar aos mesmos espaços de trabalho com os quais os agentes ou o grupo de gerenciamento do Gerente de Operações por trás do gateway estão configurados. Essa configuração permite que o gateway e o agente se comuniquem com seu espaço de trabalho atribuído.

Um gateway pode ser multiabrigado para até quatro espaços de trabalho. Este é o número total de espaços de trabalho que um agente do Windows suporta.  

Cada agente deve ter conectividade de rede com o gateway para que os agentes possam transferir automaticamente dados para e do gateway. Evite instalar o gateway em um controlador de domínio. Os computadores Linux que estão por trás de um servidor gateway não podem usar o método de instalação do [script de wrapper](agent-linux.md#install-the-agent-using-wrapper-script) para instalar o agente Log Analytics para Linux. O agente deve ser baixado manualmente, copiado para o computador e instalado manualmente porque o gateway só suporta a comunicação com os serviços do Azure mencionados anteriormente.

O diagrama a seguir mostra dados fluindo de agentes diretos, através do gateway, para a Automação do Azure e o Log Analytics. A configuração do proxy do agente deve corresponder à porta com a qual o gateway Log Analytics está configurado.  

![Diagrama de comunicação direta do agente com os serviços](./media/gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama a seguir mostra o fluxo de dados de um grupo de gerenciamento do Operations Manager para o Log Analytics.   

![Diagrama de comunicação do Gerente de Operações com o Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configure seu sistema

Os computadores designados para executar o gateway Log Analytics devem ter a seguinte configuração:

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Pelo menos um processador de 4 núcleos e 8 GB de memória 
* Um [agente de Análise de Log para Windows](agent-windows.md) configurado para reportar ao mesmo espaço de trabalho que os agentes que se comunicam através do gateway

### <a name="language-availability"></a>Disponibilidade de idiomas

O gateway Log Analytics está disponível nestes idiomas:

- Chinês (Simplificado)
- Chinês (Tradicional)
- Tcheco
- Holandês
- Inglês
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polonês
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol (internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de criptografia com suporte

O gateway Log Analytics suporta apenas o TLS (Transport Layer Security, segurança de camada de transporte) 1.0, 1.1 e 1.2.  Ele não suporta SSL (Secure Sockets Layer Layer, camada de soquetes seguros).  Para garantir a segurança dos dados em trânsito para o Log Analytics, configure o gateway para usar pelo menos o TLS 1.2. As versões mais antigas do TLS ou SSL são vulneráveis. Embora atualmente permitam compatibilidade para trás, evite usá-los.  

Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Número de conexões de agente com suporte

A tabela a seguir mostra aproximadamente quantos agentes podem se comunicar com um servidor gateway. O suporte é baseado em agentes que carregam cerca de 200 KB de dados a cada 6 segundos. Para cada agente testado, o volume de dados é de cerca de 2,7 GB por dia.

|Gateway |Agentes suportados (aproximados)|  
|--------|----------------------------------|  
|CPU: Processador Intel Xeon E5-2660 v3 \@ 2.6 GHz 2 Núcleos<br> Memória: 4 GB<br> Largura de banda da rede: 1 Gbps| 600|  
|CPU: Processador Intel Xeon E5-2660 v3 \@ 2.6 GHz 4 Núcleos<br> Memória: 8 GB<br> Largura de banda da rede: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Fazer download do gateway do Log Analytics

Obtenha a versão mais recente do arquivo de configuração do gateway Log Analytics do Microsoft Download Center[(Download Link)](https://go.microsoft.com/fwlink/?linkid=837444)ou do portal Azure.

Para obter o gateway Log Analytics do portal Azure, siga estas etapas:

1. Navegue pela lista de serviços e selecione **Log Analytics**. 
1. Selecione um workspace.
1. Na folha do seu workspace, em **Geral**, selecione **Início Rápido**. 
1. Em **Escolher uma fonte de dados para conectar ao workspace**, selecione **Computadores**.
1. Na lâmina **do Agente Direto,** selecione **O gateway Download Log Analytics**.
 
   ![Captura de tela das etapas para baixar o gateway Log Analytics](./media/gateway/download-gateway.png)

ou 

1. Na folha do seu workspace em **Configurações**, selecione **Configurações avançadas**.
1. Vá para os servidores Windows **de fontes** > **conectadas** e selecione **o gateway Download Log Analytics**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instale o gateway Log Analytics usando o assistente de configuração

Para instalar um gateway usando o assistente de configuração, siga estas etapas. 

1. Na pasta de destino, clique duas vezes em **Log Analytics gateway.msi**.
1. Sobre o **boas-vindas** página, selecione **próxima**.

   ![Captura de tela da página de boas-vindas no assistente de configuração do Gateway](./media/gateway/gateway-wizard01.png)

1. Na página **Contrato de Licença,** selecione **Aceito os termos do Contrato de Licença** para concordar com os Termos de Licença de Software da Microsoft e, em seguida, selecione **Next**.
1. Na página **Porta e endereço de proxy**:

   a. Digite o número da porta TCP a ser usado para o gateway. A configuração usa esse número de porta para configurar uma regra de entrada no Firewall do Windows.  O valor padrão é 8080.
      O intervalo válido do número da porta é de 1 a 65535. Se a entrada não estiver dentro desse intervalo, uma mensagem de erro será exibida.

   b. Se o servidor onde o gateway está instalado precisar se comunicar através de um proxy, digite o endereço proxy onde o gateway precisa se conectar. Por exemplo, insira `http://myorgname.corp.contoso.com:80`.  Se você deixar este campo em branco, o gateway tentará se conectar diretamente à internet.  Se o servidor proxy exigir autenticação, insira um nome de usuário e senha.

   c. Selecione **Avançar**.

   ![Captura de tela de configuração para o proxy gateway](./media/gateway/gateway-wizard02.png)

1. Se você não tiver o Microsoft Update ativado, a página Atualização da Microsoft será exibida e você poderá optar por habilitá-la. Faça uma seleção e selecione **Next**. Caso contrário, prossiga para a próxima etapa.
1. Na página **Pasta de destino,** deixe a pasta padrão C:\Program Files\OMS Gateway ou digite o local onde deseja instalar o gateway. Em seguida, selecione **Next**.
1. Na **página Pronto para instalar,** selecione **Instalar**. Se o controle da conta de usuário solicitar permissão para instalar, **selecione Sim**.
1. Após o término da configuração, **selecione Concluir**. Para verificar se o serviço está em execução, abra o snap-in services.msc e verifique se o **GATEWAY OMS** aparece na lista de serviços e se seu status está **em execução**.

   ![Captura de tela dos serviços locais, mostrando que o OMS Gateway está sendo executado](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instale o gateway Log Analytics usando a linha de comando

O arquivo baixado para o gateway é um pacote do Windows Installer que suporta a instalação silenciosa da linha de comando ou outro método automatizado. Se você não estiver familiarizado com as opções padrão de linha de comando para o Windows Installer, consulte [opções de linha de comando](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
A tabela a seguir destaca os parâmetros suportados pela configuração.

|Parâmetros| Observações|
|----------|------| 
|Número_da_porta | Número da porta TCP para gateway para ouvir |
|Proxy | Endereço IP do servidor proxy |
|INSTALLDIR | Caminho totalmente qualificado para especificar o diretório de instalação de arquivos de software gateway |
|USERNAME | ID do usuário para autenticar com servidor proxy |
|PASSWORD | Senha do ID do usuário para autenticar com proxy |
|LicenseAccepted | Especifique um valor de **1** para verificar se você aceita contrato de licença |
|HASAUTH | Especifique um valor de **1** quando os parâmetros USERNAME/PASSWORD forem especificados |
|HASPROXY | Especifique um valor de **1** ao especificar endereço IP para parâmetro **PROXY** |

Para instalar silenciosamente o gateway e configurá-lo com um endereço proxy específico, número da porta, digite o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Usando a opção de linha de comando /qn oculta a configuração, /qb mostra configuração durante a instalação silenciosa.  

Se você precisar fornecer credenciais para autenticar com o proxy, digite o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Após a instalação, você pode confirmar se as configurações são aceitas (excluindo o nome de usuário e a senha) usando os seguintes cmdlets do PowerShell:

- **Get-OMSGatewayConfig** – Retorna a porta TCP que o gateway está configurado para ouvir.
- **Get-OMSGatewayRelayProxy** – Retorna o endereço IP do servidor proxy com o servidor proxy com o que você o configurou para se comunicar.

## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede

Você pode configurar o gateway para alta disponibilidade usando o NLB (Network Load Balancencing, balanceamento de carga de rede) usando [nLB (Microsoft Network Load Balanceing, Balancer](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)de carga do [Azure)](../../load-balancer/load-balancer-overview.md)ou balanceadores de carga baseados em hardware. O balanceador de carga gerencia o tráfego redirecionando as conexões solicitadas dos servidores de gerenciamento do Operations Manager ou de agentes do Log Analytics entre seus nós. Se um servidor de Gateway falhar, o tráfego será redirecionado para outros nós.

### <a name="microsoft-network-load-balancing"></a>Balanceamento de carga de rede da Microsoft

Para saber como projetar e implantar um cluster de balanceamento de carga de rede Windows Server 2016, confira [Balanceamento de carga de rede](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). As etapas a seguir descrevem como configurar uma cluster de balanceamento de carga de rede Microsoft Network.  

1. Entre no servidor Windows que seja membro do cluster NLB com uma conta administrativa.  
2. Abra o Gerenciador de Balanceamento de Carga de Rede no gerenciador de servidores, clique em **Ferramentas**e clique em **Gerenciador de Balanceamento de Carga de Rede**.
3. Para conectar um servidor do gateway do Log Analytics ao Microsoft Monitoring Agent instalado, clique com o botão direito no endereço IP do cluster e clique em **Adicionar Host ao Cluster**. 

    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster](./media/gateway/nlb02.png)
 
4. Digite o endereço IP do servidor de gateway que deseja conectar. 

    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster: Conectar](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Para aprender como projetar e implantar um Azure Load Balancer, veja [O que é o Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Para implantar um balanceador de carga básico, siga as etapas descritas neste [quickstart,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) excluindo as etapas descritas na seção **Criar servidores back-end**.   

> [!NOTE]
> Configurar o Balanceador de carga do Azure usando o **SKU básico,** requer que as máquinas virtuais do Azure pertençam a um conjunto de disponibilidade. Para saber mais sobre conjuntos de disponibilidade, consulte [Gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](../../virtual-machines/windows/manage-availability.md). Para adicionar máquinas virtuais existentes a um conjunto de disponibilidade, consulte [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Depois que o balanceador de carga é criado, um pool de back-end precisa ser criado, que distribui tráfego para um ou mais servidores gateway. Siga as etapas descritas na seção de artigo quickstart [Criar recursos para o balanceador de carga](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Ao configurar o teste de saúde, ele deve ser configurado para usar a porta TCP do servidor gateway. O teste de saúde adiciona ou remove dinamicamente os servidores gateway da rotação do balanceador de carga com base em sua resposta a verificações de saúde. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configure o grupo de gerenciamento do agente de log analytics e do gerente de operações

Nesta seção, você verá como configurar agentes log analytics conectados diretamente, um grupo de gerenciamento de gerente de operações ou trabalhadores do runbook híbrido de automação do Azure com o gateway Log Analytics para se comunicar com o Azure Automation ou o Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configure um agente de Log Analytics autônomo

Ao configurar o agente Log Analytics, substitua o valor do servidor proxy pelo endereço IP do servidor de gateway log analytics e seu número de porta. Se você tiver implantado vários servidores de gateway atrás de um balanceador de carga, a configuração proxy do agente Log Analytics é o endereço IP virtual do balanceador de carga.  

>[!NOTE]
>Para instalar o agente Log Analytics no gateway e nos computadores Windows que se conectam diretamente ao Log Analytics, consulte [Conecte os computadores do Windows ao serviço Log Analytics no Azure](agent-windows.md). Para conectar computadores Linux, consulte [Conectar computadores Linux ao Azure Monitor](agent-linux.md). 
>

Depois de instalar o agente no servidor gateway, configure-o para reportar aos agentes de espaço de trabalho ou espaço de trabalho que se comunicam com o gateway. Se o agente log analytics do Windows não estiver instalado no gateway, o evento 300 será gravado no registro de eventos do OMS Gateway, indicando que o agente precisa ser instalado. Se o agente estiver instalado, mas não configurado para reportar ao mesmo espaço de trabalho que os agentes que se comunicam através dele, o evento 105 será gravado no mesmo registro, indicando que o agente no gateway precisa ser configurado para relatar ao mesmo espaço de trabalho que os agentes que comunicar com o gateway.

Depois de concluir a configuração, reinicie o serviço **OMS Gateway** para aplicar as alterações. Caso contrário, o gateway rejeitará agentes que tentam se comunicar com o Log Analytics e reportará o evento 105 no registro de eventos do OMS Gateway. Isso também acontecerá quando você adicionar ou remover um espaço de trabalho da configuração do agente no servidor gateway.

Para obter informações relacionadas ao Trabalhador de Runbook Híbrido de Automação, consulte [Automatizar recursos em seu datacenter ou nuvem usando o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configure o Gerenciador de Operações, onde todos os agentes usam o mesmo servidor proxy

A configuração do proxy do Operations Manager é aplicada automaticamente a todos os agentes que se reportam ao Operations Manager, mesmo se a configuração está vazia.  

Para usar o GATEWAY OMS para dar suporte ao Operations Manager, você deve ter:

* O Microsoft Monitoring Agent (versão 8.0.10900.0 ou posterior) instalado no servidor DO GATEWAY DO OMS e configurado com os mesmos espaços de trabalho do Log Analytics aos quais seu grupo de gerenciamento está configurado para reportar.
* Conectividade com a internet. Alternativamente, o GATEWAY OMS deve ser conectado a um servidor proxy conectado à internet.

> [!NOTE]
> Se você especificar nenhum valor para o gateway, os valores em branco serão empurrados para todos os agentes.
>

Se o grupo de gerenciamento do Gerenciador de Operações estiver se registrando em um espaço de trabalho do Log Analytics pela primeira vez, você não verá a opção de especificar a configuração proxy para o grupo de gerenciamento no console Operações. Esta opção só está disponível se o grupo de gerenciamento tiver sido registrado no serviço.  

Para configurar a integração, atualize a configuração do proxy do sistema usando o Netsh no sistema onde você está executando o console Operações e em todos os servidores de gerenciamento do grupo de gerenciamento. Siga estas etapas:

1. Abra um prompt de comando elevado:

   a. Selecione **Iniciar** e inserir **cmd**.  

   b. Clique com o botão **direito do** mouse no Prompt e **selecione Executar como administrador**.  

1. Insira o seguinte comando:

   `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o `netsh winhttp reset proxy`Log Analytics, remova a alteração executando . Em seguida, no console Operações, use a opção **Configurar servidor proxy** para especificar o servidor gateway do Log Analytics. 

1. No console do Gerenciador de Operações, no **Operations Management Suite,** selecione **Conexão**e selecione **Configurar servidor proxy**.

   ![Captura de tela do Gerenciador de Operações, mostrando a seleção Configurar servidor proxy](./media/gateway/scom01.png)

1. Selecione **Usar um servidor proxy para acessar o Pacote de Gerenciamento de Operações** e, em seguida, digite o endereço IP do servidor gateway log analytics ou endereço IP virtual do balanceador de carga. Tenha cuidado para começar `http://`com o prefixo .

   ![Captura de tela do Gerenciador de Operações, mostrando o endereço do servidor proxy](./media/gateway/scom02.png)

1. Selecione **Concluir**. O grupo de gerenciamento do Operations Manager agora está configurado para se comunicar por meio do servidor de gateway para o serviço de Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configure o Gerenciador de Operações, onde agentes específicos usam um servidor proxy

Para ambientes grandes ou complexos, você pode querer que apenas servidores (ou grupos) específicos usem o servidor gateway do Log Analytics.  Para esses servidores, você não pode atualizar o agente do Gerente de Operações diretamente porque esse valor é substituído pelo valor global para o grupo de gerenciamento.  Em vez disso, anular a regra usada para empurrar esses valores.  

> [!NOTE] 
> Use essa técnica de configuração se quiser permitir vários servidores de gateway log analytics em seu ambiente.  Por exemplo, você pode exigir que servidores de gateway log analytics específicos sejam especificados em uma base regional.
>

Para configurar servidores ou grupos específicos para usar o servidor gateway do Log Analytics: 

1. Abra o console do Operations Manager e selecione o workspace **Criação** .  
1. No workspace de Criação, selecione **Regras**. 
1. Na barra de ferramentas do Gerenciador de operações, selecione o botão **Escopo.** Se este botão não estiver disponível, certifique-se de ter selecionado um objeto, não uma pasta, no painel **Monitoramento.** A caixa de diálogo **Delimitar Objetos do Pacote de Gerenciamento** exibe uma lista de objetos, grupos ou classes comuns de destino. 
1. No **campo Procurar,** insira **o Serviço** de Saúde e selecione-o na lista. Selecione **OK**.  
1. Pesquisar por **Regra de Configuração de Proxy do Conselheiro**. 
1. Na barra de ferramentas do Gerenciador de operações, selecione **Substituições** e, em seguida, aponte para **Substituir a Regra\Para um objeto específico de classe: Serviço de saúde** e selecione um objeto na lista.  Ou crie um grupo personalizado que contenha o objeto de serviço de saúde dos servidores a que deseja aplicar essa substituição. Em seguida, aplique a substituição ao seu grupo personalizado.
1. Na caixa de diálogo **Propriedades de substituição,** adicione uma marca de seleção na coluna **'Substituir'** ao lado do parâmetro **WebProxyAddress.**  No campo **Valor de substituição,** digite a URL do servidor gateway Log Analytics. Tenha cuidado para começar `http://`com o prefixo .  

    >[!NOTE]
    > Você não precisa habilitar a regra. Ele já é gerenciado automaticamente com uma substituição no pacote de gerenciamento de referência segura do Microsoft System Center Advisor, que tem como alvo o Microsoft System Center Advisor Monitoring Server Group.
    > 

1. Selecione um pacote de gerenciamento na lista **de pacotes** de gerenciamento de destino Select ou crie um novo pacote de gerenciamento não lacrado selecionando **Novo**. 
1. Quando terminar, selecione **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurar para trabalhadores híbridos de runbook de automação

Se você tiver trabalhadores do manual híbrido de automação em seu ambiente, siga estas etapas para configurar o gateway para dar suporte aos trabalhadores.

Consulte a [seção Configurar sua](../../automation/automation-hybrid-runbook-worker.md#network-planning) rede da documentação de Automação para encontrar a URL de cada região.

Se o seu computador estiver registrado automaticamente como um Trabalhador de Runbook Híbrido, por exemplo, se a solução de Gerenciamento de Atualizações estiver ativada para uma ou mais VMs, siga estas etapas:

1. Adicione as URLs de serviço de dados de runtime do trabalho à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço do gateway do Log Analytics usando o seguinte cmdlet do PowerShell: `Restart-Service OMSGatewayService`

Se o seu computador estiver unido à Automação Azure usando o cmdlet de registro hybrid Runbook Worker, siga estas etapas:

1. Adicione a URL do registro de serviço do agente à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adicione as URLs de serviço de dados de runtime do trabalho à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço do gateway do Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell

Você pode usar cmdlets para concluir as tarefas para atualizar as configurações de configuração do gateway log analytics. Antes de usar cmdlets, certifique-se de:

1. Instale o gateway Log Analytics (Microsoft Windows Installer).
1. Abra uma janela do console do PowerShell.
1. Importe o módulo digitando este comando:`Import-Module OMSGateway`
1. Se nenhum erro tiver ocorrido na etapa anterior, o módulo foi importado com êxito, e os cmdlets poderão ser usados. Inserir `Get-Module OMSGateway`
1. Depois de usar os cmdlets para fazer alterações, reinicie o serviço OMS Gateway.

Um erro na etapa 3 significa que o módulo não foi importado. O erro pode ocorrer quando o PowerShell não consegue encontrar o módulo. Você pode encontrar o módulo no caminho de instalação do GATEWAY DO OMS: *C:\Arquivos do programa\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (obrigatória) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de retransmissão (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de Usuário<br> Senha (string segura) |Define o endereço (e as credenciais) do proxy de retransmissão (upstream) |1. Defina um proxy de relé e credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Defina um proxy de relé que não precise de autenticação:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Limpe a configuração do proxy do relé:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o host atualmente permitido (apenas o host permitido configurado localmente, não os hosts permitidos automaticamente baixados) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obrigatório) |Adiciona o host à lista de permissões |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obrigatório) |Remove o host da lista de permissões |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o assunto do certificado do cliente à lista de permissões |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o assunto do certificado do cliente da lista de permissões |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os sujeitos de certificado de cliente atualmente permitidos (apenas os assuntos permitidos localmente configurados, não baixados automaticamente os sujeitos permitidos) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Solução de problemas

Para coletar eventos registrados pelo gateway, você deve ter o agente Log Analytics instalado.

![Captura de tela da lista Visualizador de eventos no registro de gateway do Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>IDs e descrições de eventos do gateway do Log Analytics

A tabela a seguir mostra os IDs do evento e as descrições dos eventos de registro de gateway do Log Analytics.

| **Id** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicativo que não tenha um ID específico. |
| 401 |Configuração incorreta. Por exemplo, listenPort = "text" em vez de um inteiro. |
| 402 |Exceção ao analisar mensagens de handshake TLS. |
| 403 |Rede de rede. Por exemplo, não é possível se conectar ao servidor de destino. |
| 100 |Informações gerais. |
| 101 |Serviço iniciado. |
| 102 |Serviço interrompido. |
| 103 |Um comando HTTP CONNECT foi recebido do cliente. |
| 104 |Não é um comando HTTP CONNECT. |
| 105 |O servidor de destino não está na lista permitida, ou a porta de destino não é segura (443). <br> <br> Certifique-se de que o agente de MMA no servidor GATEWAY OMS e os agentes que se comunicam com o OMS Gateway estejam conectados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERRO TcpConnection – Certificado cliente inválido: CN=Gateway. <br><br> Certifique-se de que está usando a versão 1.0.395.0 do OMS Gateway ou superior. Certifique-se também de que o agente de MMA no servidor GATEWAY OMS e os agentes que se comunicam com o OMS Gateway estejam conectados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |Versão do protocolo TLS/SSL sem suporte.<br><br> O gateway Log Analytics suporta apenas TLS 1.0, TLS 1.1 e 1.2. Ele não oferece suporte ao protocolo SSL.|
| 107 |A sessão TLS foi verificada. |

### <a name="performance-counters-to-collect"></a>Contadores de desempenho a serem coletados

A tabela a seguir mostra os contadores de desempenho disponíveis para o gateway do Log Analytics. Use o Monitor de Desempenho para adicionar os contadores.

| **Nome** | **Descrição** |
| --- | --- |
| Gateway do Log Analytics/Conexão de Cliente Ativo |Número de conexões de rede de cliente ativo (TCP) |
| Gateway do Log Analytics/Contagem de Erros |Número de erros |
| Gateway do Log Analytics/Cliente Conectado |Número de clientes conectados |
| Gateway do Log Analytics/Contagem de Rejeição |Número de rejeições devido a qualquer erro de validação de TLS |

![Captura de tela da interface do gateway do Log Analytics, mostrando contadores de desempenho](./media/gateway/counters.png)

## <a name="assistance"></a>Assistência

Quando você estiver conectado ao portal Azure, você pode obter ajuda com o gateway Log Analytics ou qualquer outro serviço ou recurso do Azure.
Para obter ajuda, selecione o ícone do ponto de interrogação no canto superior direito do portal e selecione **Nova solicitação de suporte**. Em seguida, preencha o novo formulário de solicitação de suporte.

![Captura de tela de uma nova solicitação de suporte](./media/gateway/support.png)

## <a name="next-steps"></a>Próximas etapas

[Adicione fontes de dados](../../azure-monitor/platform/agent-data-sources.md) para coletar dados de fontes conectadas e armazenar os dados em seu espaço de trabalho do Log Analytics.
