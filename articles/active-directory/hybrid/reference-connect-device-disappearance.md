---
title: Entendendo o Azure AD Connect 1.4.xx.x e o desaparecimento do dispositivo | Microsoft Docs
description: Este documento descreve um problema que surge com a versão 1.4.xx.x do Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176032"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Entendendo o Azure AD Connect 1.4.xx.x e o desaparecimento do dispositivo
Com a versão 1.4.xx.x do Azure AD Connect, alguns clientes podem ver alguns ou todos os seus dispositivos Windows desaparecerem do Azure AD. Isso não é motivo de preocupação, pois essas identidades de dispositivos não são usadas pelo Azure AD durante a autorização de acesso condicional. Essa alteração não excluirá nenhum dispositivo Windows que tenha sido corretamente registrado no Azure AD para Hybrid Azure AD Join.

Se você vir a exclusão de objetos do dispositivo no Azure AD excedendo o Limite de Exclusão de Exportação, é aconselhável que o cliente permita que as exclusões passem. [Como: permitir que as exclusões fluam quando excedem o limite de exclusão](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Segundo plano
Os dispositivos Windows registrados como Hybrid Azure AD Joined são representados no Azure AD como objetos de dispositivo. Esses objetos do dispositivo podem ser usados para acesso condicional. Os dispositivos Windows 10 são sincronizados com a nuvem via Azure AD Connect, os dispositivos Windows de nível baixo são registrados diretamente usando AD FS ou logon único sem emendas.

## <a name="windows-10-devices"></a>Dispositivos Windows 10
Apenas dispositivos Windows 10 com um valor de atributo de usuário específico configurado pelo Hybrid Azure AD Join devem ser sincronizados na nuvem pelo Azure AD Connect. Nas versões anteriores do Azure AD Connect, esse requisito não foi rigorosamente aplicado, resultando em objetos desnecessários do dispositivo no Azure AD. Tais dispositivos no Azure AD sempre permaneceram no estado "pendente" porque esses dispositivos não tinham a intenção de ser registrados no Azure AD. 

Esta versão do Azure AD Connect só sincronizará dispositivos do Windows 10 que estão configurados corretamente para serem O AD Híbrido Azure. Objetos do dispositivo Windows 10 sem o Azure AD se juntar em usuário específicoCertificado será removido do Azure AD.

## <a name="down-level-windows-devices"></a>Dispositivos Windows de baixo nível
O Azure AD Connect nunca deve estar sincronizando [dispositivos Windows de nível baixo](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Todos os dispositivos no Azure AD sincronizados incorretamente serão excluídos do Azure AD. Se o Azure AD Connect está tentando excluir [dispositivos Windows de nível baixo,](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)então o dispositivo não é o que foi criado pelo Microsoft Workplace Join para [computadores não Windows 10 MSI](https://www.microsoft.com/download/details.aspx?id=53554) e não pode ser consumido por qualquer outro recurso do Azure AD.

Alguns clientes podem precisar revisitar [How To: Plan your hybrid Azure Active Directory join implementation](../devices/hybrid-azuread-join-plan.md) to get their Windows devices registercorrect correctly and ensure that such devices can full a device-based Conditional Access. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Como posso verificar quais dispositivos são excluídos com esta atualização?

Para verificar quais dispositivos estão excluídos, você pode usar este script PowerShell:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Esse script gera um relatório sobre certificados armazenados em objetos do Active Directory Computer, especificamente, certificados emitidos pelo recurso de adesão do Hybrid Azure AD.
Ele verifica os certificados presentes na propriedade UserCertificate de um objeto de Computador em AD e, para cada certificado não expirado presente, valida se o certificado foi emitido para o recurso de adesão do Azure AD híbrido (ou seja, O Nome do Assunto corresponde a CN={ObjectGUID}).
Antes, o Azure AD Connect sincronizaria com o Azure AD qualquer computador que contivesse pelo menos um certificado válido, mas a partir da versão 1.4 do Azure AD Connect, o mecanismo de sincronização pode identificar certificados de adesão do Hybrid Azure AD e 'filtrar' a nuvem objeto de computador de sincronização com Azure AD, a menos que haja um certificado de adesão a adesão a azure híbrido válido.
Os dispositivos Azure AD que já estavam sincronizados com a AD, mas não possuem um certificado de adesão a AD híbrido válido serão excluídos (CloudFiltered=TRUE) pelo mecanismo de sincronização.

## <a name="next-steps"></a>Próximas etapas
- [Histórico da versão do Azure AD Connect](reference-connect-version-history.md)
