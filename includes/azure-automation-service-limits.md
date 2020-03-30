---
title: incluir arquivo
description: incluir arquivo
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334917"
---
#### <a name="process-automation"></a>Automação de processos

| Recurso | Limite |Observações|
| --- | --- |---|
| Número máximo de novos empregos que podem ser enviados a cada 30 segundos por conta do Azure Automation (empregos não programados) |100 |Quando esse limite é atingido, os pedidos subsequentes para criar um emprego falham. O cliente recebe uma resposta de erro.|
| Número máximo de trabalhos em execução simultâneos na mesma instância de tempo por conta de automação (empregos não programados) |200 |Quando esse limite é atingido, os pedidos subsequentes para criar um emprego falham. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados de trabalho para um período de rolagem de 30 dias | 10 GB (aproximadamente 4 milhões de empregos)|Quando esse limite é atingido, os pedidos subsequentes para criar um emprego falham. |
| Limite máximo de fluxo de trabalho|1 MB|Um único fluxo não pode ser maior que 1 MB.|
| Número máximo de módulos que podem ser importados a cada 30 segundos por conta de automação |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tempo de execução do trabalho, nível livre |500 minutos por assinatura por mês ||
| Quantidade máxima de espaço em disco permitido por caixa de areia<sup>1</sup> |1 GB |Aplica-se apenas a caixas de areia Do Zure.|
| Quantidade máxima de memória dada a uma caixa de areia<sup>1</sup> |400 MB |Aplica-se apenas a caixas de areia Do Zure.|
| Número máximo de soquetes de rede permitidos por caixa de areia<sup>1</sup> |1,000 |Aplica-se apenas a caixas de areia Do Zure.|
| Tempo de execução máximo permitido por runbook<sup>1</sup> |3 horas |Aplica-se apenas a caixas de areia Do Zure.|
| Número máximo de contas de Automação em uma assinatura |Sem limite ||
| Número máximo de grupos de trabalhadores híbridos por conta de automação|4.000||
|Número máximo de trabalhos simultâneos que podem ser executados em um único Trabalhador de Runbook Híbrido|50 ||
| Tamanho máximo do parâmetro do trabalho do runbook   | 512 quilobits||
| Parâmetros máximos do runbook   | 50|Se você atingir o limite de 50 parâmetros, você pode passar uma seqüência JSON ou XML para um parâmetro e analisá-lo com o runbook.|
| Tamanho máximo da carga de webhook |  512 quilobits|
| Máximo de dias em que os dados do trabalho são retidos|30 dias|
| Tamanho máximo do estado do fluxo de trabalho PowerShell |5 MB| Aplica-se aos runbooks do fluxo de trabalho PowerShell ao desfazer o fluxo de trabalho.|

<sup>1</sup> Uma caixa de areia é um ambiente compartilhado que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma caixa de areia estão vinculados às limitações de recursos da caixa de areia.

#### <a name="change-tracking-and-inventory"></a>Controle de Alterações e Inventário

A tabela a seguir mostra os limites do item rastreado por máquina para o rastreamento de alterações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Arquivo|500||
|Registro|250||
|Software do Windows|250|Não inclui atualizações de software.|
|Pacotes do Linux|1,250||
|Serviços|250||
|Daemon|250||

#### <a name="update-management"></a>Gerenciamento de atualizações

A tabela a seguir mostra os limites para o Gerenciamento de Atualizações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Número de máquinas por implantação de atualização|1000||