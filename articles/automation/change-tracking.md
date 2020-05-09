---
title: Visão geral de Controle de Alterações e inventário na automação do Azure
description: Controle de Alterações e inventário ajuda a identificar as alterações de software e serviço da Microsoft que ocorrem em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 6a21effc3e567e75a8851fec35ff80dffc60a761
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787168"
---
# <a name="overview-of-change-tracking-and-inventory"></a>Visão geral de Controle de Alterações e inventário

Este artigo apresenta Controle de Alterações e inventário na automação do Azure. Esse recurso acompanha as alterações nas máquinas virtuais e na infraestrutura de servidor para ajudá-lo a identificar problemas operacionais e ambientais com o software gerenciado pelo Gerenciador de pacotes de distribuição. Os itens controlados pelo Controle de Alterações e pelo inventário incluem: 

- Software do Windows
- Software Linux (pacotes)
- Arquivos do Windows e Linux
- Chaves do registro do Windows
- Serviços da Microsoft
- Daemons Linux

> [!NOTE]
> Para rastrear Azure Resource Manager alterações de propriedade, consulte o histórico de [alterações](../governance/resource-graph/how-to/get-resource-changes.md)do grafo de recursos do Azure.

O Controle de Alterações e o inventário obtêm seus dados de Azure Monitor. As máquinas virtuais conectadas a espaços de trabalho do Log Analytics usam agentes de Log Analytics para coletar dados sobre alterações em softwares instalados, serviços da Microsoft, registro e arquivos do Windows e quaisquer daemons do Linux em servidores monitorados. Quando os dados estão disponíveis, os agentes os enviam para Azure Monitor para processamento. Azure Monitor aplica a lógica aos dados recebidos, registra-os e torna-os disponíveis. 

O Controle de Alterações e o recurso de inventário habilita as áreas funcionais de inventário e controle de alterações na automação do Azure. Como ambas as áreas usam o mesmo agente de Log Analytics, o processo para adicionar uma VM é o mesmo em qualquer área funcional. 

> [!NOTE]
> Para usar o Controle de Alterações e o recurso de inventário, você deve localizar todas as suas VMs na mesma assinatura e região da conta de automação.

O Controle de Alterações e o inventário atualmente não dão suporte aos seguintes itens:

* Recursão para o rastreamento de registro do Windows
* Sistemas de filenetwork
* Métodos de instalação diferentes
* *arquivos **. exe** para Windows

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos em um ciclo de coleta de 30 minutos, o controle de alterações e o desempenho do inventário poderão ser degradados.
* Quando o tráfego de rede é alto, os registros de alteração podem levar até seis horas para serem exibidos.
* Se você modificar uma configuração enquanto um computador for desligado, o computador poderá postar alterações pertencentes à configuração anterior.

O Controle de Alterações e o inventário estão enfrentando os seguintes problemas:

* Atualizações de hotfix não são coletadas em computadores Windows Server 2016 Core RS3.
* Os daemons do Linux podem mostrar um estado alterado, embora nenhuma alteração tenha ocorrido. Esse problema ocorre devido à maneira como os `SvcRunLevels` dados no log do Azure monitor [ConfigurationChange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) são capturados.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Controle de Alterações e inventário têm suporte em todos os sistemas operacionais que atendem aos requisitos do Log Analytics Agent. As versões do sistema operacional Windows com suporte oficialmente são o Windows Server 2008 SP1 ou posterior e o Windows 7 SP1 ou posterior. Também há suporte para vários sistemas operacionais Linux. Consulte [visão geral do agente de log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

## <a name="network-requirements"></a>Requisitos de rede

O Controle de Alterações e o inventário exigem especificamente os endereços de rede listados na tabela a seguir. As comunicações com esses endereços usam a porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.Azure automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Interface do usuário de Controle de Alterações e inventário

Use Controle de Alterações e inventário na portal do Azure para exibir o resumo das alterações de computadores monitorados. O recurso está disponível selecionando uma das opções Adicionar VMs para o **controle de alterações** ou o **inventário** em gerenciamento de **configuração** em sua conta de automação.  

![Painel do Controle de Alterações](./media/change-tracking/change-tracking-dash01.png)

Os menus suspensos estão disponíveis na parte superior do painel para limitar o gráfico de controle de alterações e informações detalhadas com base no tipo de alteração e nos intervalos de tempo. Você também pode clicar e arrastrar no gráfico para selecionar um intervalo de tempo personalizado. 

Você pode clicar em uma alteração ou evento para exibir seus detalhes. Os tipos de alteração disponíveis são:

* Eventos
* Daemons
* Arquivos
* Registro
* Software
* Serviços da Microsoft

Você pode adicionar, modificar ou remover cada alteração. No exemplo a seguir, você pode ver uma alteração no tipo de inicialização de um serviço de manual para automático.

![Detalhes do Controle de Alterações](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>Acompanhamento de alterações de arquivo

Para controlar as alterações nos arquivos no Windows e no Linux, Controle de Alterações e inventário usam hashes MD5 dos arquivos. O recurso usa os hashes para detectar se foram feitas alterações desde o último inventário.

## <a name="tracking-of-file-content-changes"></a>Acompanhamento de alterações de conteúdo do arquivo

Controle de Alterações e inventário permite que você exiba o conteúdo de um arquivo Windows ou Linux antes e depois de uma alteração de arquivo. Para cada alteração em um arquivo, Controle de Alterações e inventário armazena o conteúdo do arquivo em uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Quando estiver controlando o arquivo, você poderá exibir seu conteúdo antes ou depois de uma alteração. Você pode exibir o conteúdo embutido ou lado a lado. 

![Exibir alterações em um arquivo](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Acompanhamento de chaves do registro

O Controle de Alterações e o inventário permitem o monitoramento de alterações nas chaves do registro. O monitoramento permite que você identifique pontos de extensibilidade em que o código de terceiros e o malware podem ser ativados. A tabela a seguir lista as chaves de registro pré-configuradas (mas não habilitadas). Para controlar essas chaves, você deve habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registro | Finalidade |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora as entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas em processo com o **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora scripts que são executados na inicialização.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora scripts que são executados no desligamento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora as chaves que são carregadas antes de o usuário entrar na conta do Windows. A chave é usada para aplicativos de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora as alterações às configurações do aplicativo.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora as entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas em processo com o **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora as entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas em processo com o **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone para aplicativos de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o Modelo de Objeto do Documento (DOM) da página atual e para controlar a navegação para aplicativos de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramenta personalizados para aplicativos de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados a wavemapper, wave1 e wave2, MSACM. imaadpcm,. MSADPCM,. msgsm610 e vidc. Semelhante à seção [Drivers] no arquivo **System. ini** .
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados a wavemapper, wave1 e wave2, MSACM. imaadpcm,. MSADPCM,. msgsm610 e vidc para aplicativos de 32 bits em execução em computadores de 64 bits. Semelhante à seção [Drivers] no arquivo **System. ini** .
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora a lista de DLLs de sistema conhecidas ou comumente usadas. Esse sistema impede que as pessoas explorem as permissões de diretório de aplicativo fracas descartando as versões do cavalo de Troia das DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora a lista de pacotes que podem receber notificações de eventos do **Winlogon. exe**, o modelo de suporte de logon interativo para Windows.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>Suporte para monitoramento de integridade de arquivo na central de segurança do Azure

Controle de Alterações e inventário fazem uso do [fim (monitoramento de integridade de arquivo) da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Embora o FIM monitore arquivos e registros apenas, o recurso completo de Controle de Alterações e inventário também inclui o acompanhamento para:

- Alterações de software
- Serviços da Microsoft
- Daemons Linux

## <a name="recursion-support"></a>Suporte à recursão

Controle de Alterações e inventário dão suporte à recursão, que permite que você especifique curingas para simplificar o rastreamento em diretórios. A recursão também fornece variáveis de ambiente para permitir que você rastreie arquivos entre ambientes com nomes de unidade ou de vários ou dinâmicos. A lista a seguir inclui informações comuns que você deve saber ao configurar a recursão:

* Caracteres curinga são necessários para acompanhar vários arquivos.
* Os curingas só podem ser usados no último segmento de um caminho, por exemplo, **arquivo\\c:\folder*** ou **/etc/*. conf**.
* Se uma variável de ambiente tiver um caminho inválido, a validação terá êxito, mas o caminho falhará durante a execução.
* Evite nomes de caminho gerais ao definir o caminho, pois esse tipo de configuração pode fazer com que muitas pastas sejam percorridas.

## <a name="change-tracking-and-inventory-data-collection"></a>Coleta de dados de Controle de Alterações e inventário

A tabela a seguir mostra a frequência de coleta de dados para os tipos de alterações com suporte pelo Controle de Alterações e pelo inventário. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas.

| **Alterar Tipo** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços da Microsoft | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de item acompanhados por computador para Controle de Alterações e inventário.

| **Recurso** | **Limite** |
|---|---|---|
|Arquivo|500|
|Registro|250|
|Software do Windows (sem incluir hotfixes) |250|
|Pacotes do Linux|1250|
|Serviços|250|
|Daemons|250|

A média Log Analytics uso de dados para um computador usando Controle de Alterações e inventário é de aproximadamente 40 MB por mês, dependendo do seu ambiente. Usando o recurso uso e custos estimados do espaço de trabalho Log Analytics, você pode exibir os dados ingeridos por Controle de Alterações e inventário em um gráfico de uso. Você pode usar essa exibição de dados para avaliar o uso de dados e determinar como ele está afetando sua fatura. Consulte [entender seu uso e estimar os custos](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs).  

### <a name="microsoft-service-data"></a>Dados de serviço da Microsoft

A frequência de coleta padrão para os serviços da Microsoft é de 30 minutos. Você pode configurar a frequência usando um controle deslizante na guia **serviços da Microsoft** em **Editar configurações**. 

![Controle deslizante de serviços da Microsoft](./media/change-tracking/windowservices.png)

Para otimizar o desempenho, o agente de Log Analytics controla apenas as alterações. Definir um limite alto pode perder alterações se o serviço for revertido para seu estado original. Definir a frequência como um valor menor permite que você confirme as alterações que podem ser perdidas caso contrário.

> [!NOTE]
> Embora o agente possa controlar as alterações até um intervalo de 10 segundos, os dados ainda levarão alguns minutos para serem exibidos na portal do Azure. As alterações que ocorrem durante o tempo de exibição no portal ainda são rastreadas e registradas.

## <a name="support-for-alerts-on-configuration-state"></a>Suporte para alertas no estado de configuração

Um recurso importante de Controle de Alterações e inventário é o alerta sobre alterações no estado de configuração do ambiente híbrido. Muitas ações úteis estão disponíveis para disparar em resposta a alertas, por exemplo, ações no Azure functions, runbooks de automação, WebHooks e similares. O alerta sobre alterações no arquivo **C:\Windows\System32\drivers\etc\hosts** para um computador é uma boa aplicação de alertas para controle de alterações e dados de inventário. Há muitos outros cenários de alerta também, incluindo os cenários de consulta definidos na tabela a seguir. 

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Files" e FileSystemPath contêm " c:\\windows\\system32\\drivers\\"|Útil para controlar alterações em arquivos críticos do sistema.|
|ConfigurationChange <br>&#124; em que FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Útil para controlar as modificações nos principais arquivos de configuração.|
|ConfigurationChange <br>&#124; em que ConfigChangeType = = "Microsoft Services" e SvcName contém "W3SVC" e SvcState = = "Stopped"|Útil para controlar alterações em serviços críticos do sistema.|
|ConfigurationChange <br>&#124; em que ConfigChangeType = = "daemons" e SvcName contém "ssh" e SvcState! = "em execução"|Útil para controlar alterações em serviços críticos do sistema.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Software" e ChangeCategory == "Added"|Útil para ambientes que precisam de configurações de software bloqueadas.|
|ConfigurationData <br>&#124; onde SoftwareName contém "agente de monitoramento" e CurrentVersion! = "8.0.11081.0"|Útil para ver quais computadores têm uma versão de software desatualizada ou não compatível instalada. Essa consulta relata o último estado de configuração relatado, mas não relata as alterações.|
|ConfigurationChange <br>&#124; em que RegistryKey = = @ "\\HKEY_LOCAL_MACHINE\\software\\Microsoft\\Windows\\CurrentVersion QualityCompat"| Útil para controlar alterações em chaves de antivírus cruciais.|
|ConfigurationChange <br>&#124; em que RegistryKey contém @ "\\HKEY_LOCAL_MACHINE\\System\\CurrentControlSet\\Services\\SharedAccess\\Parameters firewallpolicy"| Útil para controlar alterações nas configurações de firewall.|

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com Controle de Alterações e inventário em seus runbooks, consulte [gerenciar controle de alterações e inventário](change-tracking-file-contents.md).
* Para resolver erros com Controle de Alterações e inventário, consulte [solucionar problemas de controle de alterações e inventário](automation-tutorial-troubleshoot-changes.md).
* Use [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados de controle de alterações.
