---
title: Configurar o balanceamento de carga de área de trabalho virtual do Windows-Azure
description: Como configurar o método de balanceamento de carga para um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 447de339d3ceef7aeb1c232605b0e30bbbb1e7d8
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612428"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows

Configurar o método de balanceamento de carga para um pool de hosts permite que você ajuste o ambiente de área de trabalho virtual do Windows para atender melhor às suas necessidades.

>[!NOTE]
> Isso não se aplica a um pool de hosts de área de trabalho persistente porque os usuários sempre têm um mapeamento 1:1 para um host de sessão dentro do pool de hosts.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você seguiu as instruções em [Configurar o módulo do PowerShell de área de trabalho virtual do Windows](powershell-module.md) para baixar e instalar o módulo do PowerShell e entrar em sua conta do Azure.

## <a name="configure-breadth-first-load-balancing"></a>Configurar o balanceamento de carga da primeira amplitude

O balanceamento de carga da primeira amplitude é a configuração padrão para novos pools de hosts não persistentes. O balanceamento de carga da primeira distribuição distribui novas sessões de usuário em todos os hosts de sessão disponíveis no pool de hosts. Ao configurar o balanceamento de carga da primeira amplitude, você pode definir um limite máximo de sessão por host de sessão no pool de hosts.

Para configurar um pool de hosts para executar o balanceamento de carga de primeira amplitude sem ajustar o limite máximo de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' 
```

Depois disso, para certificar-se de que você definiu o método de balanceamento de carga da primeira amplitude, execute o seguinte cmdlet: 

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType 

Name             : hostpoolname 
LoadBalancerType : BreadthFirst
```

Para configurar um pool de hosts para executar o balanceamento de carga da primeira amplitude e usar um novo limite máximo de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar o balanceamento de carga de profundidade inicial

O balanceamento de carga em profundidade distribui novas sessões de usuário para um host de sessão disponível com o número mais alto de conexões, mas não atingiu seu limite máximo de limite de sessão. Ao configurar o balanceamento de carga de profundidade inicial, você deve definir um limite máximo de sessão por host de sessão no pool de hosts.

Para configurar um pool de hosts para executar o balanceamento de carga de profundidade primeiro, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ### 
```

Para verificar se a configuração foi atualizada, execute este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit 

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Configurar o balanceamento de carga com o portal do Azure

Você também pode configurar o balanceamento de carga com o portal do Azure.

Para configurar o balanceamento de carga:

1. Entre no portal do Azure em: https://portal.azure.com. 
2. Procure e selecione **área de trabalho virtual do Windows** em serviços. 
3. Na página área de trabalho virtual do Windows, selecione **pools de hosts**.
4. Selecione o nome do pool de hosts que você deseja editar.
5. Selecione **Propriedades**.
6. Insira o **limite máximo de sessão** no campo e selecione o **algoritmo de balanceamento de carga** desejado para esse pool de hosts no menu suspenso.
7. Clique em **Salvar**. Isso aplica as novas configurações de balanceamento de carga.