---
title: Solucionar problemas Azure Data Factory UX
description: Saiba como solucionar problemas de Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567864"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Solucionar problemas Azure Data Factory UX
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para o Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>A UX do ADF não está sendo carregada

> [!NOTE]
> O Azure Data Factory UX oficialmente dá suporte ao Microsoft Edge e ao Google Chrome. Usar outros navegadores da Web pode levar a um comportamento inesperado ou não documentado.

### <a name="third-party-cookies-blocked"></a>Cookies de terceiros bloqueados

O ADF UX usa cookies de navegador para manter a sessão do usuário e habilitar experiências de desenvolvimento e monitoramento interativas. É possível que seu navegador bloqueie cookies de terceiros porque você está usando uma sessão Incognito ou tem um bloqueador de anúncios habilitado. O bloqueio de cookies de terceiros pode causar problemas ao carregar o portal, como ser redirecionado para uma página em branco, https://adf.azure.com/accesstoken.html ou obter uma mensagem de aviso informando que cookies de terceiros estão bloqueados. Para resolver esse problema, habilite as opções de cookies de terceiros no seu navegador usando as seguintes etapas:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Permitir todos os cookies

1. Visite **Chrome://Settings/cookies** em seu navegador.
1. Opção Selecionar **permitir todos os cookies** ![ Chrome-permitir-todos-cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Atualize o UX do ADF e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Permitir que somente a UX do ADF use cookies
Se você não quiser permitir todos os cookies, você pode opcionalmente permitir apenas o ADF UX:
1. Visite **Chrome://Settings/cookies**.
1. Clique em **Adicionar** em **sites que sempre podem usar a opção cookies** adicionar o ![ ADF UX a sites permitidos](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Adicione o site **ADF.Azure.com** , marque a opção **todos os cookies** e salve. ![Permitir todos os cookies do site de UX do ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Atualize o UX do ADF e tente novamente.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Visite **Edge://Settings/Content/cookies** em seu navegador.
1. Certifique-se de que **permitir que sites salvem e leiam dados de cookie** esteja habilitado e que a opção **bloquear cookies de** terceiros esteja desabilitada ![ permitir todos os cookies no Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Atualize o UX do ADF e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Permitir que somente a UX do ADF use cookies

Se você não quiser permitir todos os cookies, você pode opcionalmente permitir apenas o ADF UX:

1. Visite **Edge://Settings/Content/cookies**.
1. Na seção **permitir** , clique em **Adicionar** e adicionar site **ADF.Azure.com** . ![Adicionar a UX do ADF a sites permitidos](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Atualize o UX do ADF e tente novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

* [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/)
* [Página de P e R da Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
