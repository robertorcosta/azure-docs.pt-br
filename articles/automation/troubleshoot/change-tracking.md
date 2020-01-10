---
title: Solução de problemas com o Controle de Alterações do Azure
description: Saiba como solucionar e resolver problemas com o Controle de Alterações de automação do Azure e o recurso de inventário.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 51a9dbf8be6538534c05a4b8b6fcd913ef8c6ae3
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769924"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Solucionar problemas do Controle de Alterações e do Inventário

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Cenário: registros de Controle de Alterações não estão sendo exibidos para computadores Windows

#### <a name="issue"></a>Problema

Você não vê quaisquer resultados de Inventário ou de Controle de Alterações em computadores Windows com o Controle de Alterações ativado.

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. O **Microsoft Monitoring Agent** não está em execução
2. A comunicação com a Conta de Automação está sendo bloqueada.
3. Os pacotes de gerenciamento para o Controle de Alterações não foram baixados.
4. A VM que está sendo integrada pode ter vindo de um computador clonado sem ter sido preparada (sysprep) com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Verifique se o **Microsoft Monitoring Agent** (HealthService.exe) está em execução no computador.
1. Verifique o **Visualizador de Eventos** na máquina e procure quaisquer eventos com a palavra `changetracking` presente.
1. Visite [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber quais endereços e portas devem ter permissão para que Controle de Alterações funcione.
1. Verifique se os seguintes pacotes de gerenciamento de Controle de Alterações e de Inventário existem localmente:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Se estiver usando uma imagem clonada, primeiro faça sysprep da imagem e depois instale o agente do Microsoft Monitoring Agent.

Se essas soluções não resolverem seu problema, e você entrar em contato com o suporte, execute os comandos a seguir para coletar o diagnóstico no agente

No computador do agente, navegue até `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Por padrão, o rastreamento de erros está habilitado. Se você quiser habilitar mensagens de erro detalhadas, como no exemplo anterior, use o parâmetro `VER`. Para rastreamentos de informações, use `INF` ao invocar `StartTracing.cmd`.

## <a name="next-steps"></a>Próximos passos

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
