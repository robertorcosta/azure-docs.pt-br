---
title: Gerenciamento de unidades administrativas (visualização)-Azure AD | Microsoft Docs
description: Usando unidades administrativas para delegação mais granular de permissões no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406451"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gerenciamento de unidades administrativas no Azure Active Directory (versão prévia)

Este artigo descreve as unidades administrativas no Azure Active Directory (AD do Azure). Uma unidade administrativa é um recurso do Azure AD que pode ser um contêiner para outros recursos do Azure AD. Nesta versão de visualização, uma unidade administrativa pode conter apenas usuários e grupos.

As unidades administrativas permitem que você conceda permissões de administrador que são restritas a um departamento, região ou outro segmento da sua organização que você define. Você pode usar unidades administrativas para delegar permissões para administradores regionais ou para definir a política em um nível granular. Por exemplo, um administrador de conta de usuário pode atualizar informações de perfil, redefinir senhas e atribuir licenças para usuários somente em sua unidade administrativa.

 Por exemplo, delegando a especialistas de suporte regional a função de [administrador de assistência técnica](directory-assign-admin-roles.md#helpdesk-administrator) restrita ao gerenciamento apenas dos usuários na região à qual dão suporte.

## <a name="deployment-scenario"></a>Cenário de implantação

Restringir o escopo administrativo usando unidades administrativas pode ser útil em organizações que são constituídas de divisões independentes de qualquer tipo. Considere o exemplo de uma grande universidade que é composta por muitas escolas autônomas (escola de negócios, escola de engenharia e assim por diante) que cada uma delas tem uma equipe de administradores de ti que controlam o acesso, gerenciam usuários e definem políticas para sua escola. Um administrador central poderia:

- Criar uma função com permissões administrativas somente para usuários do Azure AD na unidade administrativa da escola de negócios
- Criar uma unidade administrativa para a escola de negócios
- Popular a unidade de administração com apenas os alunos e a equipe de ensino comercial
- Adicione a equipe de ti da escola de negócios à função com seu escopo

## <a name="license-requirements"></a>Requisitos de licença

O uso de unidades administrativas requer uma licença de Azure Active Directory Premium para cada administrador de unidade administrativa e Azure Active Directory Gratuito licenças para membros da unidade administrativa. Para obter mais informações, consulte [introdução ao Azure ad Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gerenciar unidades administrativas

Nesta versão de visualização, você pode gerenciar unidades administrativas usando o portal do Azure, os cmdlets e scripts do PowerShell ou o Microsoft Graph. Você pode consultar nossa documentação para obter detalhes:

- [Criar, remover, popular e adicionar funções a unidades administrativas](roles-admin-units-manage.md): procedimentos completos de instruções
- [Trabalhando com unidades de administrador](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): como trabalhar com unidades administrativas usando o PowerShell
- [Suporte ao grafo de unidade administrativa](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): documentação detalhada sobre Microsoft Graph para unidades administrativas.

### <a name="planning-your-administrative-units"></a>Planejando suas unidades administrativas

As unidades administrativas podem ser usadas para agrupar logicamente os recursos do Azure AD. Por exemplo, para uma organização cujo departamento de ti é disperso globalmente, pode fazer sentido criar unidades administrativas que definam esses limites geográficos. Em outro cenário em que uma organização multinacional tem diferentes "suborganizações", que são semiautônomas em operações, cada suborganização pode ser representada por uma unidade administrativa.

Os critérios em que as unidades administrativas são criadas serão guiados pelos requisitos exclusivos de uma organização. As unidades administrativas são uma maneira comum de definir a estrutura nos serviços M365s. Recomendamos que você prepare suas unidades administrativas com seu uso nos serviços M365 em mente. Você pode obter o valor máximo das unidades administrativas quando você pode associar recursos comuns ao M365 em uma unidade administrativa.

Você pode esperar que a criação de unidades administrativas na organização passe pelos seguintes estágios:

1. Adoção inicial: sua organização começará a criar unidades administrativas com base em critérios iniciais e o número de unidades administrativas aumentará à medida que os critérios forem refinados.
1. Remoção: depois que os critérios forem bem definidos, as unidades administrativas que não forem mais necessárias serão excluídas.
1. Estabilização: sua estrutura organizacional está bem definida e o número de unidades administrativas não vai mudar significativamente além de durações curtas.

## <a name="currently-supported-scenarios"></a>Cenários com suporte no momento

Os administradores globais ou os administradores de função com privilégios podem usar o portal do AD do Azure para criar unidades administrativas, adicionar usuários como membros de unidades administrativas e atribuir a equipe de ti a funções administrativas de administrador no escopo da unidade. Os administradores com escopo de unidade administrativa podem usar o portal do Office 365 para o gerenciamento básico de usuários em suas unidades administrativas.

Além disso, os grupos podem ser adicionados como membros da unidade administrativa e um administrador de grupo com escopo de unidade de administrador pode gerenciá-los usando o PowerShell, o Microsoft Graph e o portal do AD do Azure.

A tabela abaixo descreve o suporte atual para cenários de unidade administrativa.

### <a name="administrative-unit-management"></a>Gerenciamento de unidade administrativa

Permissões |   MS Graph/PowerShell   | Portal do AD do Azure | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Criando e excluindo unidades administrativas   |    Suportado    |   Com suporte   |    Sem suporte
Adicionando e removendo membros da unidade administrativa individualmente    |   Suportado    |   Com suporte   |    Sem suporte
Adição e remoção em massa de membros da unidade administrativa usando o arquivo. csv   |    Sem suporte     |  Com suporte   |    Nenhum plano para dar suporte
Atribuindo administradores com escopo de unidade administrativa  |     Suportado    |   Com suporte    |   Sem suporte
Adicionando e removendo membros AU dinamicamente com base em atributos | Sem suporte | Sem suporte | Sem suporte

### <a name="user-management"></a>Gerenciamento de Usuários

Permissões |   MS Graph/PowerShell   | Portal do AD do Azure | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gerenciamento com escopo de unidade administrativa de propriedades do usuário, senhas, licenças   |    Suportado     |  Suportado   |   Suportado
bloqueio de escopo de unidade administrativa e desbloqueio de entradas de usuário    |   Suportado   |    Suportado   |    Suportado
gerenciamento no escopo da unidade administrativa de credenciais MFA do usuário   |    Suportado   |   Com suporte   |   Sem suporte

### <a name="group-management"></a>Gerenciamento de grupos

Permissões |   MS Graph/PowerShell   | Portal do AD do Azure | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gerenciamento com escopo de unidade administrativa de propriedades e membros de grupo     |  Suportado   |    Com suporte    |  Sem suporte
gerenciamento com escopo de unidade administrativa de licenciamento de grupo   |    Suportado  |    Com suporte   |   Sem suporte

> [!NOTE]
>
> Os administradores com um escopo de unidade administrativa não podem gerenciar regras de associação de grupo dinâmico.

As unidades administrativas aplicam o escopo somente às permissões de gerenciamento. Eles não impedem que membros ou administradores usem suas [permissões de usuário padrão](../fundamentals/users-default-permissions.md) para procurar outros usuários, grupos ou recursos fora da unidade administrativa. No portal do Office 365, os usuários fora de uma unidade administrativa do administrador com escopo são filtrados, mas você pode procurar outros usuários no portal do AD do Azure, no PowerShell e em outros serviços da Microsoft.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciando a AUs](roles-admin-units-manage.md)
- [Gerenciar usuários na AUs](roles-admin-units-add-manage-users.md)
- [Gerenciar grupos na AUs](roles-admin-units-add-manage-groups.md)
- [Atribuir funções com escopo a uma AU](roles-admin-units-assign-roles.md)