---
title: Fluxo de trabalho da arquitetura VM do Windows Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos processos de fluxo de trabalho quando você implanta um serviço.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162161"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Fluxo de trabalho da arquitetura VM clássica do Windows Azure 
Este artigo fornece uma visão geral dos processos de fluxo de trabalho que ocorrem quando você implanta ou atualiza um recurso do Azure, como uma máquina virtual. 

> [!NOTE]
>O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Gerenciador de Recursos e Clássico. Este artigo aborda o uso do modelo de implantação clássica.

O diagrama a seguir apresenta a arquitetura dos recursos do Azure.

![Fluxo de trabalho azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Noções básicas do fluxo de trabalho
   
**A**. RDFE / FFE é o caminho de comunicação do usuário para o tecido. RDFE (RedDog Front End) é a API exposta publicamente que é a parte frontal do Portal de Gerenciamento e da API de Gerenciamento de Serviços, como Visual Studio, Azure MMC, e assim por diante.  Todas as solicitações do usuário passam pelo RDFE. FFE (Fabric Front End) é a camada que traduz solicitações de RDFE em comandos de malha. Todas as solicitações da RDFE passam pelo FFE para alcançar os controladores de malha.

**B**. O controlador de malha é responsável pela manutenção e monitoramento de todos os recursos do data center. Ele se comunica com agentes host de malha no sistema operacional de malha enviando informações como a versão do Sistema Operacional convidado, pacote de serviço, configuração de serviço e estado de serviço.

**C**. O Agente Host vive no Sistema Operacional Host e é responsável por configurar o Sistema Operacional convidado e se comunicar com o Agente Convidado (WindowsAzureGuestAgent) a fim de atualizar a função para um estado de meta pretendido e fazer verificações de batimentos cardíacos com o agente convidado. Se o Agente host não receber resposta de batimentos cardíacos por 10 minutos, o Agente host reinicializa o sistema operacional convidado.

**C2**. O WaAppAgent é responsável pela instalação, configuração e atualização do WindowsAzureGuestAgent.exe.

**D**.  O WindowsAzureGuestAgent é responsável pelo seguinte:

1. Configurando o Sistema Operacional convidado, incluindo firewall, ACLs, recursos do LocalStorage, pacote de serviços e configuração e certificados.
2. Configuração do SID para a conta de usuário em que a função será executada.
3. Comunicando o status da função ao tecido.
4. Iniciar o WaHostBootstrapper e monitorá-lo para ter certeza de que a função está no estado de meta.

**E**. O WaHostBootstrapper é responsável por:

1. Lendo a configuração da função e iniciando todas as tarefas e processos apropriados para configurar e executar a função.
2. Monitorando todos os seus processos infantis.
3. Elevando o evento StatusCheck no processo de host de função.

**F**. O IISConfigurator é executado se a função for configurada como uma função web Full IIS (não será executada para funções SDK 1.2 HWC). É responsável por:

1. Iniciando os serviços padrão de IIS
2. Configuração do módulo de reescrita na configuração web
3. Configuração do AppPool para a função configurada no modelo de serviço
4. Configurando o registro do IIS para apontar para a pasta DiagnosticStore LocalStorage
5. Configuração de permissões e ACLs
6. O site reside em %roleroot%:\sitesroot\0, e o AppPool aponta para este local para executar o IIS. 

**G**. As tarefas de inicialização são definidas pelo modelo de função e iniciadas pelo WaHostBootstrapper. As tarefas de inicialização podem ser configuradas para serem executadas em segundo plano de fundo de forma assíncrona, e o host bootstrapper iniciará a tarefa de inicialização e, em seguida, continuará com outras tarefas de inicialização. As tarefas de inicialização também podem ser configuradas para serem executadas no modo Simples (padrão), no qual o host bootstrapper aguardará a tarefa de inicialização para terminar de executar e retornar um código de saída de sucesso (0) antes de continuar para a próxima tarefa de inicialização.

**H**. Essas tarefas fazem parte do SDK e são definidas como plugins na definição de serviço da função (.csdef). Quando expandidos para tarefas de inicialização, o **DiagnosticsAgent** e **o RemoteAccessAgent** são únicos, pois cada um define duas tarefas de inicialização, uma regular e outra que tem um parâmetro **/blockStartup.** A tarefa normal de inicialização é definida como uma tarefa de inicialização em segundo plano para que possa ser executada em segundo plano enquanto a função em si estiver em execução. A tarefa de inicialização **/blockStartup** é definida como uma tarefa de inicialização simples para que o WaHostBootstrapper espere que ele saia antes de continuar. A tarefa **/blockStartup** aguarda que a tarefa regular termine de inicializar e, em seguida, ela sai e permite que o host bootstrapper continue. Isso é feito para que o diagnóstico e o acesso ao RDP possam ser configurados antes do início dos processos de função (isso é feito através da tarefa /blockStartup). Isso também permite que o diagnóstico e o acesso ao RDP continuem a ser executados depois que o bootstrapper host tiver terminado as tarefas de inicialização (isso é feito através da tarefa Normal).

**Eu.** WaWorkerHost é o processo de host padrão para funções normais de trabalhador. Esse processo de host hospeda todos os DLLs da função e o código de ponto de entrada, como OnStart e Run.

**J.** O WaWebHost é o processo de host padrão para funções da Web se eles estiverem configurados para usar o HWC (Hostable Web Core) compatível com SDK 1.2. As funções podem habilitar o modo HWC removendo o elemento da definição de serviço (.csdef). Neste modo, todos os códigos e DLLs do serviço são executados a partir do processo WaWebHost. O IIS (w3wp) não é usado e não há AppPools configurados no IIS Manager porque o IIS está hospedado dentro do WaWebHost.exe.

**K**. WaIISHost é o processo de host para código de ponto de entrada de função para funções da Web que usam IIS completo. Este processo carrega a primeira DLL que é encontrada que usa a classe **RoleEntryPoint** e executa o código desta classe (OnStart, Run, OnStop). Todos os eventos **RoleEnvironment** (como StatusCheck e Changed) criados na classe RoleEntryPoint são levantados neste processo.

**L**. W3WP é o processo padrão do trabalhador IIS que é usado se a função estiver configurada para usar o IIS Completo. Isso executa o AppPool configurado a partir do IISConfigurator. Todos os eventos RoleEnvironment (como StatusCheck e Changed) que são criados aqui são levantados neste processo. Observe que os eventos RoleEnvironment serão acionados em ambos os locais (WaIISHost e w3wp.exe) se você se inscrever em eventos em ambos os processos.

## <a name="workflow-processes"></a>Processos de fluxo de trabalho

1. Um usuário faz uma solicitação, como carregar arquivos ".cspkg" e ".cscfg", dizendo a um recurso para parar ou fazer uma alteração de configuração, e assim por diante. Isso pode ser feito através do portal Azure ou de uma ferramenta que usa a API de Gerenciamento de Serviços, como o recurso Visual Studio Publish. Esta solicitação vai para rdfe para fazer todo o trabalho relacionado à assinatura e, em seguida, comunicar a solicitação à FFE. O resto dessas etapas do fluxo de trabalho é implantar um novo pacote e iniciá-lo.
2. O FFE encontra o pool correto da máquina (com base na entrada do cliente, como grupo de afinidade ou localização geográfica mais entrada do tecido, como disponibilidade da máquina) e se comunica com o controlador de tecido mestre nesse pool de máquinas.
3. O controlador de malha encontra um host que tem núcleos de CPU disponíveis (ou gira um novo host). O pacote de serviço e a configuração são copiados para o host, e o controlador de malha se comunica com o agente host no sistema operacional host para implantar o pacote (configurar DIPs, portas, sistema operacional convidado e assim por diante).
4. O agente host inicia o Sistema Operacional convidado e se comunica com o agente convidado (WindowsAzureGuestAgent). O anfitrião envia batimentos cardíacos ao hóspede para ter certeza de que o papel está trabalhando em direção ao seu estado objetivo.
5. O WindowsAzureGuestAgent configura o sistema operacional convidado (firewall, ACLs, LocalStorage e assim por diante), copia um novo arquivo de configuração XML para c:\Config e, em seguida, inicia o processo WaHostBootstrapper.
6. Para funções web Full IIS, o WaHostBootstrapper inicia o IISConfigurator e diz-lhe para excluir quaisquer AppPools existentes para a função web do IIS.
7. WaHostBootstrapper lê as tarefas de **inicialização** de E:\RoleModel.xml e começa a executar tarefas de inicialização. WaHostBootstrapper espera até que todas as tarefas de inicialização simples tenham terminado e retornauma mensagem de "sucesso".
8. Para funções web Full IIS, WaHostBootstrapper diz ao IISConfigurator para `E:\Sitesroot\<index>`configurar `<index>` o IIS AppPool e `<Sites>` aponta o site para , onde está um índice baseado em 0 no número de elementos definidos para o serviço.
9. O WaHostBootstrapper iniciará o processo de host dependendo do tipo de função:
    1. **Função do trabalhador**: WaWorkerHost.exe é iniciado. WaHostBootstrapper executa o método OnStart(). Depois que ele retorna, waHostBootstrapper começa a executar o método Executar() e, em seguida, marca simultaneamente a função como Pronto e coloca-o na rotação do balanceador de carga (se InputEndpoints for definido). WaHostBootsrapper então entra em um loop de verificação do status da função.
    1. **SDK 1.2 HWC Web Role**: O WaWebHost é iniciado. WaHostBootstrapper executa o método OnStart(). Depois que ele retorna, WaHostBootstrapper começa a executar o método Executar() e, em seguida, marca simultaneamente a função como Pronto e coloca-o na rotação do balanceador de carga. O WaWebHost emite uma solicitação de aquecimento (GET /do.rd_runtime_init). Todas as solicitações da Web são enviadas para WaWebHost.exe. WaHostBootsrapper então entra em um loop de verificação do status da função.
    1. **Função web completa do IIS**: aIISHost é iniciada. WaHostBootstrapper executa o método OnStart(). Depois que ele retorna, ele começa a executar o método Run() e, em seguida, marca simultaneamente a função como Pronto e coloca-o na rotação do balanceador de carga. WaHostBootsrapper então entra em um loop de verificação do status da função.
10. As solicitações de entrada na Web para uma função web IIS completa acionam o IIS para iniciar o processo W3WP e atender à solicitação, o mesmo que seria em um ambiente IIS no local.

## <a name="log-file-locations"></a>Localizações de arquivos de log

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Este registro contém alterações no serviço, incluindo inícios, paradas e novas configurações. Se o serviço não mudar, você pode esperar ver grandes lacunas de tempo neste arquivo de log.
- C:\Logs\WaAppAgent.Log.  
Este registro contém atualizações de status e notificações de batimentos cardíacos e é atualizado a cada 2-3 segundos.  Este registro contém uma visão histórica do status da instância e lhe dirá quando a ocorrência não estava no estado Pronto.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaiISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Registros IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Registros de eventos do Windows**

`D:\Windows\System32\Winevt\Logs`
 



