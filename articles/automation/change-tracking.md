---
title: Visão geral do Controle de Alterações e Inventário da Automação do Azure
description: Este artigo descreve o recurso de Controle de Alterações e Inventário, que ajuda a identificar as alterações de software e serviço da Microsoft que ocorrem em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 4f6ae1ad5b0f3904b84d47316c11aa1a67531a28
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835097"
---
# <a name="change-tracking-and-inventory-overview"></a>Visão geral do Controle de Alterações e Inventário

Este artigo apresenta o Controle de Alterações e Inventário na Automação do Azure. Esse recurso acompanha as alterações nas máquinas virtuais e na infraestrutura de servidor para ajudá-lo a identificar problemas operacionais e ambientais com o software gerenciado pelo Gerenciador de Pacotes de Distribuição. Os itens controlados pelo Controle de Alterações e Inventário incluem: 

- Software do Windows
- Software do Linux (pacotes)
- Arquivos do Windows e do Linux
- Chave do registro do Windows
- Serviços Microsoft
- Daemons Linux

> [!NOTE]
> Para rastrear as alterações da propriedade do Azure Resource Manager, confira o [histórico de alterações](../governance/resource-graph/how-to/get-resource-changes.md) do Azure Resource Graph.

O Controle de Alterações e Inventário obtêm seus dados do Azure Monitor. As máquinas virtuais conectadas a workspaces do Log Analytics usam agentes do Log Analytics para coletar dados sobre alterações em softwares instalados, serviços da Microsoft, registro e arquivos do Windows e quaisquer daemons do Linux em servidores monitorados. Quando os dados estão disponíveis, os agentes os enviam para o Azure Monitor para processamento. O Azure Monitor aplica a lógica aos dados recebidos, registra-os e torna-os disponíveis. 

O recurso de Controle de Alterações e Inventário habilita as áreas funcionais de inventário e controle de alterações na Automação do Azure. Como ambas as áreas usam o mesmo agente do Log Analytics, o processo para adicionar uma VM é o mesmo em qualquer área funcional. 

> [!NOTE]
> Para usar o recurso de Controle de Alterações e Inventário, você deverá localizar todas as suas VMs na mesma assinatura e região da conta de Automação.

O Controle de Alterações e Inventário atualmente não dá suporte aos seguintes itens:

* Recursão para o rastreamento de registro do Windows
* Sistemas de arquivos de rede
* Métodos de instalação diferentes
* Arquivos * **.exe** para Windows

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos no ciclo de coleta de 30 minutos, o desempenho do controle de alterações e inventário poderá ser prejudicado.
* Quando o tráfego da rede é alto, os registros de alteração podem demorar até seis horas para serem exibidos.
* Se você modificar uma configuração enquanto um computador for desligado, o computador poderá lançar as alterações que pertenciam à configuração anterior.

Atualmente, a solução Controle de Alterações e Inventário está enfrentando os seguintes problemas:

* Atualizações de hotfix não são coletadas em computadores Windows Server 2016 Core RS3.
* Os daemons do Linux podem mostrar um estado alterado, embora nenhuma alteração tenha ocorrido. Esse problema ocorre devido à maneira como os dados do `SvcRunLevels` no log [ConfigurationChange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) do Azure Monitor é capturado.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

