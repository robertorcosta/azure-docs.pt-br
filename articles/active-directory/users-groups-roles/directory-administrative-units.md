---
title: Gerenciamento de unidades administrativas (versão prévia) – Azure AD | Microsoft Docs
description: Usando unidades administrativas para delegação mais granular de permissões no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a01bb7d2f4aa3d31204d6235e955e82e471d5d
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729040"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gerenciamento de unidades administrativas no Azure Active Directory (versão prévia)

Este artigo descreve como criar unidades administrativas no Azure AD (Azure Active Directory). Uma unidade administrativa é um recurso do Azure AD que pode ser um contêiner para outros recursos do Azure AD. Nessa versão prévia, uma unidade administrativa pode conter apenas usuários e grupos.

Unidades administrativas permitem que você conceda permissões de administrador restritas a um departamento, uma região ou outro segmento de sua organização que você define. Você pode usar unidades administrativas para delegar permissões a administradores regionais ou definir uma política em um nível granular. Por exemplo, um administrador de conta de usuário poderia atualizar as informações do perfil, redefinir senhas e atribuir licenças para usuários apenas na unidade administrativa dele.

 Por exemplo, delegando a especialistas de suporte regional a função de [Administrador da assistência técnica](directory-assign-admin-roles.md#helpdesk-administrator) restrita ao gerenciamento de apenas usuários na região à qual eles dão suporte.

## <a name="deployment-scenario"></a>Cenário de implantação

Restringir o escopo administrativo usando unidades administrativas pode ser útil em organizações compostas por divisões independentes de qualquer tipo. Considere o exemplo de uma grande universidade composta por muitas faculdades autônomas (Escola de Negócios, Escola de Engenharia e assim por diante) que têm, cada uma, uma equipe de administradores de TI que controlam o acesso, gerenciam usuários e definem políticas para a faculdade deles. Um administrador central poderia:

- Criar uma função com permissões administrativas somente para usuários do Azure AD na unidade administrativa da escola de negócios
- Criar uma unidade administrativa para a Escola de Negócios
- Popular a unidade administrativa com apenas os alunos e os funcionários da escola de negócios
- Adicionar a equipe de TI da escola de negócios à função com o escopo dela

## <a name="license-requirements"></a>Requisitos de licença

O uso de unidades administrativas requer uma licença do Azure Active Directory Premium para cada administrador da unidade administrativa e licenças do Azure Active Directory Gratuito para membros da unidade administrativa. Para saber mais, consulte [Introdução ao AD Premium do Azure](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gerenciar unidades administrativas

Nesta versão prévia, você pode gerenciar unidades administrativas usando o portal do Azure, cmdlets e scripts do PowerShell ou o Microsoft Graph. Você pode consultar nossa documentação para obter detalhes:

- [Criar, remover, popular e adicionar funções a unidades administrativas](roles-admin-units-manage.md): concluir procedimentos detalhados
- [Trabalhar com Unidades Administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): como trabalhar com unidades administrativas usando o PowerShell
- [Suporte ao Graph da Unidade Administrativa](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): documentação detalhada no Microsoft Graph para unidades administrativas.

### <a name="planning-your-administrative-units"></a>Planejar suas unidades administrativas

As unidades administrativas podem ser usadas para agrupar logicamente recursos do Azure AD. Por exemplo, para uma organização cujo departamento de TI está globalmente disperso, pode fazer sentido criar unidades administrativas que definem esses limites geográficos. Em outro cenário em que uma organização multinacional tem diferentes “suborganizações”, que são semiautônomas em operações, cada uma delas pode ser representada por uma unidade administrativa.

Os critérios em que as unidades administrativas são criadas serão guiados pelos requisitos exclusivos de uma organização. As Unidades Administrativas são uma forma comum de definir a estrutura nos serviços M365. Recomendamos que você prepare suas unidades administrativas considerando o uso delas nos serviços M365. Você pode obter o valor máximo das unidades administrativas quando pode associar recursos comuns no M365 em uma unidade administrativa.

Você pode esperar que a criação de unidades administrativas na organização passe pelas seguintes fases:

1. Adoção inicial: sua organização começará a criar unidades administrativas com base em critérios iniciais e o número de unidades administrativas aumentará à medida que os critérios forem refinados.
1. Remoção: depois que os critérios forem bem definidos, as unidades administrativas que não forem mais necessárias serão excluídas.
1. Estabilização: Sua estrutura organizacional está bem definida e o número de unidades administrativas não será alterado significativamente em curto prazo.

## <a name="currently-supported-scenarios"></a>Cenários com suporte no momento

Os Administradores globais ou os Administradores de funções com privilégios podem usar o portal do Azure AD para criar unidades administrativas, adicionar usuários como membros de unidades administrativas e atribuir a equipe de TI a funções de administrador no escopo da unidade administrativa. Os administradores no escopo da unidade administrativa podem usar o portal do Office 365 para gerenciamento básico de usuários nas unidades administrativas deles.

Além disso, os grupos podem ser adicionados como membros da unidade administrativa, e um administrador de grupo no escopo da unidade administrativa pode gerenciá-los usando o PowerShell, o Microsoft Graph e o portal do Azure AD.

A tabela abaixo descreve o suporte atual para cenários de unidades administrativas.

### <a name="administrative-unit-management"></a>Gerenciamento de unidades administrativas

Permissões |   MS Graph/PowerShell   | Portal do Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Criar e excluir unidades administrativas   |    Com suporte    |   Com suporte   |    Sem suporte
Adicionar e remover membros de unidades administrativas individualmente    |   Com suporte    |   Com suporte   |    Sem suporte
Adicionar e remover membros de unidades administrativas em massa usando o arquivo .csv   |    Sem suporte     |  Com suporte   |    Nenhum plano para dar suporte
Atribuir administradores no escopo da unidade administrativa  |     Com suporte    |   Com suporte    |   Sem suporte
Adicionar e remover membros da unidade administrativa dinamicamente com base em atributos | Sem suporte | Sem suporte | Sem suporte

### <a name="user-management"></a>Gerenciamento de Usuários

Permissões |   MS Graph/PowerShell   | Portal do Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gerenciamento de propriedades do usuário, senhas e licenças no escopo da unidade administrativa   |    Com suporte     |  Com suporte   |   Com suporte
bloqueio e desbloqueio de entradas de usuário no escopo da unidade administrativa    |   Com suporte   |    Com suporte   |    Com suporte
gerenciamento de credenciais MFA do usuário no escopo da unidade administrativa   |    Com suporte   |   Com suporte   |   Sem suporte

### <a name="group-management"></a>Gerenciamento de grupos

Permissões |   MS Graph/PowerShell   | Portal do Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gerenciamento de membros e propriedades do grupo no escopo da unidade administrativa     |  Com suporte   |    Com suporte    |  Sem suporte
gerenciamento de licenciamento de grupo no escopo da unidade administrativa   |    Com suporte  |    Com suporte   |   Sem suporte

> [!NOTE]
>
> Os administradores com um escopo de unidade administrativa não podem gerenciar regras de associação de grupo dinâmica.

As unidades administrativas aplicam o escopo somente às permissões de gerenciamento. Elas não impedem os membros ou administradores de usar as [permissões de usuário padrão](../fundamentals/users-default-permissions.md) para procurar outros usuários, grupos ou recursos fora da unidade administrativa. No portal do Office 365, os usuários fora de unidades administrativas do administrador com escopo são filtrados, mas você pode procurar outros usuários no portal do Azure AD, no PowerShell e em outros serviços da Microsoft.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar unidades administrativas](roles-admin-units-manage.md)
- [Gerenciar usuários em unidades administrativas](roles-admin-units-add-manage-users.md)
- [Gerenciar grupos em unidades administrativas](roles-admin-units-add-manage-groups.md)
- [Atribuir regras com escopo a uma unidade administrativa](roles-admin-units-assign-roles.md)