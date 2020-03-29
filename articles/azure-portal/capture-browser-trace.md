---
title: Capture um rastreamento do navegador para solução de problemas | Microsoft Docs
description: Capture informações de rede de um rastreamento do navegador para ajudar a solucionar problemas com o portal Azure.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310689"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Capturar um rastreamento de navegador para solucionar problemas

Se você estiver solucionando um problema com o portal Azure e precisar entrar em contato com o suporte da Microsoft, recomendamos que primeiro capture um rastreamento do navegador e algumas informações adicionais. As informações coletadas podem fornecer detalhes importantes sobre o portal no momento em que o problema ocorre. Siga os passos deste artigo para as ferramentas de desenvolvedor no navegador que você usa: Google Chrome ou Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) ou Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Chromium)

O Google Chrome e o Microsoft Edge (Chromium) são ambos baseados no [projeto de código aberto Chromium](https://www.chromium.org/Home). As etapas a seguir mostram como usar as ferramentas do desenvolvedor, que são muito semelhantes nos dois navegadores. Para obter mais informações, consulte [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) e [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Faça login no [portal Azure](https://portal.azure.com). É importante fazer login _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas ao seu login. 

1. Comece a gravar os passos que você toma no portal, usando [o Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até a etapa pouco antes de onde o problema ocorre.

1. Pressione F12 ![ou selecione](media/capture-browser-trace/chromium-icon-settings.png) > Captura de tela do ícone de configurações do navegador**Mais ferramentas** > **do desenvolvedor**.

1. Por padrão, o navegador mantém as informações de rastreamento apenas para a página que está carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua repro exija ir para mais de uma página:

    1. Selecione a guia **Rede** e selecione **'Preservar log ''''Preservar'.**

          ![Captura de tela de "Preserve log"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecione a guia **Console,** selecione **Configurações do console**e selecione **Preservar log**. Selecione **novamente as configurações do console** para fechar o painel de configurações.

          ![Captura de tela de "Preserve Log"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecione a guia **Rede** e selecione **Parar de gravar registro de rede** e **Limpar**.

    ![Captura de tela de "Stop recording network log" e "Clear"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecione **Registro de registro de**rede e reproduza o problema no portal.

    ![Captura de tela de "Iniciar sessão de criação de perfil"](media/capture-browser-trace/chromium-start-session.png)

    Você verá a saída da sessão semelhante à seguinte imagem.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Parar de gravar registro de rede,** em seguida, selecione Exportar **HAR** e salvar o arquivo.

    ![Captura de tela de "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Pare o Gravador de Passos e salve o arquivo.

1. De volta ao painel de ferramentas do desenvolvedor do navegador, selecione a guia **Console.** Clique com o botão direito do mouse e selecione **Salvar como...** e salve a saída do console em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/chromium-console-select.png)

1. Empacote o arquivo HAR, a saída do console e a gravação de tela em um formato compactado como .zip e compartilhe isso com o suporte da Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

As etapas a seguir mostram como usar as ferramentas de desenvolvedor no Microsoft Edge (EdgeHTML). Para obter mais informações, consulte [as ferramentas de desenvolvedor do Microsoft Edge (EdgeHTML).](/microsoft-edge/devtools-guide)

1. Faça login no [portal Azure](https://portal.azure.com). É importante fazer login _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas ao seu login. 

1. Comece a gravar os passos que você toma no portal, usando [o Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até a etapa pouco antes de onde o problema ocorre.

1. Pressione F12 ![ou selecione](media/capture-browser-trace/edge-icon-settings.png) > Captura de tela do ícone de configurações do navegador**Mais ferramentas** > **do desenvolvedor**.

1. Por padrão, o navegador mantém as informações de rastreamento apenas para a página que está carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua repro exija ir para mais de uma página:

    1. Selecione a guia **Rede** e, em seguida, limpe a opção **Limpar entradas na navegação**.

          ![Captura de tela de "Limpar entradas na navegação"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecione a guia **Console** e selecione **'Preservar log**' .

          ![Captura de tela de "Preserve Log"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecione a guia **Rede** e selecione **'Parar de traçar perfil'** e **limpar a sessão**.

    ![Captura de tela de "Stop perfilfiling session" e "Clear session"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecione **Iniciar sessão de criação de perfil**e, em seguida, reproduza o problema no portal.

    ![Captura de tela de "Iniciar sessão de criação de perfil"](media/capture-browser-trace/edge-start-session.png)

    Você verá a saída da sessão semelhante à seguinte imagem.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/edge-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Parar de traçar perfil,** em seguida, selecione **Exportar como HAR** e salve o arquivo.

    ![Captura de tela de "Exportar como HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Pare o Gravador de Passos e salve o arquivo.

1. De volta ao painel de ferramentas do desenvolvedor do navegador, selecione a guia **Console** e expanda a janela. Coloque o cursor no início da saída do console e arraste e selecione todo o conteúdo da saída. Clique com o botão direito do mouse e selecione **Copiar**e salve a saída do console em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/edge-console-select.png)

1. Empacote o arquivo HAR, a saída do console e a gravação de tela em um formato compactado como .zip e compartilhe isso com o suporte da Microsoft.

## <a name="apple-safari"></a>Apple Safari

As etapas a seguir mostram como usar as ferramentas de desenvolvedor no Apple Safari. Para obter mais informações, consulte [a visão geral do Safari Developer Tools](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Habilite as ferramentas de desenvolvedor no Apple Safari:

    1. Selecione **Safari**e selecione **Preferências**.

        ![Captura de tela das preferências do Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selecione a guia **Avançado** e selecione **Mostrar desenvolver menu na barra de menu**.

        ![Captura de tela das preferências avançadas do Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Faça login no [portal Azure](https://portal.azure.com). É importante fazer login _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas ao seu login. 

1. Comece a gravar os passos que você toma no portal. Para obter mais informações, consulte [Como gravar a tela em seu Mac](https://support.apple.com/HT208721).

1. No portal, navegue até a etapa pouco antes de onde o problema ocorre.

1. Selecione **Desenvolver**e selecione **Mostrar Inspetor Web**.

    ![Captura de tela de "Show Web Inspector"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Por padrão, o navegador mantém as informações de rastreamento apenas para a página que está carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua repro exija ir para mais de uma página:

    1. Selecione a **guia Rede** e selecione **'Preservar log**''

          ![Captura de tela de "Preserve Log"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecione a guia **Console** e selecione **'Preservar log**' .

          ![Captura de tela de "Preserve Log"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecione a **guia Rede** e selecione **'Limpar itens de rede'.**

    ![Captura de tela de "Itens de rede clara"](media/capture-browser-trace/safari-clear-session.png)

1. Reproduza o problema no portal. Você verá a saída da sessão semelhante à seguinte imagem.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/safari-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Exportar** e salvar o arquivo.

    ![Captura de tela de "Exportação"](media/capture-browser-trace/safari-network-export-har.png)

1. Pare o gravador de tela e salve o arquivo.

1. De volta ao painel de ferramentas do desenvolvedor do navegador, selecione a guia **Console** e expanda a janela. Coloque o cursor no início da saída do console e arraste e selecione todo o conteúdo da saída. Use o Comando-C para copiar a saída e salvá-la em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/safari-console-select.png)

1. Empacote o arquivo HAR, a saída do console e a gravação de tela em um formato compactado como .zip e compartilhe isso com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

[Visão geral do portal do Azure](azure-portal-overview.md)