---
title: Monitorar Key Vault com Azure Monitor para Key Vault (versão prévia) | Microsoft Docs
description: Este artigo descreve o Azure Monitor para Key Vaults.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: c669946ab76fcaeaaa6fd681f521408643c5a63c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531252"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitoramento do serviço do Key Vault com Azure Monitor para Key Vault (versão prévia)
O Azure Monitor para Key Vault (versão prévia) fornece monitoramento abrangente de key vaults, fornecendo uma exibição unificada das solicitações, desempenho, falhas e latência do Key Vault.
Este artigo ajudará a entender como integrar e personalizar a experiência do Azure Monitor para Key Vault (versão prévia).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Introdução ao Azure Monitor para Key Vault (versão prévia)

Antes de saltar para a experiência, você deve entender como ela apresenta e visualiza informações.
-    **Na perspectiva de escala**, mostrando uma exibição de instantâneo do desempenho com base nas solicitações, no detalhamento de falhas e em uma visão geral das operações e da latência.
-   **Análise de busca detalhada** de um key vault específico para realizar uma análise detalhada.
-    **Personalizável**, onde você pode alterar quais métricas deseja ver, modificar ou definir limites que se alinham aos limites e salvar sua própria pasta de trabalho. Os gráficos na pasta de trabalho podem ser fixados a painéis do Azure.

O Azure Monitor para Key Vault combina logs e métricas para fornecer uma solução de monitoramento global. Todos os usuários podem acessar os dados de monitoramento baseados em métricas, no entanto, a inclusão de visualizações baseadas em logs pode exigir que os usuários [habilitem o registro do Azure Key Vault](../../key-vault/general/logging.md).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Configuração dos key vaults para monitoramento

> [!NOTE]
> A habilitação de logs é um serviço pago que fornece recursos de monitoramento adicionais.

1. A guia Operações e latência ajuda a determinar quantos e quais key vaults estão habilitados. Para começar a coletar, selecione o botão **Habilitar**, que abre uma pasta de trabalho separada que lista os key vaults que exigem a habilitação dos logs de diagnóstico.

    ![Captura de tela da guia Operações e latência com o botão azul Habilitar exibido](./media/key-vaults-insights-overview/enable-logging.png)

2. Para habilitar os logs de diagnóstico, clique no link **Habilitar** abaixo da coluna Ações e crie uma nova configuração de diagnóstico que envia logs para um workspace do Log Analytics. É recomendável enviar todos os logs para o mesmo espaço de trabalho.

3. Depois que as configurações de diagnóstico forem salvas, você pode exibir todos os gráficos baseados em log e as visualizações abaixo do Key Vault Insights. Observe que pode levar de vários minutos a horas para começar o preenchimento dos logs.

