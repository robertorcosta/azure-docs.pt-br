---
title: Visão geral do modo de dispositivo compartilhado
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o modo dispositivo compartilhado para permitir o compartilhamento de dispositivos para seus Trabalhadores de Primeira Linha.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550234"
---
# <a name="overview-of-shared-device-mode"></a>Visão geral do modo dispositivo compartilhado

O modo de dispositivo compartilhado é um recurso do Azure Active Directory que permite criar aplicativos que suportam trabalhadores de linha de primeira linha e habilitar o modo de dispositivo compartilhado nos dispositivos implantados a eles.

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>O que são trabalhadores de primeira linha?

Os trabalhadores da primeira linha são funcionários de varejo, agentes de manutenção e de campo, pessoal médico e outros usuários que não se sentam na frente de um computador ou usam e-mail corporativo para colaboração. As seções a seguir introduzem os aspectos e desafios do suporte ao Firstline Workers, seguido de uma introdução aos recursos fornecidos pela Microsoft que permitem o seu aplicativo para uso pelos Trabalhadores firstline de uma organização.

### <a name="challenges-of-supporting-firstline-workers"></a>Desafios de apoiar trabalhadores de primeira linha

A habilitação dos fluxos de trabalho do Firstline Worker inclui desafios que normalmente não são apresentados por trabalhadores de informações típicos. Tais desafios podem incluir alta taxa de rotatividade e menos familiaridade com as principais ferramentas de produtividade de uma organização. Para capacitar seus Trabalhadores de Primeira Linha, as organizações estão adotando diferentes estratégias. Alguns estão adotando uma estratégia de trazer seu próprio dispositivo (BYOD) na qual seus funcionários usam aplicativos de negócios em seu telefone pessoal, enquanto outros fornecem a seus funcionários dispositivos compartilhados, como iPads ou tablets Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Suporte a vários usuários em dispositivos projetados para um usuário

Como os dispositivos móveis que executam iOS ou Android foram projetados para usuários únicos, a maioria dos aplicativos otimiza sua experiência para uso por um único usuário. Parte dessa experiência otimizada significa permitir o login único em aplicativos e manter os usuários cadastrados em seu dispositivo. Quando um usuário remove sua conta de um aplicativo, o aplicativo normalmente não o considera um evento relacionado à segurança. Muitos aplicativos até mantêm as credenciais de um usuário por perto para fazer login rápido. Você pode até ter experimentado isso quando excluiu um aplicativo do seu dispositivo móvel e depois o reinstalou, apenas para descobrir que você ainda está conectado.

### <a name="global-sign-in-and-sign-out-sso"></a>Entrada e login global (SSO)

Para permitir que os funcionários de uma organização usem seus aplicativos em um pool de dispositivos compartilhados por esses funcionários, os desenvolvedores precisam habilitar a experiência oposta. Os funcionários devem ser capazes de escolher um dispositivo da piscina e realizar um único gesto para "torná-lo deles" durante a duração de seu turno. No final de seu turno, eles devem ser capazes de realizar outro gesto para sair globalmente no dispositivo, com todas as suas informações pessoais e da empresa removidas para que eles possam devolvê-lo ao pool de dispositivos. Além disso, se um funcionário se esquece de sair, o dispositivo deve ser automaticamente assinado no final do seu turno e/ou após um período de inatividade.

O Azure Active Directory habilita esses cenários com um recurso chamado **modo de dispositivo compartilhado**.

## <a name="introducing-shared-device-mode"></a>Introduzindo o modo dispositivo compartilhado

Como mencionado, o modo dispositivo compartilhado é um recurso do Azure Active Directory que permite:

* Criar aplicativos que suportem trabalhadores de primeira linha
* Implantar dispositivos para o Firstline Workers e ativar o modo de dispositivo compartilhado

### <a name="build-applications-that-support-firstline-workers"></a>Criar aplicativos que suportem trabalhadores de primeira linha

Você pode suportar os Trabalhadores de Primeira Linha em seus aplicativos usando o aplicativo Microsoft Authentication Library (MSAL) e [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para habilitar um estado de dispositivo chamado *modo dispositivo compartilhado*. Quando um dispositivo está no modo de dispositivo compartilhado, a Microsoft fornece ao seu aplicativo informações para permitir que ele modifique seu comportamento com base no estado do usuário no dispositivo, protegendo os dados do usuário.

Os recursos suportados são:

* **Faça login em um dispositivo de usuário em todo** o dispositivo através de qualquer aplicativo suportado.
* **Assine um dispositivo de usuário em todo** o dispositivo através de qualquer aplicativo suportado.
* **Consulte o estado do dispositivo** para determinar se seu aplicativo está em um dispositivo que está no modo dispositivo compartilhado.
* **Consulte o estado do dispositivo do usuário** no dispositivo para determinar se alguma coisa mudou desde a última vez que seu aplicativo foi usado.

O suporte ao modo de dispositivo compartilhado deve ser considerado um aprimoramento de segurança e atualização de recursos para seu aplicativo, e pode ajudar a aumentar sua adoção em ambientes altamente regulamentados, como saúde e finanças.

Seus usuários dependem de você para garantir que seus dados não sejam vazados para outro usuário. O Modo Dispositivo compartilhado fornece sinais úteis para indicar ao seu aplicativo que uma alteração que você deve gerenciar ocorreu. Seu aplicativo é responsável por verificar o estado do usuário no dispositivo toda vez que o aplicativo é usado, limpando os dados do usuário anterior. Isso inclui se ele for recarregado do plano de fundo em multitarefa. Em uma alteração de usuário, você deve garantir que os dados do usuário anterior sejam limpos e que todos os dados armazenados em cache no aplicativo sejam removidos. Recomendamos que você sempre execute um processo completo de revisão de segurança depois de adicionar o recurso de modo de dispositivo compartilhado ao seu aplicativo.

Para obter detalhes sobre como modificar seus aplicativos para suportar o modo de dispositivo compartilhado, consulte a seção [Próximos passos](#next-steps) no final deste artigo.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Implantar dispositivos para o Firstline Workers e ativar o modo de dispositivo compartilhado

Uma vez que seus aplicativos suportam o modo dispositivo compartilhado e incluem as alterações de dados e segurança necessárias, você pode anunciá-los como utilizáveis pelos Trabalhadores da Primeira Linha.

Os administradores de dispositivos de uma organização são capazes de implantar seus dispositivos e aplicativos em suas lojas e locais de trabalho através de uma solução de gerenciamento de dispositivos móveis (MDM) como o Microsoft Intune. Parte do processo de provisionamento é marcar o dispositivo como um *dispositivo compartilhado*. Os administradores configuram o modo de dispositivo compartilhado, implantando o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e definindo o modo de dispositivo compartilhado através de parâmetros de configuração. Após a execução dessas etapas, todos os aplicativos que suportam o modo dispositivo compartilhado usarão o aplicativo Microsoft Authenticator para gerenciar seu estado de usuário e fornecer recursos de segurança para o dispositivo e organização.

## <a name="next-steps"></a>Próximas etapas

Nós suportamos plataformas iOS e Android para o modo de dispositivo compartilhado. Revise a documentação abaixo para que sua plataforma comece a apoiar os Trabalhadores da Primeira Linha em seus aplicativos.

* [Suporte ao modo de dispositivo compartilhado para iOS](msal-ios-shared-devices.md)
* [Suporte ao modo de dispositivo compartilhado para Android](msal-android-shared-devices.md)
