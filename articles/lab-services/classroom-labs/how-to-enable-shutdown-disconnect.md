---
title: Habilite o desligamento de VMs na desconexão dos serviços do Azure Lab | Microsoft Docs
description: Saiba como ativar ou desativar o desligamento automático das VMs quando uma conexão remota da área de trabalho estiver desconectada.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117139"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Habilite o desligamento automático de VMs na desconexão
Este artigo mostra como você pode ativar ou desativar o desligamento automático das VMs do **laboratório windows 10** (modelo ou aluno) depois que uma conexão remota da área de trabalho é desconectada. Você também pode especificar quanto tempo as VMs devem esperar para que o usuário se reconecte antes de ser automaticamente desligado.

Um administrador de conta de laboratório pode configurar essa configuração para a conta de laboratório na qual você cria laboratórios. Para obter mais informações, consulte [Configurar o desligamento automático de VMs na desconexão para uma conta de laboratório](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Como dono de laboratório, você pode substituir a configuração ao criar um laboratório ou depois que o laboratório é criado. 

## <a name="configure-when-creating-a-lab"></a>Configure ao criar um laboratório
Na etapa 3 do assistente de criação de laboratório, você pode ativar ou desativar esse recurso e também especificar quanto tempo a VM deve esperar para que o usuário se reconecte antes de ser automaticamente desligado. 

![Configurar no momento da criação do laboratório](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configure depois que o laboratório for criado
Você pode configurar essa configuração na página **Configurações** como mostrado na imagem a seguir: 

![Configure depois que o laboratório for criado](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se você desligar o sistema operacional Windows (OS) em uma VM antes de desligar uma sessão RDP na VM, o recurso de desligamento automático não funcionará corretamente.  

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Painel para laboratórios de sala de aula](use-dashboard.md)