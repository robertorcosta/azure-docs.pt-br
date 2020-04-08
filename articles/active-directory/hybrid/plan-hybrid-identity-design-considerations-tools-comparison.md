---
title: 'Identidade Híbrida: comparação de ferramentas de integração de diretório | Microsoft Docs'
description: Esta é a página que fornece uma tabela abrangente que compara as diversas ferramentas de integração de diretório que podem ser usadas para a integridade de diretório.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811083"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação de ferramentas de integração de diretório da Identidade Híbrida
Ao longo dos anos, as ferramentas de integração de diretório cresceram e evoluíram.  


- [A FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) e [o MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) ainda são suportados e permitem principalmente a sincronização entre sistemas locais.   O [Conector AD FIM Windows Azure](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) é suportado tanto no FIM quanto no MIM, mas não é recomendado para novas implantações - clientes com fontes de conexão local, como Notes ou SAP HCM, devem usar o MIM para preencher o Active Directory Domain Services (AD DS) e, em seguida, também usar o Azure AD Connect sync ou o azure AD Connect para sincronizar do AD DS para o Azure AD.
- [O Azure AD Connect sync](how-to-connect-sync-whatis.md) incorpora os componentes e funcionalidades anteriormente lançados no DirSync e no Azure AD Sync, para sincronização entre florestas AD DS e Azure AD.  
- [O provisionamento em nuvem do Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) é um novo agente da Microsoft para sincronização de AD DS para Azure AD, útil para cenários como fusão e aquisição onde as florestas AD da empresa adquirida são isoladas das florestas AD da empresa-mãe.

Para saber mais sobre as diferenças entre o azure AD Connect sync e o provisionamento de nuvem Do Azure AD Connect, consulte o artigo O que é o [provisionamento de nuvem do Azure AD Connect?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [a integração de suas identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).

