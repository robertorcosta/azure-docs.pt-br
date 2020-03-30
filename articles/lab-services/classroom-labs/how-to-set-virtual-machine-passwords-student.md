---
title: Redefinir senhas para VMs de laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Aprenda a redefinir senhas para máquinas virtuais (VMs) em laboratórios de sala de aula do Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583686"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Definir ou redefinir senha para máquinas virtuais em laboratórios de sala de aula (alunos)
Este artigo mostra como os alunos podem definir/redefinir a senha de suas VMs. 

## <a name="enable-resetting-of-passwords"></a>Habilitar a redefinição de senhas
No momento da criação do laboratório, o proprietário do laboratório pode ativar ou desativar a **mesma senha de uso para todas as máquinas virtuais**. Se essa opção foi ativada, os alunos não poderão redefinir a senha. Todas as VMs nos laboratórios terão a mesma senha que o instrutor. 

Se essa opção estiver desativada, os usuários terão que definir uma senha ao tentar se conectar à VM pela primeira vez. Os alunos também podem redefinir a senha mais tarde, a qualquer momento, conforme mostrado na última seção deste artigo. 

## <a name="reset-password-for-the-first-time"></a>Redefinir senha pela primeira vez
Se a **opção Usar a mesma senha para todas as máquinas virtuais** foi desativada, quando os usuários (alunos) selecionarem o botão **Conectar** no bloco de laboratório na página **Minhas máquinas virtuais,** o usuário verá a seguinte caixa de diálogo para definir a senha da VM: 

![Redefinir senha para o aluno](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Redefinir senha mais tarde
O aluno também pode definir a senha clicando no menu de estouro **(três apontadores verticais)** no azulejo do laboratório e selecionando **Redefinir senha**. 

![Redefinir senha mais tarde](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre outras opções de uso do aluno que um proprietário de laboratório pode configurar, consulte o seguinte artigo: [Configure o uso do aluno](how-to-configure-student-usage.md).
