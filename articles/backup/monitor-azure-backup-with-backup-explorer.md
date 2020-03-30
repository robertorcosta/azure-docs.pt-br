---
title: Monitore seus backups com o Backup Explorer
description: Este artigo descreve como usar o Backup Explorer para executar o monitoramento em tempo real de backups em cofres, assinaturas, regiões e inquilinos.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131804"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitore seus backups com o Backup Explorer

À medida que as organizações fazem backup cada vez mais de máquinas na nuvem, torna-se cada vez mais importante monitorar esses backups de forma eficiente. A melhor maneira de começar é usar um local central para visualizar informações operacionais em uma grande propriedade.

O Backup Explorer é uma pasta de trabalho incorporada do Azure Monitor que oferece aos clientes do Azure Backup este único local central. O Backup Explorer ajuda você a monitorar as atividades operacionais em toda a propriedade de backup no Azure, abrangendo inquilinos, locais, assinaturas, grupos de recursos e cofres. Em geral, o Backup Explorer fornece os seguintes recursos:

* **Perspectiva em escala :** Obtenha uma visão agregada dos itens de backup, empregos, alertas, políticas e recursos que ainda não estão configurados para backup em toda a propriedade. 
* **Análise de detalhamento**: Exiba informações detalhadas sobre cada um de seus trabalhos, alertas, políticas e itens de backup, tudo em um só lugar.
* **Interfaces acionáveis**: Depois de identificar um problema, você pode resolvê-lo indo perfeitamente para o item de backup relevante ou recurso Do Zure.

Esses recursos são fornecidos fora da caixa por integração nativa com o Gráfico de Recursos do Azure e as regras de trabalho do Monitor Azure.

> [!NOTE]
> * O Backup Explorer está disponível atualmente apenas para dados de máquinas virtuais (VMs) do Azure.
> * O Backup Explorer deve ser um painel operacional para visualizar informações sobre seus backups nos últimos 7 dias (máximo).
> * O Backup Explorer atualmente não é suportado em nuvens nacionais.
> * Atualmente, a personalização do modelo do Backup Explorer não é suportada. 
> * Não recomendamos escrever automações personalizadas nos dados do Gráfico de Recursos do Azure.

## <a name="get-started"></a>Introdução

Você pode acessar o Backup Explorer indo para qualquer um dos cofres dos Serviços de Recuperação e selecionando o link **do Explorador de Backup** no painel Visão **geral.**

![Link rápido do cofre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A seleção do link abre o Backup Explorer, que fornece uma visão agregada em todos os cofres e assinaturas aos quais você tem acesso. Se você estiver usando uma conta do Azure Lighthouse, você pode visualizar dados em todos os inquilinos aos seus inquilinos. Para obter mais informações, consulte a seção "Visualizações cruzadas" no final deste artigo.

![Página de aterrissagem do Backup Explorer](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casos de uso do Backup Explorer

O Backup Explorer exibe várias guias, cada uma fornecendo informações detalhadas sobre um artefato de backup específico (por exemplo, um item de backup, trabalho ou política). Esta seção fornece uma breve visão geral de cada uma das guias. Os vídeos fornecem casos de uso de amostras para cada artefato de backup, juntamente com descrições dos controles disponíveis.

### <a name="the-summary-tab"></a>A guia Resumo

A guia **Resumo** fornece uma rápida olhada na condição geral de sua propriedade de backup. Por exemplo, você pode visualizar o número de itens que estão sendo protegidos, o número de itens para os quais a proteção não foi ativada ou quantos empregos foram bem sucedidos nas últimas 24 horas.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>A guia Itens de backup

Você pode filtrar e visualizar cada um de seus itens de backup por assinatura, cofre e outras características. Ao selecionar o nome de um item de backup, você pode abrir o painel Azure para esse item. Por exemplo, a partir da tabela, você pode observar que o último backup falhou no item *X*. Ao selecionar *X,* você pode abrir o painel **de backup** do item, onde você pode desencadear uma operação de backup demanda.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>A guia Jobs

Selecione a guia **Empregos** para ver os detalhes de todos os trabalhos que foram acionados nos últimos 7 dias. Aqui, você pode filtrar por *Operação de Trabalho,* *Status do Trabalho*e Código de *Erro* (para trabalhos com falha).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>A guia Alertas

Selecione a guia **Alertas** para visualizar detalhes de todos os alertas gerados em seus cofres nos últimos 7 dias. Você pode filtrar alertas por tipo *(Falha de backup* ou *falha de restauração),* status atual *(Ativo* ou *Resolvido)* e gravidade *(Crítica,* *Aviso*ou *Informação).* Você também pode selecionar um link para ir à VM do Azure e tomar todas as medidas necessárias.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>A guia Políticas

Você pode selecionar a guia **Políticas** para exibir informações-chave sobre todas as políticas de backup criadas em sua propriedade de backup. Você pode visualizar o número de itens associados a cada diretiva, juntamente com o intervalo de retenção e a freqüência de backup especificada pela diretiva.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>A guia Backup não habilitado

O backup deve ser ativado para todas as máquinas que requerem proteção. Com o Backup Explorer, os administradores de backup podem identificar rapidamente quais máquinas em uma organização ainda não estão protegidas por backup. Para exibir essas informações, selecione a guia **Backup não ativado.**

O **painel Backup Não Ativado** exibe uma tabela com uma lista de máquinas desprotegidas. Sua organização pode atribuir diferentes tags a máquinas de produção e máquinas de teste, ou a máquinas que servem a uma variedade de funções. Como cada classe de máquinas precisa de uma política de backup separada, a filtragem por tags ajuda você a visualizar informações específicas para cada uma. Selecionar o nome de qualquer máquina redireciona você para o painel **Configurar backup** dessa máquina, onde você pode optar por aplicar uma política de backup apropriada.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportar para o Excel

Você pode exportar o conteúdo de qualquer tabela ou gráfico como uma planilha do Excel. O conteúdo é exportado como está, com seus filtros existentes aplicados. Para exportar linhas de tabela adicionais, você pode aumentar o número de linhas a serem exibidas na página usando a lista suspensa **'Linhas por página'** na parte superior de cada guia.

## <a name="pin-to-the-dashboard"></a>Pino no painel

Você pode selecionar o ícone "pin" no topo de cada tabela ou gráfico para fixá-lo no painel do portal Azure. Fixar essas informações ajuda a criar um painel personalizado que é adaptado para exibir as informações mais importantes para você.

## <a name="cross-tenant-views"></a>Vistas entre inquilinos

Se você é um usuário do Azure Lighthouse com acesso delegado a assinaturas em vários ambientes de inquilinos, você pode usar o filtro de assinatura padrão. Você exibe as assinaturas para as quais deseja ver os dados selecionando o ícone "filtro" no canto superior direito do portal Azure. Quando você usa esse recurso, o Backup Explorer agrega informações sobre todos os cofres em suas assinaturas selecionadas. Para saber mais, veja [O que é o Farol azure?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar o Azure Monitor para obter informações sobre seus dados de backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
