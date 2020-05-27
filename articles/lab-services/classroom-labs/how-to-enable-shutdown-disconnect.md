---
title: Habilitar desligamento de VMs ao desconectar o Azure Lab Services | Microsoft Docs
description: Saiba como habilitar ou desabilitar o desligamento automático de VMs quando uma conexão de área de trabalho remota for desconectada.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: b0c7f5daa6bcd9ab5cb8f4d7b1a513a15cd1c708
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591702"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Habilitar desligamento automático de VMs ao desconectar
Este artigo mostra como você poderá habilitar ou desabilitar o desligamento automático de VMs de laboratório do **Windows 10** (modelo ou aluno) depois que uma conexão de área de trabalho remota for desconectada. Você também poderá especificar quanto tempo as VMs deverão esperar até o usuário se reconectar antes do desligamento automático.

Um administrador da conta de laboratório pode definir essa configuração para a conta de laboratório na qual você cria laboratórios. Para obter mais informações, confira [Configurar o desligamento automático de VMs ao desconectar para uma conta de laboratório](how-to-configure-lab-accounts.md). Como proprietário de um laboratório, você poderá substituir a configuração ao criar um laboratório ou depois que o laboratório for criado. 

## <a name="configure-when-creating-a-lab"></a>Configurar ao criar um laboratório
Na página etapa 3 do assistente de criação do laboratório, você pode habilitar ou desabilitar esse recurso e também especificar quanto tempo a VM deverá aguardar até que o usuário se reconecte antes do desligamento automático. 

![Configurar no momento da criação do laboratório](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurar após a criação do laboratório
Você pode definir essa configuração na página **Configurações**, conforme mostrado na imagem a seguir: 

![Configurar após a criação do laboratório](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se você desligar o SO (sistema operacional) Windows em uma VM antes de desconectar uma sessão RDP com a VM, o recurso de desligamento automático não funcionará corretamente.  

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Painel para laboratórios de sala de aula](use-dashboard.md)