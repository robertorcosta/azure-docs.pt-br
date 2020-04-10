---
title: Azure Application Insights Agent Disable-InstrumentationEngine
description: Referência de API do Agente de Insights de Aplicativos. Desabilitar-InstrumentationEngine. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998379"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>API do agente de insights de aplicativos: Disable-InstrumentationEngine

Este artigo descreve um cmdlet que é um membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição
Desativa o mecanismo de instrumentação removendo algumas teclas de registro.
Reinicie o IIS para que as alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>parâmetros 

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para produzir logs detalhados de saída.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Saída de exemplo de desativar com sucesso o mecanismo de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Próximas etapas

 Faça mais com o Agente de Insights de Aplicativos:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.
