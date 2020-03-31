---
title: Monitorar a atividade da Nuvem Privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve as informações disponíveis sobre a atividade no ambiente Azure VMware by CloudSimple, incluindo alertas, eventos, tarefas e auditoria.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019665"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitore a solução VMware por atividade CloudSimple

Os registros de atividades do CloudSimple fornecem uma visão das operações feitas no portal CloudSimple.  A lista inclui alertas, eventos, tarefas e auditoria.  Use os registros de atividadepara determinar quem, quando e quais operações foram realizadas.  Os registros de atividade não incluem nenhuma operação de leitura feita por um usuário.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesse o [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informações sobre atividades

Para acessar as páginas Atividade, **selecione Atividade** no menu lateral.

![Visão geral da página de atividades](media/activity-page-overview.png)

Para ver detalhes sobre qualquer uma das atividades na página de atividades, selecione a atividade. Um painel de detalhes abre à direita. As ações no painel dependem do tipo de atividade. Clique **em X** para fechar o painel.

Clique em um cabeçalho de coluna para classificar o visor.  Você pode filtrar colunas para valores específicos a serem visualizados.  Baixe o relatório de atividade clicando em Download como ícone **CSV.**

## <a name="alerts"></a>Alertas

Alertas são notificações de qualquer atividade significativa em seu ambiente CloudSimple.  Os alertas incluem eventos que afetam o faturamento ou o acesso do usuário.

Para reconhecer os alertas e removê-los da lista, selecione um ou mais da lista e clique **em Reconhecer**.

As seguintes colunas de informações estão disponíveis para alertas. Clique em **Editar colunas** e selecionar colunas que deseja visualizar.

| Coluna | Descrição |
------------ | ------------- |
| Tipo de alerta | Categoria de alerta.|
| Hora | Hora do alerta ocorreu. |
| Severity | Significado do alerta.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome Nuvem Privada. |
| Tipo de recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID de Recurso | Identificador do recurso. |
| Descrição | Descrição do que acionou o alerta. |
| Confirmado | Indicação se o alerta é reconhecido. |

## <a name="events"></a>Eventos

Eventos mostram a atividade do usuário e do sistema no portal CloudSimple. A página Eventos lista a atividade associada a um recurso específico e a gravidade do impacto.

As seguintes colunas de informações estão disponíveis para alertas. Clique em **Editar colunas** e selecionar colunas que deseja visualizar.

| Coluna | Descrição |
------------ | ------------- |
| Hora | Data e hora do evento ocorreu. |
| Tipo de evento | Código numérico que identifica o evento. |
| Severity | Gravidade do evento.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome Nuvem Privada. |
| Tipo de recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| Descrição | Descrição do que acionou o alerta. |

## <a name="tasks"></a>Tarefas

Tarefas são atividades de Nuvem Privada que devem levar 30 segundos ou mais para serem concluídas. (Atividades que devem levar menos de 30 segundos são relatadas apenas como eventos.) Abra as páginas Tarefas para acompanhar o progresso das tarefas para sua Nuvem Privada.

As seguintes colunas de informações estão disponíveis para alertas. Clique em **Editar colunas** e selecionar colunas que deseja visualizar.

| Coluna | Descrição |
------------ | ------------- |
| ID da Tarefa | Identificador único para a tarefa. |
| Operação | Ação que a tarefa realiza. |
| Usuário | Usuário designado para concluir a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID de Recurso | Identificador do recurso. |
| Iniciar | Hora de começar a tarefa. |
| End | Fim do tempo para a tarefa. |
| Status | Status da tarefa atual. |
| Tempo decorrido | Tempo que a tarefa levou para ser concluída (se concluída) ou está tomando (se estiver em andamento). |
| Descrição | Descrição da tarefa. |

## <a name="audit"></a>Audit

Os registros de auditoria acompanham a atividade do usuário. Você pode usar registros de auditoria para monitorar a atividade do usuário para todos os usuários.

As seguintes colunas de informações estão disponíveis para alertas. Clique em **Editar colunas** e selecionar colunas que deseja visualizar.

| Coluna | Descrição |
------------ | ------------- |
| Hora | Hora da entrada da auditoria. |
| Operação | Ação que a tarefa realiza. |
| Usuário | Usuário atribuído à tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID de Recurso | Identificador do recurso. |
| Result | Resultado da atividade, como **Sucesso.** |
| Tempo tomado | Hora de completar a tarefa. |
| Descrição | Descrição da ação. |

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
