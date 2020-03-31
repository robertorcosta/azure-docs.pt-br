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
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456910"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação de ferramentas de integração de diretório da Identidade Híbrida
Ao longo dos anos, as ferramentas de integração de diretório cresceram e evoluíram.  Este documento tem o objetivo de ajudar a fornecer uma visão consolidada dessas ferramentas e uma comparação dos recursos que estão disponíveis em cada uma.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> O Azure AD Connect incorpora os componentes e funcionalidades anteriormente lançados como Dirsync e AAD Sync. Essas ferramentas não estão mais sendo lançadas individualmente, e todas as melhorias futuras serão incluídas nas atualizações do Azure AD Connect, para que você saiba sempre onde obter a funcionalidade mais atual.
> 
> DirSync e Azure AD Sync foram preteridos. Mais informações podem ser encontradas [aqui](reference-connect-dirsync-deprecated.md).
> 
> 

Use a seguinte chave para cada uma das tabelas.

● = Disponível Agora  
FR = Versão Futura  
PP = Versão prévia pública  

## <a name="on-premises-to-cloud-synchronization"></a>Local para a sincronização de nuvem
| Recurso | Conexão do Active Directory do Azure | Serviços de Sincronização do Azure Active Directory (AAD Sync) - NÃO TEM MAIS SUPORTE | Ferramenta de Sincronização do Azure Active Directory (DirSync) - NÃO TEM MAIS SUPORTE | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Conectar-se a única floresta do AD local |● |● |● |● |● |
| Conectar-se a várias florestas do AD local |● |● | |● |● |
| Conectar-se a várias organizações de Exchange local |● | | | | |
| Conectar-se a um único diretório LDAP local |●* | | |● |● | 
| Conectar-se a vários diretórios LDAP local |●*  | | |● |● | 
| Conectar-se a diretórios AD locais e LDAP locais |●* | | |● |● | 
| Conecte-se aos sistemas personalizados (por exemplo, SQL, Oracle, MySQL, etc.) |FR | | |● |● |
| Sincronize atributos definido pelo cliente (extensões de diretório) |● | | | | |
| Conectar-se ao RH local (isto é, SAP, Oracle eBusiness,PeopleSoft) |FR | | |● |● |
| Oferece suporte a regras de sincronização do FIM e conectores para provisionamento para os sistemas locais. | | | |● |● |

 
&#42; Atualmente, há duas opções com suporte para isso.  Eles são: 

   1. Você pode usar o conector LDAP genérico e habilitá-lo fora do Azure AD Connect.  Isso é complexo e exige um parceiro de integração e um contrato de suporte premier para manter.  Essa opção pode lidar com um ou vários diretórios LDAP. 

   2. Você pode desenvolver sua própria solução para mover objetos do LDAP para o Active Directory.  Em seguida, sincronize os objetos com o Azure AD Connect.  MIM ou FIM pode ser usado como uma possível solução para mover os objetos. 

## <a name="cloud-to-on-premises-synchronization"></a>Nuvem para sincronização no local
| Recurso | Conexão do Active Directory do Azure | Serviços de Sincronização do Azure Active Directory - NÃO TEM MAIS SUPORTE  | Ferramenta de Sincronização do Azure Active Directory (DirSync) - NÃO TEM MAIS SUPORTE  | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Write-back de dispositivos |● | |● | | |
| Write-back de atributo (para implantação híbrida do Exchange) |● |● |● |● |● |
| Write-back de objetos de grupos |● | | | | |
| Write-back de senhas (de redefinição de senha de autoatendimento (SSPR) e alteração de senha) |● |● | | | |

## <a name="authentication-feature-support"></a>Suporte ao recurso de autenticação
| Recurso | Conexão do Active Directory do Azure | Serviços de Sincronização do Azure Active Directory - NÃO TEM MAIS SUPORTE  | Ferramenta de Sincronização do Azure Active Directory (DirSync) - NÃO TEM MAIS SUPORTE  | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Sincronização de hash de senha para a floresta única do AD local |●|●|● | | |
| Sincronização de hash de senha para várias florestas do AD local |●|● | | | |
| Autenticação de passagem para florestas individuais do AD local |●| | | | |
| Logon único com federação |● |● |● |● |● |
| Logon Único Contínuo|● |||||
| Write-back de senhas (de alteração SSPR e senha) |● |● | | | |

## <a name="set-up-and-installation"></a>Instalação e configuração
| Recurso | Conexão do Active Directory do Azure | Serviços de Sincronização do Azure Active Directory - NÃO TEM MAIS SUPORTE  | Ferramenta de Sincronização do Azure Active Directory (DirSync) - NÃO TEM MAIS SUPORTE  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Oferece suporte à instalação em um controlador de domínio |● |● |● | |
| Oferece suporte à instalação usando o SQL Express |● |● |● | |
| Atualização fácil de DirSync |● | | | |
| Localização do UX do Administrador para linguagens do Windows Server |● |● |● | |
| Localização do UX do usuário final para linguagens do Windows Server | | | |● |
| Suporte para Windows Server 2008 e Windows Server 2008 R2 |● para sincronização, não para federação |● |● |● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtragem e configuração
| Recurso | Conexão do Active Directory do Azure | Serviços de Sincronização do Azure Active Directory - NÃO TEM MAIS SUPORTE  | Ferramenta de Sincronização do Azure Active Directory (DirSync) - NÃO TEM MAIS SUPORTE  | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrar em domínios e unidades organizacionais |● |● |● |● |● |
| Filtrar os valores de atributo de objetos |● |● |● |● |● |
| Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) |● |● | | | |
| Permitir que os modelos de serviço diferentes a sejam aplicados para fluxos de atributo |● |● | | | |
| Permitir a remoção de atributos do fluxo do AD para o AD do Azure |● |● | | | |
| Permitir a personalização avançada para fluxos de atributo |● |● | |● |● |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [a integração de suas identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).

