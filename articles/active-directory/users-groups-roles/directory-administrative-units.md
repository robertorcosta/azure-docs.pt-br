---
title: Gestão de unidades administrativas (preview) - Azure AD | Microsoft Docs
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406451"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gerenciamento de unidades administrativas no Azure Active Directory (pré-visualização)

Este artigo descreve unidades administrativas no Azure Active Directory (Azure AD). Uma unidade administrativa é um recurso Azure AD que pode ser um contêiner para outros recursos azure AD. Nesta versão de pré-visualização, uma unidade administrativa pode conter apenas usuários e grupos.

As unidades administrativas permitem que você conceda permissões administrativas restritas a um departamento, região ou outro segmento de sua organização que você define. Você pode usar unidades administrativas para delegar permissões a administradores regionais ou para definir políticas em um nível granular. Por exemplo, um admin de conta de usuário poderia atualizar informações de perfil, redefinir senhas e atribuir licenças para usuários apenas em sua unidade administrativa.

 Por exemplo, delegar aos especialistas de suporte regional a função [de Administrador do Helpdesk](directory-assign-admin-roles.md#helpdesk-administrator) restrita ao gerenciamento apenas dos usuários da região que eles suportam.

## <a name="deployment-scenario"></a>Cenário de implantação

Restringir o escopo administrativo usando unidades administrativas pode ser útil em organizações que são compostas por divisões independentes de qualquer tipo. Considere o exemplo de uma grande universidade que é composta por muitas escolas autônomas (School of Business, School of Engineering, e assim por diante) que cada uma tem uma equipe de administradores de TI que controlam o acesso, gerenciam usuários e definem políticas para sua escola. Um administrador central poderia:

- Crie uma função com permissões administrativas sobre apenas usuários do Azure AD na unidade administrativa da escola de negócios
- Criar uma unidade administrativa para a Escola de Negócios
- Preencha a unidade de administração com apenas os alunos e funcionários da escola de negócios
- Adicione a equipe de TI da escola de negócios à função com seu escopo

## <a name="license-requirements"></a>Requisitos de licença

O uso de unidades administrativas requer uma licença Azure Active Directory Premium para cada administrador de unidade administrativa e licenças Azure Active Directory Free para membros da unidade administrativa. Para obter mais informações, consulte [Getting started with Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gerenciar unidades administrativas

Nesta versão de pré-visualização, você pode gerenciar unidades administrativas usando o portal Azure, cmdlets e scripts do PowerShell ou o Microsoft Graph. Você pode consultar nossa documentação para obter detalhes:

- [Criar, remover, preencher e adicionar funções às unidades administrativas](roles-admin-units-manage.md): Complete procedimentos de como fazer
- [Trabalhando com Unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): Como trabalhar com unidades administrativas usando o PowerShell
- [Suporte ao Gráfico da Unidade Administrativa](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): Documentação detalhada no Microsoft Graph para unidades administrativas.

### <a name="planning-your-administrative-units"></a>Planejamento de suas unidades administrativas

Unidades administrativas podem ser usadas para agrupar logicamente os recursos do Azure AD. Por exemplo, para uma organização cujo departamento de TI está espalhado globalmente, pode fazer sentido criar unidades administrativas que definam esses limites geográficos. Em outro cenário em que uma organização multinacional tenha diferentes "suborganizações", que são semi-autônomas em operações, cada suborganização pode ser representada por uma unidade administrativa.

Os critérios sobre os quais as unidades administrativas são criadas serão orientados pelos requisitos únicos de uma organização. As Unidades Administrativas são uma forma comum de definir estrutura em todos os serviços M365. Recomendamos que você prepare suas unidades administrativas com seu uso em todos os serviços M365 em mente. Você pode obter o valor máximo das unidades administrativas quando você pode associar recursos comuns em M365 sob uma unidade administrativa.

Você pode esperar que a criação de unidades administrativas na organização passe pelas seguintes etapas:

1. Adoção Inicial: Sua organização começará a criar unidades administrativas com base em critérios iniciais e o número de unidades administrativas aumentará à medida que os critérios forem refinados.
1. Poda: Uma vez que os critérios estejam bem definidos, as unidades administrativas que não forem mais necessárias serão suprimidas.
1. Estabilização: Sua estrutura organizacional está bem definida e o número de unidades administrativas não vai mudar significativamente em curtos períodos.

## <a name="currently-supported-scenarios"></a>Cenários atualmente suportados

Os administradores globais ou os administradores de funções privilegiadas podem usar o portal Azure AD para criar unidades administrativas, adicionar usuários como membros de unidades administrativas e, em seguida, atribuir a equipe de TI a funções administrativas de administrador com escopo unitário. Os administradores administrativos com escopo unitário podem, então, utilizar o portal Office 365 para gestão básica dos usuários em suas unidades administrativas.

Além disso, grupos podem ser adicionados como membros da unidade administrativa, e um administrador de grupo com escopo unitário pode gerenciá-los usando o PowerShell, o Microsoft Graph e o portal Azure AD.

A tabela abaixo descreve o suporte atual para cenários de unidades administrativas.

### <a name="administrative-unit-management"></a>Gestão administrativa da unidade

Permissões |   Gráfico/PowerShell ms   | Portal Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Criação e exclusão de unidades administrativas   |    Com suporte    |   Com suporte   |    Sem suporte
Adicionando e removendo membros da unidade administrativa individualmente    |   Com suporte    |   Com suporte   |    Sem suporte
Agregação e remoção de membros da unidade administrativa usando arquivo .csv   |    Sem suporte     |  Com suporte   |    Nenhum plano de apoio
Atribuindo administradores administrativos com escopo unitário  |     Com suporte    |   Com suporte    |   Sem suporte
Adicionando e removendo membros da UA dinamicamente com base em atributos | Sem suporte | Sem suporte | Sem suporte

### <a name="user-management"></a>Gerenciamento de Usuários

Permissões |   Gráfico/PowerShell ms   | Portal Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gerenciamento de unidades administrativas de propriedades do usuário, senhas, licenças   |    Com suporte     |  Com suporte   |   Com suporte
bloqueio e desbloqueio de unidades administrativas de logins de usuários    |   Com suporte   |    Com suporte   |    Com suporte
gerenciamento de unidade administrativa de credenciais de MFA do usuário   |    Com suporte   |   Com suporte   |   Sem suporte

### <a name="group-management"></a>Gerenciamento de grupos

Permissões |   Gráfico/PowerShell ms   | Portal Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestão de unidades administrativas de propriedades e membros do grupo     |  Com suporte   |    Com suporte    |  Sem suporte
gestão de unidades administrativas de licenciamento de grupo   |    Com suporte  |    Com suporte   |   Sem suporte

> [!NOTE]
>
> Os administradores com um escopo de unidade administrativa não podem gerenciar regras dinâmicas de membros de grupo.

As unidades administrativas aplicam escopo apenas às permissões de gestão. Eles não impedem que membros ou administradores usem suas [permissões de usuário padrão](../fundamentals/users-default-permissions.md) para navegar em outros usuários, grupos ou recursos fora da unidade administrativa. No portal Office 365, os usuários fora das unidades administrativas de um administrador escopo são filtrados, mas você pode navegar por outros usuários no portal Azure AD, PowerShell e outros serviços da Microsoft.

## <a name="next-steps"></a>Próximas etapas

- [Gestão da AUs](roles-admin-units-manage.md)
- [Gerenciar usuários em UA](roles-admin-units-add-manage-users.md)
- [Gerenciar grupos em UA](roles-admin-units-add-manage-groups.md)
- [Atribuir funções escopo a um UA](roles-admin-units-assign-roles.md)