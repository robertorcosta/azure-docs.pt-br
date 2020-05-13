---
title: Monitorar Key Vault com Azure Monitor para Key Vault (versão prévia) | Microsoft Docs
description: Este artigo descreve o Azure Monitor para cofres de chaves.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 2387f14a537c15c891bff968573bf4d698c01770
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211297"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitorando o serviço de cofre de chaves com Azure Monitor para Key Vault (versão prévia)
Azure Monitor para Key Vault (versão prévia) fornece monitoramento abrangente de seus cofres de chaves, fornecendo uma exibição unificada de suas solicitações de Key Vault, desempenho, falhas e latência.
Este artigo o ajudará a entender como integrar e personalizar a experiência de Azure Monitor para Key Vault (versão prévia).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Introdução ao Azure Monitor para Key Vault (versão prévia)

Antes de entrar na experiência, você deve entender como ele apresenta e visualiza informações.
-    **Na perspectiva da escala** , mostrando uma exibição de instantâneo do desempenho com base nas solicitações, análise de falhas e uma visão geral das operações e da latência.
-   **Análise de busca detalhada** de um cofre de chaves específico para executar uma análise detalhada.
-    **Personalizável** , onde você pode alterar quais métricas deseja ver, modificar ou definir limites que se alinham com seus limites e salvar sua própria pasta de trabalho. Os gráficos na pasta de trabalho podem ser fixados nos painéis do Azure.

