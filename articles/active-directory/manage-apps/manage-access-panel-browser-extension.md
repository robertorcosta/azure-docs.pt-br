---
title: Solucionar problemas na extensão do painel de acesso do Azure para IE | Microsoft Docs
description: Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723911"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Solucionar problemas na extensão do painel de acesso para o Internet Explorer

Este artigo ajuda você a solucionar os problemas a seguir:

* Você não consegue acessar seus aplicativos por meio do portal de meus aplicativos ao usar o Internet Explorer.
* Consulte a mensagem "Instalação de Software", mesmo que você já tenha instalado o software.

Se você é um admin, consulte [Como implantar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Execute a ferramenta de diagnóstico

Você pode diagnosticar problemas de instalação com a Extensão do Painel de Acesso baixando e executando a ferramenta de diagnóstico do Painel de Acesso. 

Para baixar e instalar a ferramenta de diagnóstico:

1. [Selecione este link para baixar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Abra o arquivo e extraia o conteúdo para o seu computador.
1. Para executar a ferramenta, clique com o botão direito do mouse no arquivo chamado *AccessPanelExtensionDiagnosticTool.js* e selecione **Abrir com** > **o Microsoft Windows Based Script Host**.

    ![Abrir com > Microsoft Windows com base em Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Revise os resultados de diagnóstico que aparecem e selecione **Sim** para corrigir os problemas. A caixa de diálogo **Resultados de verificação** aparece com informações sobre o que fazer se a extensão não funcionar.  
1. Leia a mensagem e selecione **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está habilitada

Para verificar se você habilitou a extensão do painel de acesso no Internet Explorer:

1. No Internet Explorer, selecione o **ícone Engrenagem** no canto superior direito da janela e selecione **opções**de Internet .
1. Vá para a guia **Programas** e selecione **Gerenciar complementos**.
1. Selecione **a extensão do painel** de acesso na seção Microsoft **Corporation** e selecione **Habilitar**.
1. Para salvar as alterações, feche todas as janelas do navegador do Internet Explorer que você tiver aberta. A mudança entra em vigor na próxima vez que você abrir o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Habilitar extensões para navegação inprivada

Para habilitar extensões para navegação inprivada:

1. No Internet Explorer, selecione o **ícone Engrenagem** no canto superior direito da janela e selecione **opções**de Internet .
1. Vá para a guia **Privacidade** e verifique se as **barras de ferramentas e extensões desativadas quando a navegação privada iniciar a** caixa de seleção estão limpas.
1. Para salvar as alterações, feche todas as janelas do navegador do Internet Explorer que você tiver aberta. A mudança entra em vigor na próxima vez que você abrir o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso do seu computador:

1. No Painel de Controle, procure *desinstalar*.
1. Nos resultados da pesquisa, **selecione Desinstalar um programa**.

    ![Selecione a opção Desinstalar uma opção de programa no Painel de Controle](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Na lista, selecione **Extensão do painel de acesso** e selecione **Desinstalar**.

    ![Desinstalar a extensão do painel de acesso](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Você pode tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se você encontrar problemas para desinstalar a extensão, você também pode removê-la usando a ferramenta [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Artigos relacionados

* [Acesso a aplicativos e logon único com o Active Directory do Azure](what-is-single-sign-on.md)
* [Como implantar a extensão do painel de acesso para o Internet Explorer usando a política de grupo](deploy-access-panel-browser-extension.md)
