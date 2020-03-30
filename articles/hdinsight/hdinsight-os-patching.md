---
title: Configure o cronograma de patches do SISTEMA OPERACIONAL para clusters Azure HDInsight
description: Saiba como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206853"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configure o cronograma de patches do Sistema Operacional para clusters HDInsight baseados em Linux

> [!IMPORTANT]
> As imagens do Ubuntu ficam disponíveis para a criação do novo cluster Azure HDInsight dentro de três meses após a publicação. A partir de janeiro de 2019, os clusters em execução não são corrigidos automaticamente. Os clientes devem usar ações de script ou outros mecanismos para aplicar o patch de um cluster em execução. Clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

O HDInsight oferece suporte para que você execute tarefas comuns em seu cluster, como instalar patches do SO, atualizações de segurança e reinicializar nomes. Essas tarefas são realizadas usando os dois scripts a seguir que podem ser executados como [ações de script](hdinsight-hadoop-customize-cluster-linux.md)e configurados com parâmetros:

- `schedule-reboots.sh`- Faça uma reinicialização imediata ou agende uma reinicialização nos nós de cluster.
- `install-updates-schedule-reboots.sh`- Instale todas as atualizações, apenas atualizações de kernel + segurança, ou apenas atualizações do kernel.

> [!NOTE]  
> As ações de script não aplicarão automaticamente atualizações para todos os ciclos de atualização futuros. Execute os scripts sempre que novas atualizações forem aplicadas para instalar as atualizações e, em seguida, reiniciar a VM.

## <a name="preparation"></a>Preparação

Patch em um ambiente representativo de não-produção antes de implantar na produção. Desenvolva um plano para testar adequadamente seu sistema antes de sua correção real.

De tempos em tempos, a partir de uma sessão ssh com seu cluster, você pode receber uma mensagem de que um upgrade está disponível. A mensagem pode parecer algo como:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Patching é opcional e a seu critério.

## <a name="restart-nodes"></a>Nó de reinicialização
  
O [script schedule-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), define o tipo de reinicialização que será realizada nas máquinas no cluster. Ao enviar a ação do script, defina-a para aplicar em todos os três tipos de nó: nó de cabeça, nó do trabalhador e zookeeper. Se o script não for aplicado a um tipo de nó, as VMs para esse tipo de nó não serão atualizadas ou reiniciadas.

O `schedule-reboots script` aceita um parâmetro numérico:

| Parâmetro | Valores aceitos | Definição |
| --- | --- | --- |
| Tipo de reinicialização para executar | 1 ou 2 | Um valor de 1 permite a reinicialização do cronograma (programado em 12-24 horas). Um valor de 2 permite a reinicialização imediata (em 5 minutos). Se nenhum parâmetro for dado, o padrão é 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instale atualizações e reinicie os nós

O [script install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) oferece opções para instalar diferentes tipos de atualizações e reiniciar a VM.

O `install-updates-schedule-reboots` script aceita dois parâmetros numéricos, conforme descrito na tabela a seguir:

| Parâmetro | Valores aceitos | Definição |
| --- | --- | --- |
| Tipo de atualizações para instalar | 0, 1 ou 2 | Um valor de 0 instala apenas atualizações do kernel. Um valor de 1 instala todas as atualizações, e 2 instala apenas kernel + atualizações de segurança. Se nenhum parâmetro for fornecido, o padrão será 0. |
| Tipo de reinicialização para executar | 0, 1 ou 2 | Um valor de 0 desativa a reinicialização. Um valor de 1 permite a reinicialização do cronograma, e 2 permite a reinicialização imediata. Se nenhum parâmetro for fornecido, o padrão será 0. O usuário deve alterar o parâmetro de entrada 1 para o parâmetro de entrada 2. |

> [!NOTE]
> Você deve marcar um script como persistido depois de aplicá-lo a um cluster existente. Caso contrário, quaisquer nós novos criado por meio de operações de dimensionamento usarão o agendamento de aplicação de patch padrão. Se você aplicar o script como parte do processo de criação de cluster, ele persistirá automaticamente.

## <a name="next-steps"></a>Próximas etapas

Para obter etapas específicas sobre o uso de ações de script, consulte as seguintes seções em [clusters HDInsight baseados em Linux personalizados usando ação de script](hdinsight-hadoop-customize-cluster-linux.md):

- [Usar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Aplique uma ação de script a um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
