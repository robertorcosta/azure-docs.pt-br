---
title: Perguntas frequentes sobre o provisionamento em nuvem do Azure AD Connect
description: Este documento descreve as perguntas frequentes sobre o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcf2df4e472d022fcdec0c9b7c69c73192c503f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518463"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Perguntas frequentes sobre provisionamento em nuvem Azure Active Directory Connect

Leia sobre as perguntas frequentes sobre o provisionamento de nuvem do Azure AD (Azure Active Directory) Connect.

## <a name="general-installation"></a>Instalação geral

**P: Com que frequência o provisionamento de nuvem é executado?**

O provisionamento de nuvem está agendado para ser executado a cada 2 minutos. A cada 2 minutos, quaisquer alterações de usuário, de grupo e de hash de senha serão provisionadas para o Azure AD.

**P: Estou vendo falhas de sincronização de hash de senha na primeira execução. Por quê?**

Isso é esperado. As falhas são devido ao objeto de usuário não estar presente no Azure AD. Depois que o usuário for provisionado no Azure AD, os hashes de senha deverão ser provisionados na execução subsequente. Aguarde algumas execuções e confirme se a sincronização de hash de senha já deixou de apresentar os erros.

**P: o que acontece se a instância de Active Directory tem atributos que não são suportados pelo provisionamento de nuvem (por exemplo, extensões de diretório)?**

O provisionamento em nuvem será executado e provisionará os atributos compatíveis. Os atributos não compatíveis não serão provisionados no Azure AD. Examine as extensões de diretório em Active Directory e certifique-se de que você não precisa desses atributos para fluir para o Azure AD. Se um ou mais atributos forem necessários, considere o uso da sincronização do Azure AD Connect ou mover as informações necessárias para um dos atributos compatíveis (por exemplo, atributos de extensão 1 a 15).

**P: Qual é a diferença entre a sincronização e o provisionamento de nuvem do Azure AD Connect?**

Com a sincronização do Azure AD Connect, o provisionamento é executado no servidor de sincronização local. A configuração é armazenada no servidor de sincronização local. Com o provisionamento de nuvem do Azure AD Connect, a configuração de provisionamento é armazenada na nuvem e é executada na nuvem como parte do serviço de provisionamento do Azure AD. 

**P: Posso usar o provisionamento de nuvem para sincronizar de várias florestas do Active Directory?**

Sim. O provisionamento de nuvem pode ser usado para sincronizar de várias florestas do Active Directory. No ambiente de várias florestas, todas as referências (por exemplo, gerente) precisam estar dentro do domínio.  

**P: Como o agente é atualizado?**

Os agentes são atualizados automaticamente pela Microsoft. Para a equipe de TI, isso reduz a carga de ter que testar e validar novas versões de agente. 

**P: Posso desabilitar a atualização automática?**

Não há nenhuma maneira compatível de desabilitar a atualização automática.

**P: Posso alterar a âncora de origem para o provisionamento de nuvem?**

Por padrão, o provisionamento de nuvem usa o ms-ds-consistency-GUID com um fallback para ObjectGUID como âncora de origem. Não há nenhuma maneira compatível de alterar a âncora de origem.

**P: Vejo novas entidades de serviço com os nomes de domínio do AD ao usar o provisionamento de nuvem. Isso é esperado?**

Sim, o provisionamento de nuvem cria uma entidade de serviço para a configuração de provisionamento com o nome de domínio como o nome da entidade de serviço. Não faça nenhuma alteração na configuração da entidade de serviço.

**P: O que acontece quando é exigido de um usuário sincronizado que ele altere a senha no próximo logon?**

Se a sincronização de hash de senha estiver habilitada no provisionamento de nuvem e o usuário sincronizado for necessário para alterar a senha no próximo logon no AD local, o provisionamento de nuvem não provisionará o hash de senha "a ser alterado" para o Azure AD. Depois que o usuário altera a senha, o hash de senha do usuário é provisionado do AD para o Azure AD.

**P: O provisionamento de nuvem dá suporte a write-back de ms-ds-consistencyGUID para algum objeto?**

Não, o provisionamento de nuvem não dá suporte a write-back de ms-ds-consistencyGUID para nenhum objeto (incluindo objetos de usuário). 

**P: Estou provisionando usuários usando o provisionamento de nuvem. Eu excluí a configuração. Por que eu ainda vejo os antigos objetos sincronizados no Azure AD?** 

Quando você exclui a configuração, o provisionamento de nuvem não remove automaticamente os objetos sincronizados no Azure AD. Para garantir que você não fique com objetos antigos, altere o escopo da configuração para um grupo vazio ou unidades organizacionais. Depois que o provisionamento é executado e limpa os objetos, desabilite e exclua a configuração. 

**P:  O que significa que o Exchange híbrido não tem suporte?**

O recurso de implantação híbrida do Exchange permite a coexistência de caixas de correio do Exchange no local e no Office 365. O Azure AD Connect está sincronizando um conjunto específico de atributos do Azure AD em seu diretório local.  No momento, o agente de provisionamento de nuvem não sincroniza esses atributos de volta em seu diretório local e, portanto, não tem suporte como uma substituição para o Azure AD Connect.

**P:  Posso instalar o agente de provisionamento de nuvem no Windows Server Core?**

Não, não há suporte para a instalação do agente no Server Core.

**P: posso usar um servidor de preparo com o agente de provisionamento de nuvem?**

Não, não há suporte para servidores de preparo.

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
