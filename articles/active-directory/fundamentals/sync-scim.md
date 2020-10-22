---
title: Sincronização de SCIM com Azure Active Directory
description: Diretrizes arquitetônicas sobre como atingir esse padrão de sincronização
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1dda465dd675e0f5f519f86289df2621be0b9bb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367849"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Sincronização de SCIM com Azure Active Directory

O sistema para SCIM (gerenciamento de identidade entre domínios) é um protocolo padrão aberto para automatizar a troca de informações de identidade do usuário entre domínios de identidade e sistemas de ti. O SCIM garante que os funcionários adicionados ao sistema de gerenciamento de capital humano (HCM) automaticamente tenham contas criadas no Azure Active Directory (Azure AD) ou no Windows Server Active Directory. Os perfis e atributos de usuário são sincronizados entre os dois sistemas, atualizando a remoção de usuários com base no status do usuário ou na alteração da função.

SCIM é uma definição padronizada de dois pontos de extremidade: a '/user ' e um ponto de extremidade/groups. Ele usa verbos REST comuns para criar, atualizar e excluir objetos. Ele também usa um esquema predefinido para atributos comuns, como nome do grupo, nome de usuário, nome, sobrenome e email. Os aplicativos que oferecem uma API REST SCIM 2,0 podem reduzir ou eliminar o problema de trabalhar com APIs de gerenciamento de usuários ou produtos proprietários. Por exemplo, qualquer cliente compatível com SCIM pode fazer uma POSTAgem HTTP de um objeto JSON para o ponto de extremidade/Users para criar uma nova entrada de usuário. Em vez de precisar de uma API um pouco diferente para as mesmas ações básicas, os aplicativos que estão em conformidade com o padrão SCIM podem aproveitar instantaneamente os clientes, as ferramentas e o código preexistentes. 

## <a name="use-when"></a>Use quando: 

Você deseja provisionar automaticamente as informações do usuário de um sistema HCM para o Azure AD e o Windows Server Active Directory e, em seguida, para sistemas de destino, se necessário. 

![diagrama arquitetônico](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Componentes do sistema 

* **Sistema HCM**: aplicativos e tecnologias que permitem o processo de gerenciamento de capital humano e as práticas que dão suporte e automatizam processos de RH durante todo o ciclo de vida do funcionário. 

* **Serviço de provisionamento do Azure ad**: usa o protocolo scim 2,0 para provisionamento automático. O serviço se conecta ao ponto de extremidade do SCIM para o aplicativo e usa o esquema de objeto de usuário do SCIM e as APIs REST para automatizar o provisionamento e desprovisionamento de usuários e de grupos.  

* **Azure ad**: repositório de usuários usado para gerenciar o ciclo de vida de identidades e seus direitos. 

* **Sistema de destino**: aplicativo ou sistema que tem ponto de extremidade scim e funciona com o provisionamento do Azure ad para habilitar o provisionamento automático de usuários e grupos.  

## <a name="implement-scim-with-azure-ad"></a>Implementar o SCIM com o Azure AD 

* [Como o provisionamento funciona no Azure AD ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais no portal do Azure ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [Criar um ponto de extremidade SCIM e configurar o provisionamento de usuário com o Azure AD  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [Conformidade do protocolo SCIM 2,0 do serviço de provisionamento do Azure AD](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)

