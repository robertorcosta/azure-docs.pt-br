---
title: Usar o painel de configurações do Azure Application Insights Profiler | Microsoft Docs
description: Ver o status do Profiler e iniciar sessões de criação de perfil
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671623"
---
# <a name="configure-application-insights-profiler"></a>Configurar o Profiler do Application Insights

## <a name="updated-profiler-agent"></a>Agente de profiler atualizado
Os recursos do gatilho só funcionam com a versão 2.6 ou mais recente do agente profiler. Se você estiver executando um Serviço de Aplicativo Azure, seu agente será atualizado automaticamente. Você pode ver qual versão do agente que você está executando se você for para a URL kudu https://yourwebsite.scm.azurewebsites.net/diagnosticservicespara o seu site e anexar \DiagnosticServices até o final dele, assim: . O Webjob do Application Insights Profiler deve ser versão 2.6 ou mais recente. Você pode forçar uma atualização reiniciando seu aplicativo web. 

Se você estiver executando o profiler em uma VM ou Cloud Service, você precisa ter a versão 16.0.4 do Windows Azure Diagnostics (WAD) ou mais recente. Você pode verificar a versão do WAD fazendo login em sua VM e procurando este diretório: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. O nome do diretório é a versão do WAD que está instalada. O agente Azure VM atualizará o WAD automaticamente quando novas versões estiverem disponíveis.

## <a name="profiler-settings-page"></a>Página de configurações do profiler

Para abrir o painel de configurações do Azure Application Insights Profiler, vá para o painel de desempenho do Aplicativo Insights e, em seguida, selecione o botão **Configurar perfil.**

![Link para abrir página de configurações do Profiler][configure-profiler-entry]

Isso abre uma página que se parece com isso:

![Página de configurações do profiler][configure-profiler-page]

A página **Configurar o Perfil de Insights de Aplicativos** tem esses recursos:

| | |
|-|-|
Perfil Agora | Inicia a criação de perfil de sessões para todos os aplicativos que estão vinculados a esta instância do Application Insights.
Gatilhos | Permite configurar gatilhos que fazem com que o profiler seja executado. 
Sessões recentes de criação de perfil | Exibe informações sobre sessões de criação de perfil passadas.

## <a name="profile-now"></a>Perfil Agora
Esta opção permite que você inicie uma sessão de criação de perfil sob demanda. Quando você clicar neste link, todos os agentes de perfil que estão enviando dados para esta instância do Application Insights começarão a capturar um perfil. Após 5 a 10 minutos, a sessão de perfil aparecerá na lista abaixo.

Para que um usuário acione manualmente uma sessão de profiler, ele requer no mínimo acesso "gravar" em sua função para o componente Application Insights. Na maioria dos casos, você tem esse acesso automaticamente e nenhum trabalho adicional é necessário. Se você estiver tendo problemas, a função de escopo de assinatura a ser adicionado seria a função "Contribuinte componente do aplicativo Insights". [Veja mais sobre o controle de acesso de função com o Azure Monitoring](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Configurações do gatilho
![Configurações do gatilho Flyout][trigger-settings-flyout]

Clicar no botão Gatilhos na barra de menus abre a caixa de configurações do gatilho. Você pode configurar o gatilho para iniciar a criação de perfil quando a porcentagem de uso de CPU ou memória atingir o nível definido.

| | |
|-|-|
Botão ligado / desligado | On: profiler pode ser iniciado por este gatilho; Off: profiler não será iniciado por este gatilho.
Limiar de memória | Quando essa porcentagem de memória estiver em uso, o profiler será iniciado.
Duration | Define o tempo que o profiler executará quando acionado.
Resfriamento | Define o tempo que o profiler esperará antes de verificar o uso da memória ou da CPU novamente depois de acionado.

## <a name="recent-profiling-sessions"></a>Sessões recentes de criação de perfil
Esta seção da página mostra informações sobre sessões recentes de criação de perfil. Uma sessão de criação de perfil representa o período de tempo em que o agente de perfil estava fazendo um perfil em uma das máquinas que hospedam seu aplicativo. Você pode abrir os perfis de uma sessão clicando em uma das linhas. Para cada sessão, mostramos:

| | |
|-|-|
Acionado por | Como a sessão foi iniciada, seja por um gatilho, Profile Now ou amostragem padrão. 
Nome do aplicativo | Nome do aplicativo que foi perfilado.
Instância da máquina | Nome da máquina em que o agente de perfil funcionou.
Timestamp | Quando o perfil foi capturado.
Tracee | Número de vestígios que foram anexados a solicitações individuais.
CPU (%) | Porcentagem da CPU que estava sendo usada enquanto o profiler estava em execução.
Memória % | Porcentagem da memória que estava sendo usada enquanto o profiler estava em execução.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Use testes de desempenho web para gerar tráfego para seu aplicativo

Você pode disparar o Profiler manualmente com um único clique. Imagine que você está executando um teste de desempenho Web. Você precisará de vestígios para ajudá-lo a entender como seu aplicativo web está sendo executado carga. Ter controle sobre quando os rastreamentos são capturados é crucial, pois você sabe quando o teste de carga será executado. Mas o intervalo de amostragem aleatória poderá não detectar isso.

As próximas seções ilustram como esse cenário funciona:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passo 1: Gerar tráfego para o seu aplicativo web iniciando um teste de desempenho web

Se seu aplicativo da Web já tiver tráfego de entrada ou se você quiser gerar tráfego manualmente, pule esta seção e continue na Etapa 2.

1. No portal 'Insights do aplicativo', **selecione Configurar** > **teste de desempenho**. 

1. Selecione o botão **Novo** para iniciar um novo teste de desempenho.

   ![Criar novo teste de desempenho][create-performance-test]

1. No painel **Novo teste de desempenho**, configure a URL de destino do teste. Aceite todas as configurações padrão e selecione **Executar teste** para começar a execução do teste de carga.

    ![Configurar o teste de carga][configure-performance-test]

    O novo teste é colocado na fila primeiro, seguido por um status *em andamento*.

    ![O teste de carga é enviado e colocado na fila][load-test-queued]

    ![O teste de carga está com a execução em andamento][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passo 2: Inicie uma sessão de Profiler demanda

1. Quando o teste de carga estiver em execução, inicie o Profiler para capturar rastreamentos no aplicativo Web enquanto ele recebe a carga.

1. Vá para o painel **Configurar o Profiler**.


### <a name="step-3-view-traces"></a>Etapa 3: Exibir rastreamentos

Depois que o Profiler terminar a execução, siga as instruções na notificação para ir para o painel Desempenho e exibir os rastreamentos.

## <a name="next-steps"></a>Próximas etapas
[Habilitar o Profiler e ver os rastreios](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
