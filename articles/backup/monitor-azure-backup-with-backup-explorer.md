---
title: Monitorar seus backups com o Gerenciador de backup
description: Este artigo descreve como usar o Gerenciador de backup para executar o monitoramento em tempo real de backups em cofres, assinaturas, regiões e locatários.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 87780124d531212a141520df65ff7408cc120e55
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160982"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorar seus backups com o Gerenciador de backup

À medida que as organizações fazem backup de cada vez mais máquinas para a nuvem, é mais importante monitorar esses backups com eficiência. A melhor maneira de começar é usar um local central para exibir informações operacionais em um espaço grande.

O Gerenciador de backup é uma pasta de trabalho Azure Monitor interna que fornece aos clientes de backup do Azure esse único local central. O Gerenciador de backup ajuda a monitorar atividades operacionais em todo o espaço de backup no Azure, abrangendo locatários, locais, assinaturas, grupos de recursos e cofres. Em grande escala, o Gerenciador de backup fornece os seguintes recursos:

* **Perspectiva em escala**: Obtenha uma exibição agregada dos itens de backup, trabalhos, alertas, políticas e recursos que ainda não foram configurados para backup em todo o espaço.
* **Análise de busca detalhada**: exibe informações detalhadas sobre cada um de seus trabalhos, alertas, políticas e itens de backup, tudo em um único lugar.
* **Interfaces acionáveis**: depois de identificar um problema, você pode resolvê-lo indo diretamente para o item de backup relevante ou o recurso do Azure.

Esses recursos são fornecidos de forma integrada pela integração nativa com o grafo de recursos do Azure e pastas de trabalho do Azure Monitor.

> [!NOTE]
>
> * O Gerenciador de backup está disponível no momento apenas para dados de VMs (máquinas virtuais) do Azure.
> * O Gerenciador de backup deve ser um painel operacional para exibir informações sobre seus backups nos últimos 7 dias (máximo).
> * No momento, não há suporte para o Gerenciador de backup em nuvens nacionais.
> * Atualmente, não há suporte para a personalização do modelo do Gerenciador de backup.
> * Não recomendamos a gravação de automaçãos personalizadas em dados do grafo de recursos do Azure.

## <a name="get-started"></a>Introdução

Você pode acessar o Gerenciador de backup indo para qualquer um dos seus cofres dos serviços de recuperação e selecionando o link do **Gerenciador de backup** no painel **visão geral** .

![Link rápido do cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A seleção do link abre o Gerenciador de backup, que fornece uma exibição agregada em todos os cofres e assinaturas aos quais você tem acesso. Se você estiver usando uma conta do Azure Lighthouse, poderá exibir dados em todos os locatários aos quais você tem acesso. Para obter mais informações, consulte a seção "exibições entre locatários" no final deste artigo.

![Página de aterrissagem do Gerenciador de backup](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casos de uso do Gerenciador de backup

O Gerenciador de backup exibe várias guias, cada uma fornecendo informações detalhadas sobre um artefato de backup específico (por exemplo, um item de backup, um trabalho ou uma política). Esta seção fornece uma breve visão geral de cada uma das guias. Os vídeos fornecem exemplos de casos de uso para cada artefato de backup, juntamente com descrições dos controles disponíveis.

### <a name="the-summary-tab"></a>A guia Resumo

A guia **Resumo** fornece uma visão rápida da condição geral do seu espaço de backup. Por exemplo, você pode exibir o número de itens que estão sendo protegidos, o número de itens para os quais a proteção não foi habilitada ou quantos trabalhos foram bem-sucedidos nas últimas 24 horas.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>A guia itens de backup

Você pode filtrar e exibir cada um de seus itens de backup por assinatura, cofre e outras características. Ao selecionar o nome de um item de backup, você pode abrir o painel do Azure para esse item. Por exemplo, na tabela, você pode observar que o último backup falhou para o item *X*. Ao selecionar *X*, você pode abrir o painel **backup** do item, no qual é possível disparar uma operação de backup sob demanda.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>A guia trabalhos

Selecione a guia **trabalhos** para exibir os detalhes de todos os trabalhos que foram disparados nos últimos 7 dias. Aqui, você pode filtrar por *operação de trabalho*, *status do trabalho*e *código de erro* (para trabalhos com falha).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>A guia alertas

Selecione a guia **alertas** para exibir detalhes de todos os alertas que foram gerados em seus cofres nos últimos 7 dias. Você pode filtrar alertas por tipo (falha de*backup* ou *falha de restauração*), status atual (*ativo* ou *resolvido*) e severidade (*crítico*, *aviso*ou *informações*). Você também pode selecionar um link para acessar a VM do Azure e executar qualquer ação necessária.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>A guia políticas

Você pode selecionar a guia **políticas** para exibir informações de chave sobre todas as políticas de backup que foram criadas em todo o seu espaço de backup. Você pode exibir o número de itens associados a cada política, juntamente com o período de retenção e a frequência de backup especificados pela política.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>A guia backup não habilitado

O backup deve ser habilitado para todos os computadores que exigem proteção. Com o Gerenciador de backup, os administradores de backup podem identificar rapidamente quais computadores de uma organização ainda não estão protegidos pelo backup. Para exibir essas informações, selecione a guia **backup não habilitado** .

O painel **backup não habilitado** exibe uma tabela com uma lista de computadores desprotegidos. Sua organização pode atribuir marcas diferentes a computadores de produção e computadores de teste ou a computadores que atendem a uma variedade de funções. Como cada classe de máquinas precisa de uma política de backup separada, filtrar por marcas ajuda a exibir informações específicas para cada uma delas. A seleção do nome de qualquer computador o redireciona para o painel **Configurar backup** desse computador, no qual você pode optar por aplicar uma política de backup apropriada.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportar para o Excel

Você pode exportar o conteúdo de qualquer tabela ou gráfico como uma planilha do Excel. O conteúdo é exportado como está, com os filtros existentes aplicados. Para exportar linhas de tabela adicionais, você pode aumentar o número de linhas a serem exibidas na página usando a lista suspensa **linhas por página** na parte superior de cada guia.

## <a name="pin-to-the-dashboard"></a>Fixar no painel

Você pode selecionar o ícone "fixar" na parte superior de cada tabela ou gráfico para fixá-lo ao seu painel de portal do Azure. Fixar essas informações ajuda a criar um painel personalizado que é personalizado para exibir as informações que são mais importantes para você.

## <a name="cross-tenant-views"></a>Exibições entre locatários

Se você for um usuário Lighthouse do Azure com acesso delegado a assinaturas em vários ambientes de locatário, você poderá usar o filtro de assinatura padrão. Você exibe as assinaturas para as quais deseja ver os dados selecionando o ícone "filtrar" no canto superior direito do portal do Azure. Quando você usa esse recurso, o Gerenciador de backup agrega informações sobre todos os cofres em suas assinaturas selecionadas. Para saber mais, confira [o que é o Azure Lighthouse?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar Azure Monitor para obter informações sobre seus dados de backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
