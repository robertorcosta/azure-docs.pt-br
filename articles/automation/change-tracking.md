---
title: Controlar alterações com a Automação do Azure
description: A solução Controle de Alterações ajuda a identificar alterações no software e Serviço Windows que ocorrem no ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682985"
---
# <a name="track-environment-changes-with-change-tracking"></a>Rastrear as alterações do ambiente com o rastreamento de alterações

Este artigo ajuda você a usar a solução de Controle de Alterações para identificar facilmente as alterações em seu ambiente. A solução rastreia as seguintes alterações de configuração para ajudá-lo a identificar problemas operacionais:

- Software do Windows
- Software Linux (pacotes)
    >[!NOTE]
    >O Rastreamento de alterações apenas rastreia o software gerenciado com o gerenciador de pacotes de distribuição.

- Arquivos Windows e Linux
- Chaves de registro do Windows
- Serviços do Windows
- Daemons Linux

Depois que a solução estiver ativada, você poderá visualizar o resumo das alterações para seus computadores monitorados selecionando **O Rastreamento de Alterações** em **Gerenciamento de Configuração** em sua conta de Automação.

> [!NOTE]
> A automação do Azure Altera as alterações nas máquinas virtuais. Para acompanhar as alterações de propriedade do Azure Resource Manager, consulte o histórico de [alterações](../governance/resource-graph/how-to/get-resource-changes.md)do Gráfico de Recursos do Azure .

É possível exibir as alterações nos computadores e, em seguida, analisar detalhadamente cada evento. Os menus suspensos estão disponíveis na parte superior do gráfico para limitar o gráfico e as informações detalhadas com base no tipo de alteração e intervalos de tempo. Você também pode clicar e arrastrar no gráfico para selecionar um intervalo de tempo personalizado. **Tipo de alteração** será um dos seguintes valores **Eventos,** **Daemons,** **Arquivos,** **Registro,** **Software,** **Serviços Windows**. A categoria mostra o tipo de alteração e pode ser **adicionada,** **modificada**ou **removida.**

![imagem do painel Controle de Alterações](./media/change-tracking/change-tracking-dash01.png)

Ao clicar em uma alteração ou evento, as informações detalhadas sobre essa alteração serão exibidas. Como possível visualizar no exemplo, o tipo de inicialização do serviço foi alterado de Manual para Automático.

![imagem dos detalhes do controle de alterações](./media/change-tracking/change-tracking-details.png)

Alterações no software instalado, serviços do Windows, registro do Windows e arquivos e daemons Linux nos servidores monitorados são enviadas para o serviço Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

### <a name="windows-operating-systems"></a>Sistemas operacionais Windows

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2008 R2 ou posterior

### <a name="linux-operating-systems"></a>Sistemas operacionais Linux

As seguintes distribuições Linux têm suporte oficialmente. No entanto, o agente para Linux também pode ser executado em outras distribuições não listadas. Salvo indicação em contrário, todas as versões secundárias são compatíveis com cada versão principal listada.

#### <a name="64-bit"></a>64 bits

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32 bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS and 16.04 LTS

## <a name="limitations"></a>Limitações

A solução de Controle de Alterações atualmente não dá suporte ao seguinte:

* Recursão para o rastreamento de registro do Windows
* Sistemas de arquivos de rede
* Diferentes métodos de instalação
* ***.exe** arquivos para Windows

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos em um ciclo de coleta de 30 minutos, o desempenho da solução pode ser degradado.
* Quando o tráfego de rede é alto, os registros de alteração podem levar até seis horas para serem exibidos.
* Se você modificar a configuração enquanto um computador é desligado, o computador pode postar alterações pertencentes à configuração anterior.

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a solução Controle de Alterações está enfrentando os seguintes problemas:

* Atualizações de hotfix não são coletadas em computadores Windows Server 2016 Core RS3.
* Os Daemons Linux podem mostrar um estado alterado, mesmo que não tenha havido mudança. Isso é devido `SvcRunLevels` à forma como o campo é capturado.

## <a name="network-requirements"></a>Requisitos de rede

O rastreamento de alterações requer especificamente os seguintes endereços. Comunicações para esses endereços usam a porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.Azure automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Configurações de caractere curinga, recursão e ambiente

A recursão permite que você especifique caracteres curinga para simplificar o acompanhamento em diretórios, e variáveis para que você possa acompanhar arquivos em ambientes com vários nomes de unidade ou nomes de unidade dinâmicos. A seguir está uma lista que mostra informações comuns que você deve saber ao configurar a recursão:

* Caracteres curinga são necessários para acompanhar vários arquivos.
* Curingas são usados apenas no último segmento de um\\caminho, por exemplo, c:\folder file* ou /etc/*.conf.
* Se uma variável de ambiente tiver um caminho inválido, a validação será bem sucedida, mas esse caminho falhará quando o Inventário for executado.
* Evite caminhos gerais ao definir o caminho, pois esse tipo de configuração pode fazer com que muitas pastas sejam atravessadas.

## <a name="change-tracking-data-collection-details"></a>Detalhes de coleta de dados do Controle de Alterações

A tabela a seguir mostra a frequência da coleta de dados para os tipos de alterações. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas:

| **Tipo de alteração** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços do Windows | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de item controlados por máquina para Controle de Alterações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Arquivo|500||
|Registro|250||
|Software do Windows|250|Não inclui hotfixes de software|
|Pacotes do Linux|1250||
|Serviços|250||
|Daemon|250||

O uso médio de dados do Log Analytics para uma máquina usando o Change Tracking é de aproximadamente 40MB por mês. Esse valor é somente uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

### <a name="windows-service-tracking"></a>Rastreamento de serviço do Windows

A frequência da coleta padrão para os serviços do Windows é de 30 minutos. Para configurar a frequência, acesse **Controle de Alterações**. Em **Editar Configurações** na guia **Serviços do Windows**, há um controle deslizante que permite alterar a frequência de coleta para os serviços do Windows de 10 segundos para até 30 minutos. Mova o controle deslizante para a frequência desejada e ele a salvará automaticamente.

![Controle deslizante de serviços do Windows](./media/change-tracking/windowservices.png)

O agente controla somente as alterações e isso otimiza o desempenho do agente. Definir um limite alto poderá deixar passar alterações se o serviço for revertido ao estado original. Definir a frequência para um valor menor permite capturar alterações que poderiam ser perdidas de outra forma.

> [!NOTE]
> Embora o agente possa controlar as alterações em um intervalo de apenas 10 segundos, os dados ainda demoram alguns minutos para serem exibido no portal. As alterações durante o tempo de exibição no portal do ainda são controladas e registradas.

### <a name="registry-key-change-tracking"></a>Controle de alterações de chave do Registro

O objetivo de monitorar alterações às chaves do registro é identificar os pontos de extensibilidade em que código de terceiros e o malware podem ser ativados. A lista a seguir mostra a lista de chaves do Registro pré-configuradas. Essas chaves estão configuradas, mas não habilitadas. Para controlar essas chaves do Registro, é necessário habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registro | Finalidade |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitores entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora scripts que são executados na inicialização.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora scripts que são executados no desligamento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora as chaves que são carregadas antes da usuário se autentica em suas contas do Windows. A chave é usada para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora as alterações às configurações do aplicativo.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitores entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitores entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone para programas de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramentas personalizada para programas de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à seção [drivers] no arquivo SYSTEM.INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits executados em computadores de 64 bits. Semelhante à seção [drivers] no arquivo SYSTEM.INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora a lista de DLLs de sistema conhecidas ou comumente usadas; esse sistema impede que pessoas explorem as permissões de diretório de aplicativo fracas via depósito de versões com cavalo de troia das DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora a lista de pacotes capaz de receber notificações de eventos do Winlogon, o modelo de suporte de logon interativo para o sistema operacional Windows.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Habilite o rastreamento de alterações

Para começar a acompanhar as alterações, você precisa ativar a solução De rastreamento de alterações. Existem muitas maneiras de embarcar máquinas para alterar rastreamento. A seguir estão as maneiras recomendadas e compatíveis de integrar a solução.

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Da navegação em várias máquinas](automation-onboard-solutions-from-browse.md)
* [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de Automação do Azure](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Configurar o rastreamento de alterações

Para aprender a embarcar computadores na solução, consulte [soluções de Automação onboarding](automation-onboard-solutions-from-automation-account.md). Depois de ter uma máquina embarcando com a solução De rastreamento de alterações, você pode configurar os itens a serem rastreados. Quando você habilita uma nova chave de arquivo ou registro para rastrear, ela é ativada para o Rastreamento de Alterações.

Para rastrear alterações em arquivos no Windows e Linux, os hashes MD5 dos arquivos são usados. Esses hashes são usados para detectar se uma alteração foi feita desde o último inventário.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Habilite o monitoramento de integridade de arquivos no Azure Security Center

O Azure Security Center adicionou o File Integrity Monitoring (FIM), que é construído no Azure Change Tracking. Embora a FIM monitore apenas arquivos e registros, a solução completa de Rastreamento de Alterações também inclui:

- Mudanças de software
- Serviços Windows
- Linux Daemons

Se você já habilitou a FIM e gostaria de experimentar a solução completa de Rastreamento de Alterações, você precisa executar as seguintes etapas. As configurações não são removidas por este processo.

> [!NOTE]
> Ativar a solução completa de rastreamento de alterações pode causar cobranças adicionais, para obter mais informações, consulte [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/).

1. Remova a solução de monitoramento navegando até o espaço de trabalho e localizando-a na [Lista de soluções de monitoramento instaladas.](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)
2. Clique no nome da solução para abrir sua página de resumo e, em seguida, clique em Excluir, conforme detalhado em [Remover uma solução de monitoramento](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Reative a solução navegando para a conta de automação e selecionando **o rastreamento de alterações** em **gerenciamento de configuração**.
4. Confirme os detalhes da configuração do espaço de trabalho e clique **em Ativar**.

## <a name="configure-file-content-change-tracking"></a>Configurar o rastreamento de alteração de conteúdo do arquivo

Você pode visualizar o conteúdo antes e depois da alteração do arquivo com o rastreamento de alteração de conteúdo do arquivo. Este recurso está disponível para arquivos Windows e Linux. Para cada alteração em um arquivo, o conteúdo do arquivo é armazenado em uma conta de armazenamento. O arquivo é mostrado antes e depois da alteração, em linha ou lado a lado. Para obter mais informações, confira [Exibir o conteúdo de um arquivo controlado](change-tracking-file-contents.md).

![exibir alterações em um arquivo](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Configurar chaves do Registro do Windows para rastrear

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. Em sua conta de automação, selecione **Alterar rastreamento** em Gerenciamento **de Configuração**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página Controle de Alterações, selecione **Registro do Windows** e, em seguida, clique em **+ Adicionar** para adicionar um novo registro para controle.
3. Em **Adicionar Registro do Windows para Controle de Alterações**, insira as informações para a chave rastrear e clique em **Salvar**.

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável da chave de registro a ser rastreada.        |
|Agrupar     | Um nome de grupo para agrupar chaves de registro logicamente.        |
|Chave de Registro do Windows   | O caminho para verificar a chave de registro. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Configurar o rastreamento de arquivos no Windows

Use as seguintes etapas para configurar o rastreamento de arquivos em computadores Windows:

1. Em sua conta de automação, selecione **Alterar rastreamento** em Gerenciamento **de Configuração**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página Controle de Alterações, selecione **Arquivos do Windows** e, em seguida, clique em **+ Adicionar** para adicionar o novo arquivo para controle.
3. Em **Adicionar o Arquivo do Windows para o Controle de Alterações**, insira as informações para o arquivo a controlar e clique em **Salvar**.

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | Verdade se a configuração for aplicada, e falsa de outra forma.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **c:\temp\\\*.txt**<br>Você também pode usar variáveis `%winDir%\System32\\\*.*`de ambiente, como .       |
|Recursão     | É verdade se a recursão for usada ao procurar o item a ser rastreado, e Falsa de outra forma.        |
|Carregar o conteúdo do arquivo para todas as configurações| É verdadeiro carregar o conteúdo do arquivo em alterações rastreadas e Falso de outra forma.|

## <a name="configure-file-tracking-on-linux"></a>Configurar o rastreamento de arquivos no Linux

Use as etapas a seguir para configurar o acompanhamento de arquivo em computadores Linux:

1. Em sua conta de automação, selecione **Alterar rastreamento** em Gerenciamento **de Configuração**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página Controle de Alterações, selecione **Arquivos Linux** e, em seguida, clique em **+ Adicionar** para adicionar o novo arquivo para controle.
3. No **Adicionar arquivo Linux para rastreamento de alterações,** digite as informações do arquivo ou diretório para rastrear e clicar **em Salvar**.

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo. Por exemplo: “/etc/* .conf”       |
|Tipo de caminho     | Tipo de item a ser rastreado; possíveis valores são: Arquivo e Diretório.        |
|Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
|Usar o Sudo     | Essa configuração determina se o Sudo será usado durante a verificação do item.         |
|Links     | Essa configuração determina como os links simbólicos lidam ao passar diretórios.<br> **Ignorar** – ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>**Seguir** - Segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados.<br>**Gerenciar** - Segue os links simbólicos e permite a alteração do conteúdo retornado.     |
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **verdadeiro** ou **falso**.|

> [!NOTE]
> A opção "Gerenciar" links não é recomendada. Não há suporte para a recuperação de conteúdo do arquivo.

## <a name="search-logs"></a>Pesquisa da logs

Você pode fazer várias pesquisas contra os registros de alterações. Com a página Alterar rastreamento aberta, clique **em Log Analytics**, isso abre a página Logs. A tabela a seguir fornece pesquisas de logs de exemplo para os registros de alterações coletados por essa solução:

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationData<br>&#124; onde   ConfigDataType == "WindowsServices" e SvcStartupType == "Auto"<br>&#124; onde SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) por SoftwareName, Computer         | Mostra os registros de inventário mais recentes para os Serviços do Windows que foram configurados para Automático, mas foram relatados como Parados<br>Os resultados estão limitados ao registro mais recente desse SoftwareName e Computador      |
|ConfigurationChange<br>&#124; onde ConfigChangeType == "Software" e ChangeCategory == "Removed"<br>&#124; ordenar por TimeGenerated desc|Mostra os registros de alterações do software removido|

## <a name="alert-on-changes"></a>Alertar sobre alterações

Um recurso-chave do Rastreamento de Alterações está alertando sobre o estado de configuração e quaisquer alterações no estado de configuração do seu ambiente híbrido. O exemplo a seguir mostra que o arquivo **C:\windows\system32\drivers\etc\hosts** foi modificado em uma máquina. Este arquivo é importante porque o Windows o usa para resolver nomes de hostpara endereços IP. Esta operação tem precedência sobre o DNS, e pode resultar em problemas de conectividade ou o redirecionamento do tráfego para sites maliciosos ou perigosos.

![Um gráfico mostrando os hosts de alteração de arquivo](./media/change-tracking/changes.png)

Para analisar ainda mais essa alteração, vá para a Pesquisa de logs clicando em **Log Analytics**. Quando estiver na Pesquisa de logs, pesquise alterações de conteúdo ao arquivo Hosts com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Esta consulta procura as alterações incluindo uma alteração do conteúdo de arquivos cujo caminho totalmente qualificado contém a palavra "hosts". Você também pode pedir para um arquivo específico alterando a parte do caminho para seu formulário totalmente qualificado (como `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Depois que a consulta retornar os resultados desejados, clique em **Nova regra de alerta** na pesquisa de log para abrir a página de criação de alerta. Você também pode navegar para essa experiência por meio do **Azure Monitor** no portal do Azure. 

Verifique sua consulta novamente e modifique a lógica de alerta. Nesse caso, você desejará que o alerta seja disparado se for detectada até mesmo uma alteração entre todos os computadores no ambiente.

![Uma imagem que mostra a consulta de alteração para o controle de alterações para o arquivo de hosts](./media/change-tracking/change-query.png)

Após definir a lógica da condição, atribua grupos de ação para executar ações em resposta ao alerta que está sendo disparado. Nesse caso, eu configurei emails para serem enviados e um tíquete ITSM para ser criado.  Muitas outras ações úteis também podem ser executadas, como disparar uma Função do Azure, um Runbook de automação, um webhook ou um aplicativo lógico.

![Uma imagem configurando um grupo de ações para alertar sobre a alteração](./media/change-tracking/action-groups.png)

Após configurar os parâmetros e a lógica, podemos aplicar o alerta ao ambiente.

### <a name="alert-suggestions"></a>Sugestões de alerta

Embora alertar sobre alterações no arquivo hosts seja uma boa aplicação de alertas para dados de rastreamento de alterações ou inventário, há muito mais cenários para alertar, incluindo os casos definidos juntamente com suas consultas de exemplo na seção abaixo.

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Files" e FileSystemPath contêm " c:\\windows\\system32\\drivers\\"|Útil para controlar alterações a arquivos críticos do sistema|
|ConfigurationChange <br>&#124; em que FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Útil para controlar modificações a arquivos de configuração chave|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "WindowsServices" e SvcName contêm "w3svc" e SvcState == "Stopped"|Útil para controlar alterações a serviços críticos do sistema|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Daemons" e SvcName contêm "ssh" e SvcState != "Running"|Útil para controlar alterações a serviços críticos do sistema|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Software" e ChangeCategory == "Added"|Útil para ambientes que precisam bloquear configurações de software|
|ConfigurationData <br>&#124; em que SoftwareName contém "Monitoring Agent" e CurrentVersion != "8.0.11081.0"|Útil para ver quais computadores têm uma versão de software desatualizada ou não em conformidade instalada. Relata o último estado de configuração relatada, não as alterações.|
|ConfigurationChange <br>&#124; onde RegistryKey ==\\\\@"HKEY_LOCAL_MACHINE SOFTWARE Microsoft\\Windows CurrentVersion\\\\QualityCompat"| Útil para controlar alterações a chaves antivírus cruciais|
|ConfigurationChange <br>&#124; onde RegistryKey contém\\\\@"HKEY_LOCAL_MACHINE\\\\SISTEMA CurrentControlSet Services SharedAccess\\Parameters\\FirewallPolicy"| Útil para controlar alterações em configurações de firewall|

## <a name="next-steps"></a>Próximas etapas

Visite o tutorial sobre Controle de Alterações para saber mais sobre o uso da solução:

> [!div class="nextstepaction"]
> [Solucionar problemas de alterações em seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Use [pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para visualizar dados de rastreamento de alterações detalhados.
