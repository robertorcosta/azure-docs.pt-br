---
title: Monitorar seus backups com o Gerenciador de backup
description: Um artigo que descreve como usar o Gerenciador de backup para executar o monitoramento em tempo real de backups em cofres, assinaturas, regiões e locatários
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992177"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorar seus backups com o Gerenciador de backup

À medida que as organizações fazem backup de cada vez mais máquinas na nuvem, é importante ter um local central para exibir informações operacionais sobre backups em um espaço grande, para monitorar backups de maneira eficiente.

A pasta de trabalho do Gerenciador de backup é uma pasta de trabalho Azure Monitor interna que permite que os clientes de backup tenham um único painel para executar atividades de monitoramento operacional relacionadas ao backup em todo o espaço de backup no Azure (abrangendo locatários, locais, assinaturas, grupos de recursos e cofres), tudo de um lugar central. Em geral, ele fornece os seguintes recursos:

* **Em perspectiva de escala** – uma exibição agregada dos itens de backup, trabalhos, alertas, políticas e recursos não configurados para backup em todo o espaço de backup. 
* **Análise de busca detalhada** – você pode optar por obter informações detalhadas de cada uma das entidades – os trabalhos, alertas, políticas e itens de backup, tudo de um único lugar.
* **Interfaces acionáveis** – depois de identificar um problema, você pode optar por executar ações navegando diretamente para o item de backup ou o recurso do Azure.

Os recursos acima são fornecidos prontos para uso por integração nativa com o grafo de recursos do Azure e pastas de trabalho do Azure Monitor.

> [!NOTE]
> * O Gerenciador de backup está disponível no momento apenas para dados da VM do Azure.
> * O Gerenciador de backup deve ser um painel operacional para exibir informações sobre seus backups nos últimos 7 dias (máximo).
> * Atualmente, não há suporte para a personalização do modelo do Gerenciador de backup. Além disso, atualmente não recomendamos a gravação de automaçãos personalizadas em dados do grafo de recursos do Azure.

## <a name="getting-started"></a>Introdução

Você pode acessar o Gerenciador de backup navegando até qualquer um dos seus cofres de serviços de recuperação e clicando no link do **Gerenciador de backup** na página **visão geral** .

![Link rápido do cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Clicar no link abre o Gerenciador de backup, que fornece uma exibição agregada em todos os cofres e assinaturas aos quais você tem acesso. Se você estiver usando uma conta do Azure Lighthouse, poderá exibir dados em todos os locatários aos quais você tem acesso (veja mais na seção abaixo em exibições de locatário cruzado).

![Página de aterrissagem do Gerenciador](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casos de uso do Gerenciador de backup

O Gerenciador de backup é composto por várias guias, cada guia que fornece informações detalhadas sobre um tipo específico de artefato de backup, por exemplo, itens de backup, trabalhos, políticas, etc. Esta seção fornece uma breve visão geral de cada uma das guias. Os vídeos fornecem exemplos de casos de uso para cada uma das guias, juntamente com uma descrição dos vários controles disponíveis para o usuário.

### <a name="summary"></a>Resumo

A guia Resumo permite que você tenha uma visão rápida da condição geral do seu espaço de backup. Você pode exibir informações como o número de itens que estão sendo protegidos, o número de itens para os quais a proteção não foi habilitada, quantos trabalhos foram bem-sucedidos nas últimas 24 horas e assim por diante. 

Cada uma das outras guias representa uma entidade distinta, por exemplo: itens de backup, trabalhos de backup, alertas de backup e políticas de backup. Você também pode exibir informações sobre computadores para os quais o backup não foi configurado. Clicar em qualquer uma dessas guias mostrará informações específicas para essa entidade.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Itens de Backup

Você pode exibir cada um de seus itens de backup filtrados por assinatura, cofre e outros parâmetros. Clicar no nome de um item de backup permite abrir a folha do Azure para esse item de backup. Por exemplo, na tabela, você pode observar que o último backup falhou para o item ' X '. Clicar em ' X ' abre a folha de backup desse item, em que é possível disparar uma operação de backup sob demanda.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Trabalhos

Você pode exibir os detalhes de todos os trabalhos que foram disparados nos últimos sete dias, navegando até a guia trabalhos. Aqui, você pode filtrar por operação de trabalho, status do trabalho e código de erro (no caso de trabalhos com falha).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Alertas

Você pode exibir detalhes de todos os alertas que foram acionados em seus cofres nos últimos sete dias, clicando na guia alertas. Aqui, você pode filtrar os registros pelo tipo do alerta (por exemplo, falha de backup, falha de restauração), o status atual do alerta (por exemplo, ativo ou resolvido) e a severidade do alerta (por exemplo, crítico, aviso, informações). Você também pode navegar até a VM do Azure clicando no link para a VM e executar a ação necessária.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Políticas

Clicar na guia políticas permite que você exiba informações de chave em todas as políticas de backup que foram criadas em todo o seu espaço de backup. Você pode ver quantos itens estão associados a cada política, juntamente com o período de retenção e a frequência de backup especificados por cada política.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Backup não habilitado

É importante que o administrador de backup de uma organização identifique rapidamente quais computadores da organização ainda não têm o backup habilitado, para que o backup possa ser habilitado para todos os computadores que precisam de proteção. Clicar na guia **backup não habilitado** ajuda você nesta tarefa.

Nessa guia, você verá uma tabela que contém a lista de itens que não estão protegidos. Se a sua organização seguir a prática de atribuir marcas diferentes a computadores de produção e computadores de teste, ou computadores que atendem a funções diferentes, cada um deles pode precisar de uma política de backup separada, filtrar por marcas ajuda a exibir informações específicas para um determinada classe de computadores. Clicar no nome de qualquer item redireciona você para a folha **Configurar backup** para esse item, em que você pode optar por fazer backup desse item com uma política de backup apropriada.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Exportando para o Excel

Clicar no botão de seta para baixo na parte superior direita de qualquer widget (tabela/gráfico) exporta o conteúdo desse widget como uma planilha do Excel, como está com filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, você pode aumentar o número de linhas exibidas na página usando a lista suspensa **linhas por página** na parte superior de cada guia.

## <a name="pinning-to-dashboard"></a>Fixação no painel

Você pode clicar no ícone de pino na parte superior de cada widget para fixar o widget ao seu painel de portal do Azure. Isso ajuda a criar painéis personalizados adaptados para exibir as informações mais importantes de que você precisa.

## <a name="cross-tenant-views"></a>Exibições entre locatários

Se você for um usuário Lighthouse do Azure com acesso delegado a assinaturas em vários ambientes de locatário, poderá usar o filtro de assinatura padrão (clicando no ícone de filtro no canto superior direito do portal do Azure) para selecionar todas as assinaturas que deseja consulte dados para. Fazer isso permitirá que o Gerenciador de backup agregue informações sobre todos os cofres entre essas assinaturas. Saiba mais sobre o Azure Lighthouse [aqui](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar Azure Monitor para obter informações sobre seus dados de backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)