4. Para obter assistência adicional sobre como habilitar os logs de diagnóstico para o serviço de Key Vault, leia o [guia completo](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Exibir no Azure Monitor

No Azure Monitor, é possível exibir os detalhes de solicitação, latência e falha de vários key vaults da sua assinatura e ajudar a identificar problemas de desempenho e cenários de limitação.

Para exibir a utilização e as operações de seus cofres de chaves em todas as suas assinaturas, execute as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Selecione **Monitor** no painel esquerdo do portal do Azure e, na seção Insights, selecione **Key Vaults (versão prévia)** .

![Captura de tela da experiência de visão geral com vários grafos](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Pasta de trabalho Visão geral

Na pasta de trabalho Visão geral da assinatura selecionada, a tabela exibe as métricas interativas dos key vaults agrupados na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* Assinaturas - somente as assinaturas que têm key vaults são listadas.

* Key Vaults - por padrão, somente até 5 key vaults são selecionados previamente. Se você selecionar todos ou vários key vaults no seletor de escopo, até 200 key vaults serão retornados. Por exemplo, se você tiver um total de 573 key vaults nas três assinaturas selecionadas, somente 200 vaults serão exibidos.

* Intervalo de tempo - por padrão, exibe as últimas 24 horas de informações com base nas seleções correspondentes.

O bloco contador, nas listas suspensas, acumula o número total de key vaults nas assinaturas selecionadas e reflete quantos foram selecionados. Existem mapas de calor condicionais com código de cores para as colunas da pasta de trabalho que relatam as métricas de solicitação, falhas e latência. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

## <a name="failures-workbook"></a>Pasta de trabalho Falhas

Selecione **Falhas** na parte superior da página e a guia Falhas será aberta. Ela mostra as ocorrências da API, a frequência ao longo do tempo, juntamente com a quantidade de determinados códigos de resposta.

![Captura de tela da pasta de trabalho Falhas](./media/key-vaults-insights-overview/failures.png)

Há codificação de cor condicional ou mapas de calor para colunas na pasta de trabalho que relatam as métricas de ocorrências da API com um valor azul. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

A pasta de trabalho exibe Sucessos (códigos de status 2xx), Erros de autenticação (códigos de status 401/403), Limitação (códigos de status 429) e Outras falhas (códigos de status 4xx).

Para entender melhor o que cada um dos códigos de status representa, recomendamos ler a documentação em [Status e códigos de resposta do Azure Key Vault](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="operations--latency-workbook"></a>Pasta de trabalho Operações e latência

Selecione **Operações e latência** na parte superior da página e a guia **Operações e latência** será aberta. Esta guia permite integrar os key vaults para monitoramento. Para obter etapas mais detalhadas, confira a seção [Configuração dos key vaults para monitoramento](#configuring-your-key-vaults-for-monitoring).

Você pode ver quantos dos key vaults estão habilitados para o registro. Se pelo menos um vault foi configurado corretamente, você pode ver as tabelas que exibem as operações e os códigos de status de cada um dos key vaults. Você pode clicar na seção de detalhes de uma linha para obter informações adicionais sobre cada operação.

![Captura de tela dos gráficos de operações e latência](./media/key-vaults-insights-overview/logs.png)

Se você não estiver visualizando os dados desta seção, veja a seção superior sobre como habilitar logs para Azure Key Vault ou consulte a seção Solução de problemas abaixo.

## <a name="view-from-a-key-vault-resource"></a>Exibição de um recurso do Key Vault

Para acessar o Azure Monitor para Key Vault diretamente em um key vault:

1. Na portal do Azure, selecione Key Vaults.

2. Na lista, escolha um key vault. Na seção Monitoramento, escolha Insights (versão prévia).

Essas exibições também podem ser acessadas selecionando o nome do recurso de um key vault na pasta de trabalho do Azure Monitor.

![Captura de tela da exibição de um recurso do key vault](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Na pasta de trabalho **Visão geral** para o key vault, são mostradas várias métricas de desempenho que ajudam a avaliar rapidamente o seguinte:

- Gráficos de desempenho interativos que mostram os detalhes mais fundamentais relacionados a transações, latência e disponibilidade do key vault.

- Os blocos de métricas e status que realçam a disponibilidade do serviço, a contagem total de transações para o recurso do key vault e a latência geral.

Selecionar qualquer uma das outras guias para **Falhas** ou **Operações** abre as respectivas pastas de trabalho.

![Captura de tela da exibição de falhas](./media/key-vaults-insights-overview/resource-failures.png)

A pasta de trabalho Falhas detalha os resultados de todas as solicitações do key vault no intervalo de tempo selecionado e fornece a categorização sobre Sucessos (2xx), Erros de autenticação (401/403), Limitação (429) e outras falhas.

![Captura de tela da exibição de operações](./media/key-vaults-insights-overview/operations.png)

A pasta de trabalho Operações permite aprofundar-se nos detalhes completos de todas as transações, que podem ser filtradas pelo status do resultado usando os blocos de nível superior.

![Captura de tela da exibição de operações](./media/key-vaults-insights-overview/info.png)

Os usuários também podem explorar as exibições com base em tipos de transação específicos na tabela superior, que atualiza dinamicamente a tabela inferior, na qual os usuários podem exibir detalhes completos da operação em um painel de contexto pop-up.

>[!NOTE]
> Observe que os usuários devem ter as configurações de diagnóstico habilitadas para exibir esta pasta de trabalho. Para saber mais sobre como habilitar a configuração de diagnóstico, leia mais sobre [Registro do Azure Key Vault](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Fixar e exportar

Você pode fixar qualquer uma das seções de métrica em um Painel do Azure, selecionando o ícone de alfinete na parte superior direita da seção.

As pastas de trabalho Visão geral ou falhas de várias assinaturas e key vaults dão suporte à exportação dos resultados no formato do Excel, selecionando o ícone de download à esquerda do ícone de alfinete.

![Captura de tela do ícone de fixação selecionado](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalizar Azure Monitor para Key Vault

Esta seção destaca os cenários comuns para editar a pasta de trabalho a fim de personalizar o suporte às necessidades de análise de dados:
*  Escopo da pasta de trabalho para sempre selecionar uma assinatura ou key vault específico
* Alterar as métricas na grade
* Alterar o limite de solicitações
* Alterar a renderização de cores

Você pode iniciar as personalizações habilitando o modo de edição e selecionando o botão **Personalizar** na barra de ferramentas superior.

![Captura de tela do botão Personalizar](./media/key-vaults-insights-overview/customize.png)

As personalizações são salvas em uma pasta de trabalho personalizada para evitar a substituição da configuração padrão em nossa pasta de trabalho publicada. As pastas de trabalho são salvas em um grupo de recursos, seja na seção Meus Relatórios, que é particular a você, ou na seção Relatórios Compartilhados, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a galeria de pastas de trabalho para iniciá-la.

![Captura de tela da galeria da pasta de trabalho](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Especificação de uma assinatura ou um key vault

Você pode configurar as pastas de trabalho Visão geral ou Falhas de várias assinaturas e key vaults para definir o escopo para uma assinatura ou key vault específico em cada execução, seguindo as etapas abaixo:

1. Selecione **Monitor** no portal e, em seguida, selecione **Key Vaults (versão prévia)** no painel esquerdo.
2. Na pasta de trabalho **Visão geral**, na barra de comandos, selecione **Editar**.
3. Selecione na lista suspensa **Assinaturas** uma ou mais assinaturas que você deseja usar como o padrão. Lembre-se de que a pasta de trabalho permite selecionar até 10 assinaturas no total.
4. Selecione na lista suspensa **Key Vaults** uma ou mais contas que você deseja usar como padrão. Lembre-se de que a pasta de trabalho permite selecionar até 200 contas de armazenamento no total.
5. Selecione **Salvar como** na barra de comandos para salvar uma cópia da pasta de trabalho com as personalizações e, em seguida, clique em **Edição concluída** para retornar ao modo de leitura.

## <a name="troubleshooting"></a>Solução de problemas

Para obter diretrizes gerais de solução de problemas, consulte o [artigo de solução de problemas](troubleshoot-workbooks.md)de informações baseadas na pasta de trabalho dedicada.

Esta seção ajuda no diagnóstico e na solução de alguns dos problemas mais comuns que você pode encontrar ao usar o Azure Monitor para Key Vault (versão prévia). Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolução de problemas ou falhas de desempenho

Para ajudar a solucionar os problemas relacionados ao key vault identificados com o Azure Monitor para Key Vault (versão prévia), confira a documentação [Azure Key Vault](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Por que só posso ver os cofres de chaves 200

Há um limite de 200 key vaults que podem ser selecionados e exibidos. Independentemente do número de assinaturas selecionadas, o número de key vaults selecionados tem um limite de 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não vejo todas as minhas assinaturas no seletor de assinatura

Mostramos apenas as assinaturas que contêm key vaults, escolhidas no filtro de assinatura selecionado, que são selecionadas no "Diretório + Assinatura" no cabeçalho do portal do Azure.

![Captura de tela do filtro de assinatura](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Estou recebendo uma mensagem de erro informando que a "consulta excede o número máximo de espaços de trabalho/regiões permitidos", o que fazer agora

No momento, há um limite para 25 regiões e 200 espaços de trabalho para exibir os dados. Será necessário reduzir o número de assinaturas e/ou grupos de recursos.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais a Key Vault insights, como fazer isso

Para fazer alterações, selecione o "Modo de edição" para modificar a pasta de trabalho e, em seguida, você pode salvar seu trabalho como uma nova pasta de trabalho vinculada a uma assinatura e um grupo de recursos designados.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o refinamento de tempo depois de fixarmos qualquer parte das pastas de trabalho

Utilizamos o intervalo de agregação "Automático" e, portanto, depende do intervalo de tempo selecionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte da pasta de trabalho é fixada

O intervalo de tempo dependerá das configurações do painel.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Por que não vejo nenhum dado para meu Key Vault nas seções de latência de & de operações

Para exibir os dados baseados em logs, é necessário habilitar os logs de cada um dos key vaults que deseja monitorar. Isso pode ser feito nas configurações de diagnóstico para cada key vault. É necessário enviar os dados para um workspace do Log Analytics designado.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Já habilitei os logs para o meu Key Vault, por que ainda não consigo ver meus dados em operações & latência

No momento, os logs de diagnóstico não funcionam retroativamente, portanto, os dados só começarão a aparecer depois que ações forem realizados nos seus key vaults. Portanto, pode levar algum tempo, variando de horas a um dia, dependendo do volume de atividade do key vault.

Além disso, se você tiver um grande número de key vaults e assinaturas selecionados, talvez não seja possível exibir seus dados devido às limitações de consulta. Para exibir seus dados, talvez seja necessário reduzir o número de assinaturas ou key vaults selecionados. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>E se eu quiser ver outros dados ou fazer minhas próprias visualizações? Como fazer alterações no Key Vault insights

Você pode editar a pasta de trabalho existente usando o modo de edição e, em seguida, salvar seu trabalho como uma nova pasta de trabalho que terá todas as novas alterações.

## <a name="next-steps"></a>Próximas etapas

Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../platform/workbooks-overview.md).
