---
title: 'Azure AD Connect: problemas de âncora de fonte durante a instalação | Microsoft Docs'
description: Este tópico fornece etapas de como solucionar problemas com a âncora de origem durante a instalação.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114147"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Solução de problemas problemas de âncora de origem durante a instalação
Este artigo explica as diferentes questões relacionadas à âncora de origem que podem ocorrer durante a instalação e oferece maneiras de resolver esses problemas.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Âncora de fonte inválida no diretório ativo do Azure

### <a name="custom-installation"></a>Personalização Instalada

Durante a instalação personalizada, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existir no Azure Active Directory, o Azure AD Connect aplicará a mesma diretiva, a menos que seja substituída pelo cliente. O assistente informa qual atributo foi lido. Além disso, o assistente avisa se você tentar substituir a política de âncora de origem.

Durante esta operação de leitura, é possível que a política de âncora de origem no Azure Active Directory seja inesperada. Neste caso, o Azure AD Connect não sabe qual a âncora de origem a usar e precisa de substituição manual.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Para resolver esse problema, você pode substituir manualmente a âncora de origem selecionando um atributo específico. Proceda com essa opção se e somente se você tiver certeza de qual atributo selecionar. Se você não tiver certeza, entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se você alterar a política de âncora de origem, ela pode quebrar a associação entre seus usuários locais e seus recursos Azure associados.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalação Expressa
Durante a instalação expressa, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existir no Azure Active Directory, o Azure AD Connect aplicará a mesma diretiva. Não há opção de fazer a substituição manual.

Durante esta operação de leitura, é possível que a política de âncora de origem no Azure Active Directory seja inesperada. Neste caso, o Azure AD Connect não sabe qual deve ser a âncora de origem.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Para resolver esse problema, você precisa reinstalar usando o modo personalizado e substituir manualmente a âncora de origem selecionando um atributo específico. Proceda com essa opção se e somente se você tiver certeza de qual atributo selecionar. Se você não tiver certeza, entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se você alterar a política de âncora de origem, ela pode quebrar a associação entre seus usuários locais e seus recursos Azure associados.

### <a name="invalid-source-anchor-in-sync-engine"></a>Âncora de origem inválida no mecanismo sync
Durante a instalação, é possível que o Azure AD Connect tente configurar o mecanismo de sincronização usando uma âncora de origem inválida. Esta operação é provavelmente um problema de produto e a instalação do Azure AD Connect falhará. Entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) se você for executado neste problema.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [a integração de suas identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).