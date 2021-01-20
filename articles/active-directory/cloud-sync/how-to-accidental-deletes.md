---
title: Azure AD Connect exclusões acidentais de sincronização de nuvem
description: Este tópico descreve como usar o recurso de exclusão acidental para evitar exclusões.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613052"
---
# <a name="accidental-delete-prevention"></a>Prevenção acidental de exclusão

O documento a seguir descreve o recurso de exclusão acidental para Azure AD Connect a sincronização de nuvem.  O recurso de exclusão acidental foi projetado para protegê-lo contra alterações de configuração acidentais e alterações em seu diretório local que afetariam muitos usuários e grupos.  Esse recurso permite que você:

- Configure a capacidade de impedir exclusões acidentais automaticamente. 
- Definir o número de objetos (limite) Além do qual a configuração entrará em vigor 
- Configure um endereço de email de notificação para que eles possam receber uma notificação por email depois que o trabalho de sincronização em questão for colocado em quarentena para esse cenário 

Para usar esse recurso, defina o limite para o número de objetos que, se forem excluídos, a sincronização deverá parar.  Portanto, se esse número for atingido, a sincronização será interrompida e uma notificação será enviada para o email especificado.  Isso permite investigar o que está acontecendo.


## <a name="configure-accidental-delete-prevention"></a>Configurar prevenção acidental de exclusão
Para usar o novo recurso, siga as etapas abaixo.


1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure AD Connect**.
3.  Selecione **gerenciar sincronização de nuvem**.
4. Em **configuração**, selecione sua configuração.
5. Em **configurações** , preencha o seguinte:
    - **Email de notificação** -email usado para notificações
    - **Impedir exclusões acidentais** -Marque esta caixa para habilitar o recurso
    - **Limite de exclusão acidental** – Insira um número de objetos para disparar a interrupção de sincronização e a notificação

![Exclusões acidentais](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Próximas etapas 

- [O que é Azure AD Connect a sincronização de nuvem?](what-is-cloud-sync.md)
- [Como instalar Azure AD Connect sincronização de nuvem](how-to-install.md)
 

