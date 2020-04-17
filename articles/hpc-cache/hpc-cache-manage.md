---
title: Gerenciar e atualizar o Cache Azure HPC
description: Como gerenciar e atualizar o Cache Azure HPC usando o portal Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 57d6a2024cd6fd979426ca5de5e261f110f6156f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537943"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerencie seu cache a partir do portal Azure

A página de visão geral do cache no portal Azure mostra detalhes do projeto, status do cache e estatísticas básicas para o cache. Ele também tem controles para parar ou iniciar o cache, excluir o cache, liberar dados para armazenamento a longo prazo e atualizar o software.

Para abrir a página de visão geral, selecione seu recurso de cache no portal Azure. Por exemplo, carregue a página **Todos os recursos** e clique no nome do cache.

![captura de tela da página visão geral de uma instância do Cache Azure HPC](media/hpc-cache-overview.png)

Os botões na parte superior da página podem ajudá-lo a gerenciar o cache:

* **Iniciar** e [**Parar**](#stop-the-cache) - Suspende a operação de cache
* [**Flush**](#flush-cached-data) - Grava dados alterados para alvos de armazenamento
* [**Upgrade**](#upgrade-cache-software) - Atualiza o software de cache
* **Atualização** - Recarrega a página de visão geral
* [**Excluir**](#delete-the-cache) - Destrói permanentemente o cache

Leia mais sobre essas opções abaixo.

## <a name="stop-the-cache"></a>Pare o cache

Você pode parar o cache para reduzir custos durante um período inativo. Você não é cobrado por tempo de atividade enquanto o cache é interrompido, mas você é cobrado pelo armazenamento de disco alocado do cache. (Veja a página [de preços](https://aka.ms/hpc-cache-pricing) para obter detalhes.)

Um cache parado não responde às solicitações do cliente. Você deve desmontar clientes antes de parar o cache.

O botão **Stop** suspende um cache ativo. O botão **Stop** está disponível quando o status de um cache é **saudável** ou **degradado**.

![captura de tela dos botões superiores com Stop destacado e uma mensagem pop-up descrevendo a ação stop e perguntando 'você quer continuar?' com sim (padrão) e sem botões](media/stop-cache.png)

Depois de clicar em Sim para confirmar a interrupção do cache, o cache libera automaticamente seu conteúdo para os alvos de armazenamento. Esse processo pode levar algum tempo, mas garante a consistência dos dados. Finalmente, o status do cache muda para **Parado**.

Para reativar um cache parado, clique no botão **Iniciar.** Nenhuma confirmação é necessária.

![captura de tela dos botões superiores com Iniciar destacado](media/start-cache.png)

## <a name="flush-cached-data"></a>Flush dados armazenados em cache

O botão **Flush** na página de visão geral informa ao cache para gravar imediatamente todos os dados alterados armazenados no cache para os alvos de armazenamento back-end. O cache salva rotineiramente dados para os alvos de armazenamento, portanto, não é necessário fazer isso manualmente, a menos que você queira ter certeza de que o sistema de armazenamento back-end está atualizado. Por exemplo, você pode usar **Flush** antes de tirar um instantâneo de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de flush, o cache não pode atender às solicitações do cliente. O acesso ao cache é suspenso e é retomado após o término da operação.

![captura de tela dos botões superiores com Flush destacado e uma mensagem pop-up descrevendo a ação flush e perguntando 'você quer continuar?' com sim (padrão) e sem botões](media/hpc-cache-flush.png)

Quando você inicia a operação de flush de cache, o cache pára de aceitar solicitações do cliente e o status do cache na página de visão geral é alterado para **Flushing**.

Os dados no cache são salvos nos alvos de armazenamento apropriados. Dependendo da quantidade de dados necessários para serem lavados, o processo pode levar alguns minutos ou mais de uma hora.

Depois que todos os dados são salvos em alvos de armazenamento, o cache começa automaticamente a receber solicitações do cliente novamente. O status do cache retorna ao **Healthy**.

## <a name="upgrade-cache-software"></a>Atualizar software de cache

Se uma nova versão de software estiver disponível, o botão **Atualizar** fica ativo. Você também deve ver uma mensagem no topo da página sobre a atualização do software.

![captura de tela da linha superior de botões com o botão Atualizar ativado](media/hpc-cache-upgrade-button.png)

O acesso ao cliente não é interrompido durante uma atualização de software, mas o desempenho do cache diminui. Planeje atualizar o software durante horas de uso não-pico ou em um período de manutenção planejado.

A atualização de software pode levar várias horas. Caches configurados com maior rendimento levam mais tempo para serem atualizados do que caches com valores de throughput de pico menores.

Quando uma atualização de software estiver disponível, você terá uma semana ou mais para aplicá-la manualmente. A data final está listada na mensagem de atualização. Se você não atualizar durante esse tempo, o Azure aplica automaticamente a atualização ao seu cache. O tempo da atualização automática não é configurável. Se você estiver preocupado com o impacto do desempenho do cache, você mesmo deve atualizar o software antes que o período de tempo expire.

Se o cache for interrompido quando a data final for aprovada, o cache atualizará automaticamente o software na próxima vez que for iniciado. (A atualização pode não começar imediatamente, mas começará na primeira hora.)

Clique no botão **Atualizar** para iniciar a atualização de software. O status do cache muda para **Atualização** até que a operação seja concluída.

## <a name="delete-the-cache"></a>Excluir o cache

O botão **Excluir** destrói o cache. Quando você exclui um cache, todos os seus recursos são destruídos e não incorrem mais em cobranças de conta.

Os volumes de armazenamento back-end usados como alvos de armazenamento não são afetados quando você exclui o cache. Você pode adicioná-los a um cache futuro mais tarde ou descomissioná-los separadamente.

> [!NOTE]
> O Cache Azure HPC não grava automaticamente dados alterados do cache para os sistemas de armazenamento back-end antes de excluir o cache.
>
> Para ter certeza de que todos os dados do cache foram gravados para armazenamento a longo prazo, [pare o cache](#stop-the-cache) antes de excluí-lo. Certifique-se de que ele mostra o status **Parou** antes de clicar no botão excluir.

## <a name="cache-metrics-and-monitoring"></a>Métricas de cache e monitoramento

A página de visão geral mostra gráficos para algumas estatísticas básicas de cache - throughput de cache, operações por segundo e latência.

![captura de tela de três gráficos de linha mostrando as estatísticas mencionadas acima para um cache amostral](media/hpc-cache-overview-stats.png)

Esses gráficos fazem parte das ferramentas de monitoramento e análise incorporadas do Azure. Ferramentas e alertas adicionais estão disponíveis nas páginas sob o título **Monitoramento** na barra lateral do portal. Saiba mais na seção portal da documentação do [Azure Monitoring](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [as métricas e ferramentas de estatística do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o cache Do Azure HPC](hpc-cache-support-ticket.md)
