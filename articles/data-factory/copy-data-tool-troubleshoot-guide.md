---
title: Solução de problemas Copiar Dados ferramenta no Azure Data Factory
description: Saiba como solucionar problemas de ferramenta de Copiar Dados no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388342"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Solução de problemas Copiar Dados ferramenta no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para Copiar Dados ferramenta no Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Código de erro: não é possível validar na ferramenta de Copiar Dados

- **Sintomas**: na primeira etapa da ferramenta de copiar dados, você encontra a mensagem de aviso "não é possível validar".
- **Causas**: isso pode acontecer quando todos os cookies de terceiros estiverem desabilitados.
- **Resolução**: 
    - Use o Internet Explorer ou o navegador Microsoft Edge.
    - Se você estiver usando o navegador Chrome, siga as instruções abaixo para adicionar a exceção de cookies para *microsoftonline.com* e *Windows.net*.
        1.  Abra o navegador Chrome.
        2.  Clique na chave inglesa ou em três linhas à direita (personalizar e controlar o Google Chrome).
        3.  Clique em **Configurações**.
        4.  Pesquise **cookies** ou vá para **privacidade** em configurações avançadas.
        5.  Selecione **configurações de conteúdo**.    
        6.  Os cookies devem ser definidos para **permitir que os dados locais sejam definidos (recomendado)**.
        7.  Clique em **gerenciar exceções**. Em **padrão de nome de host** , insira o seguinte e certifique-se de **que permitir** é o conjunto de comportamento.
            - login.microsoftonline.com
            - login.windows.net
        8.  Feche o navegador e reinicie.
    - Se você estiver usando o navegador Firefox, siga as instruções abaixo para adicionar a exceção de cookies.
        1. No menu Firefox, vá para **ferramentas**  >  **Opções**.
        2. Em **Privacy**  >  **histórico**de privacidade, seu pode ver que a configuração atual é **usar configurações personalizadas para o histórico**.
        3. Em **aceitar cookies de terceiros**, sua configuração atual pode **nunca**ser, em seguida, clique em **exceções** à direita para adicionar os sites a seguir.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Feche o navegador e reinicie. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Código de erro: não é possível abrir a página de logon e digitar a senha

- **Sintomas**: a ferramenta de copiar dados redireciona para a página de logon, mas a página de logon não é mostrada com êxito.
- **Causas**: esse problema pode ocorrer se você alterou o ambiente de rede da rede do Office para a rede doméstica. Há alguns caches em navegadores. 
- **Resolução**: 
    1.  Feche o navegador e tente novamente. Vá para a próxima etapa se o problema ainda existir.   
    2.  Se você estiver usando o navegador Internet Explorer, tente abri-lo no modo particular (pressione "CTRL" + "Shift" + "P"). Se você estiver usando o navegador Chrome, tente abri-lo no modo Incognito (pressione "CTRL" + "Shift" + "N"). Vá para a próxima etapa se o problema ainda existir. 
    3.  Tente usar outro navegador. 


## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:
*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de perguntas e respostas da Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento do ADF](concepts-data-flow-performance.md)