O Controle de Alterações e Inventário tem suporte em todos os sistemas operacionais que atendem aos requisitos de agente do Log Analytics. As versões do sistema operacional Windows com suporte oficialmente são o Windows Server 2008 SP1 ou posterior e o Windows 7 SP1 ou posterior. Vários sistemas operacionais Linux também são compatíveis. Confira [Visão geral do Agente do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

## <a name="network-requirements"></a>Requisitos de rede

O Controle de Alterações e Inventário exige especificamente os endereços de rede listados na tabela a seguir. As comunicações com esses endereços usam a porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.Azure automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Interface do usuário do Controle de Alterações e Inventário

Use Controle de Alterações e Inventário no portal do Azure para exibir o resumo das alterações para computadores monitorados. O recurso está disponível selecionando uma das opções de adicionar VMs para **Controle de alterações** ou **Inventário** em **Gerenciamento de Configuração** em sua conta de Automação.  

![Painel Controle de Alterações](./media/change-tracking/change-tracking-dash01.png)

Os menus suspensos estão disponíveis na parte superior do painel para limitar o gráfico de controle de alterações e as informações detalhadas com base no tipo de alteração e intervalos de tempo. Você também pode clicar e arrastrar no gráfico para selecionar um intervalo de tempo personalizado. 

Você pode clicar em uma alteração ou evento para exibir seus detalhes. Os tipos de alterações disponíveis são:

* Eventos
* Daemons
* Arquivos
* Registro
* Software
* Serviços Microsoft

Você pode adicionar, modificar ou remover cada alteração. No exemplo abaixo, você pode ver uma alteração no tipo de inicialização de um serviço de Manual para Automático.

![Detalhes do Controle de Alterações](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>Controle de Alterações de arquivo

Para rastrear alterações em arquivos no Windows e Linux, o Controle de Alterações e Inventário usa os hashes MD5 dos arquivos. O recurso usa os hashes para detectar se foram feitas alterações desde o último inventário.

## <a name="tracking-of-file-content-changes"></a>Controle de alterações de conteúdo do arquivo

O Controle de Alterações e Inventário permite exibir o conteúdo de um arquivo Windows ou Linux antes e depois de uma alteração de arquivo. Para cada alteração em um arquivo, o Controle de Alterações e Inventário armazena o conteúdo do arquivo em uma [conta de Armazenamento do Azure](../storage/common/storage-create-storage-account.md). Quando estiver controlando o arquivo, você poderá exibir seu conteúdo antes ou depois de uma alteração. Você pode exibir o conteúdo embutido ou lado a lado. 

![Exibir alterações em um arquivo](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Acompanhamento de chaves do Registro

O Controle de Alterações e Inventário permite o monitoramento de alterações nas chaves do Registro. O monitoramento permite identificar os pontos de extensibilidade em que código de terceiros e o malware podem ser ativados. A tabela a seguir lista as chaves de Registro pré-configuradas (mas não habilitadas). Para controlar essas chaves, é necessário habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registro | Finalidade |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora scripts que são executados na inicialização.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora scripts que são executados no desligamento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora as chaves que são carregadas antes que o usuário entre na conta do Windows. A chave é usada para aplicativos de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora as alterações às configurações do aplicativo.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone para aplicativos de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação para aplicativos de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramentas personalizada para aplicativos de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à seção [drivers] no arquivo **system.ini**.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para aplicativos de 32 bits executados em computadores de 64 bits. Semelhante à seção [drivers] no arquivo **system.ini**.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora a lista de DLLs de sistema conhecidas ou comumente usadas. Esse sistema impede que pessoas explorem as permissões de diretório de aplicativo fracas via depósito de versões com cavalo de troia das DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora a lista de pacotes que pode receber notificações de eventos do **winlogon.exe**, o modelo de suporte de logon interativo para o Windows.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>Suporte para Monitoramento de integridade de arquivo na Central de Segurança do Azure

O Controle de Alterações e Inventário usam [FIM (Monitoramento de Integridade do Arquivo) da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Embora o FIM monitore apenas arquivos e registros, o recurso completo de Controle de Alterações e Inventário também inclui o acompanhamento para:

- Mudanças de software
- Serviços Microsoft
- Daemons Linux

## <a name="recursion-support"></a>Suporte à recursão

O Controle de Alterações e Inventário dá suporte à recursão, o que permite que você especifique curingas para simplificar o rastreamento em diretórios. A recursão também fornece variáveis de ambiente para permitir que você rastreie arquivos entre ambientes com nomes de unidade variados ou dinâmicos. A seguir está uma lista que inclui informações comuns que você deve saber ao configurar a recursão:

* Caracteres curinga são necessários para acompanhar vários arquivos.
* Caracteres curinga podem ser usados apenas no último segmento de um caminho, como **c:\folder\\file*** ou **/etc/*.conf**.
* Se uma variável de ambiente tiver um caminho inválido, a validação terá êxito, mas o caminho falhará durante a execução.
* Evite nomes de caminho gerais ao definir o caminho, pois esse tipo de configuração pode fazer muitas pastas serem percorridas.

## <a name="change-tracking-and-inventory-data-collection"></a>Coleta de dados do Controle de Alterações e Inventário

A tabela a seguir mostra a frequência de coleta de dados para os tipos de alterações com suporte pelo Controle de Alterações e Inventário. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas.

| **Alterar Tipo** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços Microsoft | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de item controlados por máquina para Controle de Alterações e Inventário.

| **Recurso** | **Limite** |
|---|---|---|
|Arquivo|500|
|Registro|250|
|Software do Windows (sem incluir hotfixes) |250|
|Pacotes do Linux|1250|
|Serviços|250|
|Daemons|250|

O uso médio de dados do Log Analytics para uma máquina usando o Controle de Alterações e Inventário é de aproximadamente 40 MB por mês, dependendo do seu ambiente. Usando o recurso de Uso e custos estimados do workspace do Log Analytics, você pode exibir os dados ingeridos pelo Controle de Alterações e Inventário em um gráfico de uso. Você pode usar essa exibição de dados para avaliar o uso de dados e determinar como ele está afetando sua fatura. Confira [Entender seu uso e estimar os custos](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs).  

### <a name="microsoft-service-data"></a>Dados de serviço da Microsoft

A frequência da coleta padrão para os serviços da Microsoft é de 30 minutos. Você pode configurar a frequência usando um controle deslizante na guia **Serviços da Microsoft** em **Editar Configurações**. 

![Controle deslizante dos serviços da Microsoft](./media/change-tracking/windowservices.png)

Para otimizar o desempenho, o agente do Log Analytics apenas controla as alterações. Definir um limite alto poderá deixar passar alterações se o serviço for revertido ao estado original. Definir a frequência para um valor menor permite capturar alterações que poderiam ser perdidas de outra forma.

> [!NOTE]
> Embora o agente possa controlar as alterações em um intervalo de apenas 10 segundos, os dados ainda demoram alguns minutos para ser exibido no portal do Azure. As alterações que ocorrem durante o tempo de exibição no portal do ainda são controladas e registradas.

## <a name="support-for-alerts-on-configuration-state"></a>Suporte para alertas no estado de configuração

Uma funcionalidade crucial de Controle de Alterações e Inventário é alertar sobre as alterações ao estado de configuração de seu ambiente híbrido. Muitas ações úteis estão disponíveis para disparar em resposta a alertas, por exemplo, ações no Azure Functions, runbooks de automação, webhooks e similares. O alerta sobre alterações no arquivo **C:\windows\system32\drivers\etc\hosts** para um computador é uma boa aplicação de alertas para dados de Controle de Alterações e Inventário. Há muitos outros cenários de alerta também, incluindo os cenários de consulta definidos na tabela a seguir. 

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Files" e FileSystemPath contêm " c:\\windows\\system32\\drivers\\"|Útil para controlar alterações a arquivos críticos do sistema.|
|ConfigurationChange <br>&#124; em que FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Útil para controlar modificações a arquivos de configuração chave.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Serviços Microsoft" e SvcName contêm "w3svc" e SvcState == "Parado"|Útil para controlar alterações a serviços críticos do sistema.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Daemons" e SvcName contêm "ssh" e SvcState!= "Em execução"|Útil para controlar alterações a serviços críticos do sistema.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Software" e ChangeCategory == "Added"|Útil para ambientes que precisam bloquear configurações de software.|
|ConfigurationData <br>&#124; em que SoftwareName contém "Monitoring Agent" e CurrentVersion!= "8.0.11081.0"|Útil para ver quais computadores têm uma versão de software desatualizada ou não em conformidade instalada. Esta consulta relata o último estado de configuração relatada, mas não reporta as alterações.|
|ConfigurationChange <br>&#124; em que RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Útil para controlar alterações a chaves antivírus cruciais.|
|ConfigurationChange <br>&#124; em que RegistryKey contém @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Útil para controlar alterações em configurações de firewall.|

## <a name="next-steps"></a>Próximas etapas

* Para ter detalhes sobre como trabalhar com os recursos Controle de Alterações e Inventário, confira [Gerenciar o Controle de Alterações e o Inventário](change-tracking-file-contents.md).
* Para habilitar o recurso de um runbook, confira [Habilitar Controle de Alterações e Inventário de um runbook](automation-enable-changes-from-runbook.md).
* Para habilitar o recurso de uma conta de Automação, confira [Habilitar Controle de Alterações e Inventário de uma conta de Automação](automation-enable-changes-from-auto-acct.md).
* Para habilitar o recurso navegando no portal do Azure, confira [Habilitar Controle de Alterações e Inventário do portal do Azure](automation-onboard-solutions-from-browse.md).
* Para habilitar o recurso de uma VM do Azure, confira [Habilitar Controle de Alterações e Inventário de uma VM do Azure](automation-enable-changes-from-vm.md).
* Se você precisar pesquisar logs armazenados em seu workspace do Log Analytics, confira [Pesquisas de logs em logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Para solucionar problemas de erros de recurso, confira [Solucionar problemas do Controle de Alterações e Inventário](troubleshoot/change-tracking.md).