Azure Monitor para Key Vault combina logs e métricas para fornecer uma solução de monitoramento global. Todos os usuários podem acessar os dados de monitoramento baseados em métricas, no entanto, a inclusão de visualizações baseadas em logs pode exigir que os usuários [habilitem o registro em log de seus Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Configurando seus cofres de chaves para monitoramento

> [!NOTE]
> A habilitação de logs é um serviço pago que fornece recursos de monitoramento adicionais.

1. A guia de latência de & de operações ajuda a determinar quantos e quais cofres de chaves estão habilitados. Para começar a coletar, selecione o botão **habilitar** , que o levará a uma pasta de trabalho separada que lista os cofres de chaves que exigem a habilitação dos logs de diagnóstico.

    ![Captura de tela da guia operações e latência com o botão habilitar azul exibido](./media/key-vaults-insights-overview/enable-logging.png)

2. Para habilitar os logs de diagnóstico, clique no link **habilitar** abaixo da coluna ações e crie uma nova configuração de diagnóstico que envia logs para um espaço de trabalho log Analytics. É recomendável enviar todos os logs para o mesmo espaço de trabalho.

3. Depois que as configurações de diagnóstico forem salvas, você poderá exibir todos os gráficos baseados em log e as visualizações abaixo do Key Vault insights. Observe que pode levar vários minutos até horas para começar a popular os logs.

4. Para obter assistência adicional sobre como habilitar os logs de diagnóstico para seu serviço de Key Vault, leia o [guia completo](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="view-from-azure-monitor"></a>Exibir de Azure Monitor

Em Azure Monitor, você pode exibir detalhes de solicitação, latência e falha de vários cofres de chaves em sua assinatura e ajudar a identificar problemas de desempenho e cenários de limitação.

Para exibir a utilização e as operações de suas contas de armazenamento em todas as suas assinaturas, execute as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Selecione **Monitor** no painel esquerdo na portal do Azure e, na seção insights, selecione **cofres de chaves (versão prévia)**.

![Captura de tela da experiência de visão geral com vários grafos](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Pasta de trabalho de visão geral

Na pasta de trabalho Visão geral da assinatura selecionada, a tabela exibe métricas interativas do cofre de chaves para cofres de chaves agrupados na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* Assinaturas – somente as assinaturas que têm cofres de chaves são listadas.

* Cofres de chaves – por padrão, somente até 5 cofres de chaves são previamente selecionados. Se você selecionar todos ou vários cofres de chaves no seletor de escopo, até 200 cofres de chave serão retornados. Por exemplo, se você tivesse um total de 573 Key Vaults em três assinaturas que você selecionou, somente os cofres 200 serão exibidos.

* Intervalo de tempo – por padrão, exibe as últimas 24 horas de informações com base nas seleções correspondentes feitas.

O bloco do contador, na lista suspensa, acumula o número total de cofres de chaves nas assinaturas selecionadas e reflete quantos são selecionados. Há um calor codificado por cor condicional para as colunas da pasta de trabalho que relatam as métricas de solicitação, falhas e latência. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

## <a name="failures-workbook"></a>Pasta de trabalho falhas

Selecione **falhas** na parte superior da página e a guia falhas é aberta. Ele mostra as ocorrências da API, a frequência ao longo do tempo, juntamente com a quantidade de determinados códigos de resposta.

![Captura de tela de falhas na pasta de trabalho](./media/key-vaults-insights-overview/failures.png)

Há codificação de cor condicional ou calor para colunas na pasta de trabalho que relatam métricas de ocorrências de API com um valor azul. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

A pasta de trabalho exibe sucessos (códigos de status 2xx), erros de autenticação (códigos de status 401/403), limitação (códigos de status 429) e outras falhas (códigos de status de 4xx).

Para entender melhor o que cada um dos códigos de status representam, é recomendável ler a documentação em [Azure Key Vault status e códigos de resposta](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses).

## <a name="operations--latency-workbook"></a>Pasta de trabalho de latência & de operações

Selecione as **operações & latência** na parte superior da página e a guia **operações & latência** é aberta. Esta guia permite que você integre seus cofres de chaves para monitoramento. Para obter etapas mais detalhadas, consulte a seção [Configurando seus cofres de chaves para monitoramento](#configuring-your-key-vaults-for-monitoring) .

Você pode ver quantos dos cofres de chaves estão habilitados para o registro em log. Se pelo menos um cofre tiver sido configurado corretamente, você poderá ver as tabelas que exibem as operações e os códigos de status de cada um dos cofres de chaves. Você pode clicar na seção de detalhes de uma linha para obter informações adicionais sobre a operação individual.

![Captura de tela de operações e gráficos de latência](./media/key-vaults-insights-overview/logs.png)

Se você não estiver vendo dados para esta seção, faça referência à seção superior sobre como habilitar logs para Azure Key Vault ou consulte a seção solução de problemas abaixo.

## <a name="view-from-a-key-vault-resource"></a>Exibir de um recurso de Key Vault

Para acessar Azure Monitor para Key Vault diretamente de um cofre de chaves:

1. Na portal do Azure, selecione cofres de chaves.

2. Na lista, escolha um cofre de chaves. Na seção monitoramento, escolha insights (versão prévia).

Essas exibições também podem ser acessadas selecionando o nome do recurso de um cofre de chaves da pasta de trabalho do nível de Azure Monitor.

![Captura de tela da exibição de um recurso do Key Vault](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Na pasta de trabalho **visão geral** do cofre de chaves, ele mostra várias métricas de desempenho que ajudam você a avaliar rapidamente:

- Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados a transações, latência e disponibilidade do cofre de chaves.

- Os blocos métricas e de status realçam a disponibilidade do serviço, a contagem total de transações para o recurso do cofre de chaves e a latência geral.

A seleção de qualquer uma das outras guias para **falhas** ou **operações** abre as respectivas pastas de trabalho.

![Captura de tela da exibição de falhas](./media/key-vaults-insights-overview/resource-failures.png)

A pasta de trabalho de falhas detalha os resultados de todas as solicitações do cofre de chaves no intervalo de tempo selecionado e fornece a categorização em sucessos (2xx), erros de autenticação (401/403), limitação (429) e outras falhas.

![Captura de tela da exibição de operações](./media/key-vaults-insights-overview/operations.png)

A pasta de trabalho de operações permite aos usuários aprofundar-se nos detalhes completos de todas as transações, que podem ser filtradas pelo status do resultado usando os blocos de nível superior.

![Captura de tela da exibição de operações](./media/key-vaults-insights-overview/info.png)

Os usuários também podem criar o escopo de exibições com base em tipos de transação específicos na tabela superior, que atualiza dinamicamente a tabela inferior, em que os usuários podem exibir detalhes completos da operação em um painel de contexto pop-up.

>[!NOTE]
> Observe que os usuários devem ter as configurações de diagnóstico habilitadas para exibir esta pasta de trabalho. Para saber mais sobre como habilitar a configuração de diagnóstico, leia mais sobre o [log de Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging).

## <a name="pin-and-export"></a>Fixar e exportar

Você pode fixar qualquer uma das seções de métrica em um painel do Azure selecionando o ícone de pino na parte superior direita da seção.

As pastas de trabalho Visão geral ou falhas de várias assinaturas e cofres de chaves dão suporte à exportação dos resultados no formato do Excel selecionando o ícone de download à esquerda do ícone de pino.

![Captura de tela do ícone de pino selecionado](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalizar Azure Monitor para Key Vault

Esta seção destaca os cenários comuns para editar a pasta de trabalho para personalizar o suporte às suas necessidades de análise de dados:
*  Escopo a pasta de trabalho para sempre selecionar uma assinatura específica ou cofre (s) de chaves
* Alterar as métricas na grade
* Alterar o limite de solicitações
* Alterar a renderização de cor

Você pode iniciar as personalizações habilitando o modo de edição, selecionando o botão **Personalizar** na barra de ferramentas superior.

![Captura de tela do botão Personalizar](./media/key-vaults-insights-overview/customize.png)

As personalizações são salvas em uma pasta de trabalho personalizada para evitar a substituição da configuração padrão em nossa pasta de trabalho publicada. As pastas de trabalho são salvas em um grupo de recursos, na seção meus relatórios, que é particular para você ou na seção relatórios compartilhados, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a Galeria de pastas de trabalho para iniciá-la.

![Captura de tela da Galeria de pastas de trabalho](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Especificando uma assinatura ou um cofre de chaves

Você pode configurar as pastas de trabalho de várias assinaturas e da visão geral do cofre de chaves ou falhas para definir o escopo para uma ou mais assinaturas ou cofres de chaves em cada execução, executando as seguintes etapas:

1. Selecione **Monitor** no portal e, em seguida, selecione **cofres de chaves (visualização)** no painel esquerdo.
2. Na pasta de trabalho **visão geral** , na barra de comandos, selecione **Editar**.
3. Selecione na lista suspensa **assinaturas** uma ou mais assinaturas que você deseja que a Yo use como o padrão. Lembre-se de que a pasta de trabalho dá suporte à seleção de até um total de 10 assinaturas.
4. Selecione na lista suspensa **cofres de chaves** uma ou mais contas que você deseja que ela use como o padrão. Lembre-se de que a pasta de trabalho dá suporte à seleção de até um total de 200 contas de armazenamento.
5. Selecione **salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com suas personalizações e clique em **edição concluída** para retornar ao modo de leitura.

## <a name="troubleshooting"></a>Solução de problemas

Esta seção o ajudará com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar Azure Monitor para Key Vault (versão prévia). Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolvendo problemas de desempenho ou falhas

Para ajudar a solucionar problemas relacionados ao cofre de chaves que você identifica com Azure Monitor para Key Vault (versão prévia), consulte a [documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="why-can-i-only-see-200-key-vaults"></a>Por que só posso ver os cofres de chaves 200?

Há um limite de 200 cofres de chaves que podem ser selecionados e exibidos. Independentemente do número de assinaturas selecionadas, o número de cofres de chaves selecionados tem um limite de 200.

### <a name="what-will-happen-when-a-pinned-item-is-clicked"></a>O que acontecerá quando um item fixado for clicado?

Quando um item fixado no painel for clicado, ele abrirá uma das duas opções a seguir:
* Se as informações foram salvas, ele abrirá a instância do insights da qual o PIN foi salvo.
* Se as informações não foram salvas, ele abrirá uma nova instância de insights padrão.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não vejo todas as minhas assinaturas no seletor de assinatura?

Mostramos apenas assinaturas que contêm cofres de chaves, escolhidas no filtro de assinatura selecionado, que são selecionadas na "diretório + assinatura" no cabeçalho portal do Azure.

![Captura de tela do filtro de assinatura](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Estou recebendo uma mensagem de erro informando que a "consulta excede o número máximo de espaços de trabalho/regiões permitidos", o que fazer agora?

Atualmente, há um limite para 25 regiões e 200 espaços de trabalho, para exibir seus dados, será necessário reduzir o número de assinaturas e/ou grupos de recursos.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais a Key Vault insights, como fazer isso?

Para fazer alterações, selecione o "modo de edição" para modificar a pasta de trabalho e, em seguida, você pode salvar seu trabalho como uma nova pasta de trabalho vinculada a uma assinatura e um grupo de recursos designados.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o refinamento de tempo depois de fixarmos qualquer parte das pastas de trabalho?

Utilizamos o intervalo de tempo "auto" e, portanto, depende do período selecionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte da pasta de trabalho é fixada?

O intervalo de tempo dependerá das configurações do painel.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Por que não vejo nenhum dado para o meu Key Vault nas seções de latência de & de operações?

Para exibir os dados baseados em logs, você precisará habilitar os logs para cada um dos cofres de chaves que deseja monitorar. Isso pode ser feito nas configurações de diagnóstico para cada cofre de chaves. Você precisará enviar seus dados para um espaço de trabalho Log Analytics designado.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Já habilitei os logs para o meu Key Vault, por que ainda não consigo ver meus dados sob a latência de & de operações?

Atualmente, os logs de diagnóstico não funcionam retroativamente, portanto, os dados só começarão a aparecer uma vez que houve ações para seus cofres de chaves. Portanto, pode levar algum tempo, variando de horas a um dia, dependendo de como o cofre de chaves está ativo.

Além disso, se você tiver um grande número de cofres de chaves e assinaturas selecionados, talvez não seja possível exibir seus dados devido a limitações de consulta. Para exibir seus dados, talvez seja necessário reduzir o número de assinaturas ou cofres de chaves selecionados. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>E se eu quiser ver outros dados ou fazer minhas próprias visualizações? Como fazer alterações no Key Vault insights?

Você pode editar a pasta de trabalho existente com o uso do modo de edição e, em seguida, salvar seu trabalho como uma nova pasta de trabalho que terá todas as suas novas alterações.

## <a name="next-steps"></a>Próximas etapas

Conheça os cenários em que as pastas de trabalho foram projetadas para dar suporte, como criar novos e personalizar relatórios existentes e muito mais examinando [criar relatórios interativos com pastas de trabalho do Azure monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
