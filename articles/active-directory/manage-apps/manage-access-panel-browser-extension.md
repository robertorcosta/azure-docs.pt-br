---
title: Solucionar problemas da extensão do painel de acesso do Azure para o IE | Microsoft Docs
description: Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763254"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Solucionar problemas da extensão do painel de acesso para o Internet Explorer

Este artigo ajuda você a solucionar os problemas a seguir:

* Você não consegue acessar seus aplicativos por meio do portal de meus aplicativos ao usar o Internet Explorer.
* Consulte a mensagem "Instalação de Software", mesmo que você já tenha instalado o software.

Se você for um administrador, consulte [como implantar a extensão do painel de acesso para o Internet Explorer usando o política de grupo](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Executar a ferramenta de diagnóstico

Você pode diagnosticar problemas de instalação com a extensão do painel de acesso baixando e executando a ferramenta de diagnóstico do painel de acesso. 

Para baixar e instalar a ferramenta de diagnóstico:

1. [Selecione este link para baixar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Abra o arquivo e extraia o conteúdo para o computador.
1. Para executar a ferramenta, clique com o botão direito do mouse no arquivo chamado *AccessPanelExtensionDiagnosticTool.js* e selecione **abrir com**  >  **o host de script baseado no Microsoft Windows**.

    ![Abrir com > Microsoft Windows com base em Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Examine os resultados do diagnóstico que aparecem e selecione **Sim** para corrigir os problemas. A caixa de diálogo **verificar resultados** é exibida com informações sobre o que fazer se a extensão não funcionar.  
1. Leia a mensagem e selecione **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está habilitada

Para verificar se você habilitou a extensão do painel de acesso no Internet Explorer:

1. No Internet Explorer, selecione o **ícone de engrenagem** no canto superior direito da janela e selecione opções da **Internet**.
1. Vá para a guia **programas** e selecione **gerenciar**Complementos.
1. Selecione **extensão do painel de acesso** na seção **Microsoft Corporation** e selecione **habilitar**.
1. Para salvar as alterações, feche todas as janelas do navegador Internet Explorer que você abriu. A alteração entrará em vigor na próxima vez que você abrir o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Habilitar extensões para navegação InPrivate

Para habilitar extensões para navegação InPrivate:

1. No Internet Explorer, selecione o **ícone de engrenagem** no canto superior direito da janela e selecione opções da **Internet**.
1. Vá para a guia **privacidade** e verifique se a caixa de seleção **desabilitar barras de ferramentas e extensões quando a navegação InPrivate é iniciada** está desmarcada.
1. Para salvar as alterações, feche todas as janelas do navegador Internet Explorer que você abriu. A alteração entrará em vigor na próxima vez que você abrir o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso do seu computador:

1. No painel de controle, procure *desinstalar*.
1. Nos resultados da pesquisa, selecione **desinstalar um programa**.

    ![Selecione a opção desinstalar um programa no painel de controle](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Na lista, selecione **extensão do painel de acesso** e selecione **desinstalar**.

    ![Desinstalar a extensão do painel de acesso](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Você pode tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se você encontrar problemas ao desinstalar a extensão, também poderá removê-la usando a ferramenta [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Artigos relacionados

* [Acesso a aplicativos e logon único com o Active Directory do Azure](what-is-single-sign-on.md)
* [Como implantar a extensão do painel de acesso para o Internet Explorer usando Política de Grupo](deploy-access-panel-browser-extension.md)
