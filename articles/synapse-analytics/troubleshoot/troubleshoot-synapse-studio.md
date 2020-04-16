---
title: Solução de problemas Azure Synapse Studio (pré-visualização)
description: Solução de problemas Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431313"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure Synapse Studio (pré-visualização) solução de problemas

Este guia de solução de problemas fornece instruções sobre quais informações fornecer ao abrir um ticket de suporte em problemas de conectividade de rede. Com as informações adequadas, podemos resolver o problema mais rapidamente.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Problema de conectividade de serviço sql sob demanda (pré-visualização)

### <a name="symptom-1"></a>Sintoma 1

A opção "SQL on-demand" está acinzentada na lista suspensa "Conectar-se".

![sintoma1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Sintoma 2

A execução da consulta com "SQL on-demand" dá a mensagem de erro "Falha ao estabelecer conexão com servidor".

![sintoma2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

> [!NOTE] 
>    As seguintes etapas de solução de problemas são para Chromium Edge e Chrome. Você pode usar outros navegadores (como o FireFox) com as mesmas etapas de solução de problemas, mas a janela "Ferramenta de desenvolvedor" pode ter layout diferente das capturas de tela neste TSG. Se possível, NÃO use o Borda clássico para solução de problemas, pois pode mostrar informações imprecisas em determinada situação.

Abra o painel "Informações de diagnóstico", selecione o botão "Baixar diagnóstico". Mantenha as informações baixadas para relatórios de erros. Em vez disso, copie o "Session ID" e anexe-o ao abrir o bilhete de suporte.

![diagnóstico-informações](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Para começar a solucionar problemas, tente novamente a operação que realizou no Azure Synapse Studio.

- Para o sintoma 1, selecione o botão "Atualizar" à direita da lista de paradas "Usar banco de dados" na guia "SQL script" e verifique se você pode ver "SQL sob demanda".
- Para o sintoma 2, tente executar a consulta novamente para ver se ela é executada com sucesso.

Se o problema ainda existir, pressione F12 no seu navegador para abrir "Ferramentas de desenvolvedor" (DevTools).

Na janela "Ferramentas do desenvolvedor", mude para o painel "Rede". Selecione o botão "Limpar" na barra de ferramentas no painel "Rede", se necessário.
Certifique-se de que "Desativar cache" no painel "Rede" seja verificado.

Tente novamente a operação que realizou no Azure Synapse Studio. Você pode ver novos itens mostrados na lista "Rede" em "Ferramentas de desenvolvedor". Observe o tempo atual do sistema para fornecer no bilhete de suporte.

![painel de rede](media/troubleshooting-synapse-studio/network-panel.png)

Encontre o item cuja coluna de Url corresponde ao seguinte padrão:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Onde [*A*] é o seu nome de espaço de trabalho, e "-ondemand" poderia ser "-sqlod" e onde [*B*] deve ser um nome de banco de dados, como "mestre". Deve haver no máximo dois itens com o mesmo valor de URL, mas valores de método diferentes; OPÇÕES e CORREIO. Verifique se esses dois itens têm "200" ou "20x" sob a coluna de status, onde "x" pode ser de qualquer dígito.

Se qualquer um deles tiver algo além de "20x" e:

- o status começa com "(falha)", ampliando a coluna "Status" ou sobreriando o ponteiro sobre o texto de status para ver o texto completo. Inclua o texto e/ou captura de tela ao abrir o bilhete de suporte.

    ![status-texto](media/troubleshooting-synapse-studio/status-text.png)

    - Se você ver ERR_NAME_NOT_RESOLVED e criou seu espaço de trabalho em 10 minutos, espere 10 minutos e tente novamente para ver se o problema ainda existe.
    - Se você ver ERR_INTERNET_DISCONNECTED ou ERR_NETWORK_CHANGED, pode indicar que sua conexão de rede do PC está tendo problemas. Verifique sua conexão de rede e tente novamente a operação.
    - Se você vir ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR ou outros códigos de erro contendo "SSL", isso pode indicar que sua configuração SSL local está tendo problemas ou o administrador da rede bloqueou o acesso ao servidor sob demanda SQL. Abra um bilhete de suporte e anexe o código de erro na descrição.
    - Se você vir ERR_NETWORK_ACCESS_DENIED, talvez seja necessário verificar com o administrador se a política de firewall local bloqueou o acesso ao domínio *.database.windows.net ou à porta remota 1443.
    - Opcionalmente, tente a mesma operação imediatamente em um ambiente diferente de máquina e/ou rede para excluir um problema de configuração de rede em seu PC.

- o status é "40x", "50x" ou outros números, selecione no item(s) para ver os detalhes. Você deve ver os detalhes do item à direita. Encontre a seção "Cabeçalho de resposta"; em seguida, verifique se existe um item chamado "access-control-allow-origin". Nesse caso, verifique se ele tem um dos seguintes valores:

    - `*`(asterisco único)
    - https://web.azuresynapse.net/(ou outro valor com o que o texto na barra de endereços do seu navegador começa com)

Se o cabeçalho de resposta contém um dos valores acima, significa que já deveríamos ter coletado as informações de falha. Você pode abrir um ticket de suporte se necessário e, opcionalmente, anexar a captura de tela dos detalhes do item.

Se você não puder ver o cabeçalho ou o cabeçalho não tiver um dos valores listados acima, anexe uma captura de tela dos detalhes do item ao abrir o bilhete.

![detalhes do item](media/troubleshooting-synapse-studio/item-details.png)

Se as etapas acima não resolverem seu problema, você pode precisar abrir um ticket de suporte. Ao enviar seu bilhete de suporte, inclua o "Session ID" ou "Diagnostic Info" baixado no início deste guia.

Ao relatar o problema, você pode, opcionalmente, tirar uma captura de tela da sua guia "Console" na "Ferramentas do Desenvolvedor" e anexá-la ao bilhete de suporte. Role o conteúdo e tire mais de uma captura de tela, se necessário, para capturar toda a mensagem.

![desenvolvedor-ferramenta-console](media/troubleshooting-synapse-studio/developer-tool-console.png)

Se você estiver anexando capturas de tela, forneça o tempo (ou um intervalo de tempo estimado) de quando você tirou as capturas de tela. Vai nos ajudar quando olhar para o problema.

Certos navegadores suportam mostrar carimbos na guia "Console". Para O Chromium Edge/Chrome, abra a caixa de diálogo "Configurações" em "Ferramentas do desenvolvedor" e marque "Mostrar carimbos de tempo" na guia "Preferências".

![configurações de desenvolvedor-ferramenta-console](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-time-carimbo](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Próximas etapas
Se as etapas anteriores não ajudarem a resolver o problema [Crie um ticket de suporte](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
