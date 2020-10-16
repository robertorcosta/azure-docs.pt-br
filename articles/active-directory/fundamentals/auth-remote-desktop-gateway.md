---
title: Área de Trabalho Remota serviços de gateway com Azure Active Directory
description: Diretrizes arquitetônicas sobre como atingir esse padrão de autenticação
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
ms.openlocfilehash: ff099d41970ad4d78d5c6035a60f448f29ab24b1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114053"
---
# <a name="remote-desktop-gateway-services"></a>Serviços de gateway Área de Trabalho Remota

Uma implantação de RDS (Serviços de Área de Trabalho Remota padrão) inclui vários [serviços de função área de trabalho remota](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) em execução no Windows Server. A implantação de RDS com o proxy de aplicativo Azure Active Directory (Azure AD) tem uma conexão de saída permanente do servidor que executa o serviço do conector. Outras implantações deixam conexões de entrada abertas por meio de um balanceador de carga. Esse padrão de autenticação permite que você ofereça mais tipos de aplicativos Publicando aplicativos locais por meio de Serviços de Área de Trabalho Remota. Ele também reduz a superfície de ataque de sua implantação usando o Azure Proxy de Aplicativo do AD.

## <a name="use-when"></a>Usar quando

Você precisa fornecer acesso remoto e proteger sua implantação de Serviços de Área de Trabalho Remota com pré-autenticação.

![diagrama arquitetônico](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa RDS servido pelo proxy de aplicativo.

* **Navegador da Web**: o componente com o qual o usuário interage para acessar a URL externa do aplicativo.

* **AD do Azure**: autentica o usuário. 

* **Serviço de proxy de aplicativo**: atua como proxy reverso para encaminhar a solicitação do usuário para o RDS. O proxy de aplicativo também pode impor qualquer política de acesso condicional. 

* **Serviços de área de trabalho remota**: atua como uma plataforma para aplicativos virtualizados individuais, fornecendo acesso seguro de área de trabalho móvel e remota e fornecendo aos usuários finais a capacidade de executar seus aplicativos e desktops da nuvem. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Implementar serviços de gateway Área de Trabalho Remota com o Azure AD

* [Publicar área de trabalho remota com o Azure Proxy de Aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
