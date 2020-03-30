---
title: Corrigir problemas com membros de grupo dinâmico - Azure AD | Microsoft Docs
description: Dicas de solução de problemas para a adesão a grupos dinâmicos no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026552"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Solucionar problemas e resolver problemas de grupos

## <a name="troubleshooting-group-creation-issues"></a>Problemas de criação de grupos

**Desabilitei a criação do grupo de segurança no portal Azure, mas os grupos ainda podem ser criados via Powershell** O **Usuário pode criar grupos de segurança nos portais do Azure** configurando no portal Azure controles se os usuários não-administradores podem ou não criar grupos de segurança no painel Access ou no portal Azure. Ele não controla a criação de grupos de segurança via Powershell.

Para desativar a criação de grupo para usuários não administradores no Powershell:
1. Verifique se os usuários não administradores têm permissão para criar grupos:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se retornar `UsersPermissionToCreateGroupsEnabled : True`, os usuários não administradores podem criar grupos. Para desabilitar esse recurso:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um erro máximo dos grupos ao tentar criar um Grupo Dinâmico no Powershell**<br/>
Se você receber uma mensagem no Powershell indicando _políticas de grupo dinâmicas, a contagem máxima de grupos é atingida,_ isso significa que você atingiu o limite máximo para grupos Dinâmicos em seu inquilino. O número máximo de grupos Dinâmicos por inquilino é de 5.000.

Para criar novos grupos Dinâmicos, primeiro você precisará excluir alguns grupos Dinâmicos existentes. Não há como aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solucionando problemas de associações dinâmicas a grupos

**Configurei uma regra em um grupo, mas nenhuma associação foi atualizada no grupo**<br/>
1. Verifique os valores dos atributos do usuário ou do dispositivo na regra. Certifique-se de que há usuários que satisfazem a regra. Para dispositivos, verifique as propriedades do dispositivo para garantir que quaisquer atributos sincronizados contenham os valores esperados.<br/>
2. Verifique o status de processamento de membros para confirmar se ele está completo. Você pode verificar o status de [processamento de membros](groups-create-rule.md#check-processing-status-for-a-rule) e a última data atualizada na página Visão **Geral** do grupo.

Se tudo estiver correto, aguarde alguns instantes para que o grupo seja populado. Dependendo do tamanho do seu locatário, o grupo pode levar até 24 horas para ser populado pela primeira vez ou depois de uma alteração de regra.

**Configurei uma regra, mas agora os membros da regra existentes foram removidos**<br/>Este comportamento é esperado. Membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.

**Não vejo as alterações da associação instantaneamente quando adiciono ou altero uma regra, por que não?**<br/>A avaliação de associação dedicada é feita periodicamente em um processo assíncrono em segundo plano. O tempo que o processo leva é determinado pelo número de usuários no diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de usuários verão as alterações de associação de grupo em poucos minutos. Os diretórios com um grande número de usuários podem levar até 30 minutos ou mais para serem populados.

**Como posso forçar o grupo a ser processado agora?**<br/>
Atualmente, não há como acionar automaticamente o grupo a ser processado demanda. No entanto, você pode acionar manualmente o reprocessamento atualizando a regra de adesão para adicionar um espaço em branco no final.  

**Eu encontrei uma erro de processamento de regra**<br/>A seguinte tabela relacionará os erros de regra de associação e como corrigi-los.

| Erro do analisador de regra | Erro de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade <br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](groups-dynamic-membership.md#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/><br/>Não há suporte para o operador usado para o tipo de propriedade (neste exemplo, -contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Operador desaparecido. Use -and ou -or para unir predicados<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Erro na expressão regular usada com -match<br>(user.userPrincipalName -match "*@domain.ext")<br>ou alternativamente: (user.userPrincipalName -match "@domain.ext") |

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md)
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrando suas identidades locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)