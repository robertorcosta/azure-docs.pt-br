---
title: Gerenciar e atualizar o cache HPC do Azure
description: Como gerenciar e atualizar o cache HPC do Azure usando o portal do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 825b8a34e130286a5772363107311fe4170e8743
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515566"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerenciar seu cache do portal do Azure

A página Visão geral do cache na portal do Azure mostra detalhes do projeto, status do cache e estatísticas básicas para seu cache. Ele também tem controles para parar ou iniciar o cache, excluir o cache, liberar dados para o armazenamento de longo prazo e atualizar o software.

Para abrir a página Visão geral, selecione o recurso de cache na portal do Azure. Por exemplo, carregue a página **todos os recursos** e clique no nome do cache.

![captura de tela de uma página de visão geral da instância do cache HPC do Azure](media/hpc-cache-overview.png)

Os botões na parte superior da página podem ajudá-lo a gerenciar o cache:

* **Iniciar** e [**parar**](#stop-the-cache) – suspende a operação de cache
* [**Flush**](#flush-cached-data) -grava dados alterados para destinos de armazenamento
* [**Atualização**](#upgrade-cache-software) – atualiza o software de cache
* **Atualizar** – recarrega a página Visão geral
* [**Excluir**](#delete-the-cache) – destrói permanentemente o cache

Leia mais sobre essas opções abaixo.

Clique na imagem abaixo para assistir a um [vídeo](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) que demonstra as tarefas de gerenciamento de cache.

[![miniatura de vídeo: cache do HPC do Azure: gerenciar (clique para visitar a página de vídeo)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Parar o cache

Você pode interromper o cache para reduzir os custos durante um período inativo. Você não é cobrado pelo tempo de atividade enquanto o cache é interrompido, mas você é cobrado pelo armazenamento em disco alocado do cache. (Consulte a página de [preços](https://aka.ms/hpc-cache-pricing) para obter detalhes.)

Um cache interrompido não responde às solicitações do cliente. Você deve desmontar os clientes antes de parar o cache.

O botão **parar** suspende um cache ativo. O botão **parar** está disponível quando o status de um cache está **íntegro** ou **degradado**.

![captura de tela dos botões superior com parar realçado e uma mensagem pop-up que descreve a ação de parada e perguntando "deseja continuar?" com Sim (padrão) e nenhum botão](media/stop-cache.png)

Depois de clicar em Sim para confirmar a interrupção do cache, o cache libera automaticamente seu conteúdo para os destinos de armazenamento. Esse processo pode levar algum tempo, mas garante a consistência dos dados. Por fim, o status do cache é alterado para **parado**.

Para reativar um cache interrompido, clique no botão **Iniciar** . Nenhuma confirmação é necessária.

![captura de tela dos botões superior com início realçado](media/start-cache.png)

## <a name="flush-cached-data"></a>Liberar dados armazenados em cache

O botão **liberar** na página Visão geral informa o cache para gravar imediatamente todos os dados alterados armazenados no cache para os destinos de armazenamento de back-end. O cache salva rotineiramente os dados nos destinos de armazenamento, portanto, não é necessário fazer isso manualmente, a menos que você queira verificar se o sistema de armazenamento de back-end está atualizado. Por exemplo, você pode usar **flush** antes de tirar um instantâneo de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de liberação, o cache não pode atender às solicitações do cliente. O acesso ao cache é suspenso e continua após a conclusão da operação.

![captura de tela dos botões superior com liberação realçada e uma mensagem pop-up descrevendo a ação de liberação e perguntando "deseja continuar?" com Sim (padrão) e nenhum botão](media/hpc-cache-flush.png)

Quando você inicia a operação de liberação de cache, o cache para de aceitar solicitações de cliente e o status do cache na página de visão geral muda para a **liberação**.

Os dados no cache são salvos nos destinos de armazenamento apropriados. Dependendo da quantidade de dados que precisa ser liberada, o processo pode levar alguns minutos ou mais de uma hora.

Depois que todos os dados são salvos em destinos de armazenamento, o cache começa automaticamente a executar solicitações de cliente novamente. O status do cache retorna para **íntegro**.

## <a name="upgrade-cache-software"></a>Atualizar o software de cache

Se uma nova versão de software estiver disponível, o botão **Atualizar** se tornará ativo. Você também deverá ver uma mensagem na parte superior da página sobre como atualizar o software.

![captura de tela da linha superior de botões com o botão de atualização habilitado](media/hpc-cache-upgrade-button.png)

O acesso do cliente não é interrompido durante uma atualização de software, mas o desempenho do cache fica mais lento. Planeje a atualização de software durante horas de uso fora do pico ou em um período de manutenção planejada.

A atualização de software pode levar várias horas. Os caches configurados com maior taxa de transferência levam mais tempo para atualizar que os caches com valores menores de taxa de transferência de pico.

Quando uma atualização de software estiver disponível, você terá uma semana ou mais para aplicá-la manualmente. A data de término é listada na mensagem de atualização. Se você não atualizar durante esse tempo, o Azure aplicará automaticamente a atualização ao seu cache. O tempo de atualização automática não é configurável. Se você estiver preocupado com o impacto no desempenho do cache, atualize o software por conta própria antes que o período de tempo expire.

Se o cache for interrompido quando a data de término passar, o cache atualizará automaticamente o software na próxima vez que for iniciado. (A atualização pode não iniciar imediatamente, mas será iniciada na primeira hora.)

Clique no botão **Atualizar** para iniciar a atualização de software. O status do cache muda para **atualizando** até que a operação seja concluída.

## <a name="delete-the-cache"></a>Excluir o cache

O botão **excluir** destrói o cache. Quando você exclui um cache, todos os seus recursos são destruídos e não incorrem mais em encargos de conta.

Os volumes de armazenamento de back-end usados como destinos de armazenamento não são afetados quando você exclui o cache. Você pode adicioná-los a um cache futuro posteriormente ou descomissiona-los separadamente.

> [!NOTE]
> O cache HPC do Azure não grava automaticamente os dados alterados do cache nos sistemas de armazenamento de back-end antes de excluir o cache.
>
> Para garantir que todos os dados no cache tenham sido gravados no armazenamento de longo prazo, [interrompa o cache antes de](#stop-the-cache) excluí-lo. Verifique se ele mostra o status **parado** antes de clicar no botão excluir.

## <a name="cache-metrics-and-monitoring"></a>Métricas e monitoramento de cache

A página Visão geral mostra grafos para algumas estatísticas básicas de cache-taxa de transferência de cache, operações por segundo e latência.

![captura de tela de três gráficos de linha mostrando as estatísticas mencionadas acima para um cache de exemplo](media/hpc-cache-overview-stats.png)

Esses gráficos fazem parte das ferramentas internas de monitoramento e análise do Azure. Ferramentas e alertas adicionais estão disponíveis nas páginas no cabeçalho **monitoramento** na barra lateral do Portal. Saiba mais na seção portal da documentação de [monitoramento do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [ferramentas de métricas e estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o cache do HPC do Azure](hpc-cache-support-ticket.md)
