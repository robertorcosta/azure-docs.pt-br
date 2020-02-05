---
title: Soluções VMware do Azure (AVS)-monitorar a atividade de nuvem privada AVS
description: Descreve as informações disponíveis sobre atividades na solução VMware do Azure por meio do ambiente AVS, incluindo alertas, eventos, tarefas e auditoria.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019665"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Monitorar atividade de soluções VMware (AVS)

Os logs de atividade de AVS fornecem uma percepção das operações realizadas no portal da AVS. A lista inclui alertas, eventos, tarefas e auditoria. Use os logs de atividade para determinar quem, quando e quais operações foram executadas. Os logs de atividades não incluem nenhuma operação de leitura feita por um usuário.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Acessar o portal da AVS

Acesse o [portal da AVS](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informações da atividade

Para acessar as páginas de atividade, selecione **atividade** no menu lateral.

![Visão geral da página de atividade](media/activity-page-overview.png)

Para exibir detalhes sobre qualquer uma das atividades na página atividade, selecione a atividade. Um painel de detalhes é aberto à direita. As ações no painel dependem do tipo de atividade. Clique em **X** para fechar o painel.

Clique em um cabeçalho de coluna para classificar a exibição. Você pode filtrar colunas para ver valores específicos. Baixe o relatório de atividades clicando no ícone **baixar como CSV** .

## <a name="alerts"></a>Alertas

Alertas são notificações de qualquer atividade significativa em seu ambiente de AVS. Os alertas incluem eventos que afetam a cobrança ou o acesso do usuário.

Para confirmar os alertas e removê-los da lista, selecione um ou mais na lista e clique em **reconhecer**.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Description |
------------ | ------------- |
| Tipo de alerta | Categoria do alerta.|
| Tempo | Hora em que o alerta ocorreu. |
| Gravidade | Significância do alerta.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome da nuvem privada AVS. |
| Tipo de recurso | Categoria do recurso: sincronização de nuvem privada de AVS, rack de nuvem. |
| ID de Recurso | Identificador do recurso. |
| Description | Descrição do que disparou o alerta. |
| Confirmado | Indica se o alerta é confirmado. |

## <a name="events"></a>Eventos

Eventos mostram a atividade de usuário e sistema no portal da AVS. A página eventos lista a atividade associada a um recurso específico e a gravidade do impacto.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Description |
------------ | ------------- |
| Tempo | Data e hora em que o evento ocorreu. |
| Tipo de evento | Código numérico que identifica o evento. |
| Gravidade | Severidade do evento.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome da nuvem privada AVS. |
| Tipo de recurso | Categoria do recurso: sincronização de nuvem privada de AVS, rack de nuvem. |
| Description | Descrição do que disparou o alerta. |

## <a name="tasks"></a>Tarefas

As tarefas são atividades de nuvem privada de AVS que devem levar 30 segundos ou mais para serem concluídas. (As atividades que devem levar menos de 30 segundos são relatadas apenas como eventos.) Abra as páginas tarefas para acompanhar o progresso das tarefas da sua nuvem privada de AVS.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Description |
------------ | ------------- |
| ID da Tarefa | Identificador exclusivo da tarefa. |
| Operação | Ação executada pela tarefa. |
| Usuário | Usuário atribuído para concluir a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de recurso | Categoria do recurso: sincronização de nuvem privada de AVS, rack de nuvem. |
| ID de Recurso | Identificador do recurso. |
| Iniciar | Hora de início da tarefa. |
| Terminar | Hora de término da tarefa. |
| Status | Status da tarefa atual. |
| Tempo decorrido | Tempo que a tarefa levou para ser concluída (se estiver concluída) ou que está sendo executada (se em andamento). |
| Description | Descrição da tarefa. |

## <a name="audit"></a>Audit

Os logs de auditoria controlam a atividade do usuário. Você pode usar os logs de auditoria para monitorar a atividade do usuário para todos os usuários.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Description |
------------ | ------------- |
| Tempo | Hora da entrada de auditoria. |
| Operação | Ação executada pela tarefa. |
| Usuário | Usuário atribuído à tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de recurso | Categoria do recurso: sincronização de nuvem privada de AVS, rack de nuvem. |
| ID de Recurso | Identificador do recurso. |
| Result | Resultado da atividade, como **êxito**. |
| Tempo decorrido | Tempo para concluir a tarefa. |
| Description | Descrição da ação. |

## <a name="next-steps"></a>Próximos passos

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre as [nuvens privadas da AVS](cloudsimple-private-cloud.md)
