---
title: Solucionando problemas de erros comuns de autenticação | Azure Marketplace
description: Fornece assistência com erros de autenticação comuns ao usar as APIs do Portal do Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8c9bd6dbe54f797090c5f7405ad709db954f8fb9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115461"
---
# <a name="troubleshooting-common-authentication-errors"></a>Solução de problemas de erros de autenticação comuns

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue a funcionar após a migração para o Partner Center.

Este artigo fornece assistência para erros de autenticação comuns ao usar as APIs do Portal do Cloud Partner.

## <a name="unauthorized-error"></a>Erro não autorizado

Se você constantemente receber erros `401 unauthorized`, verifique se há um token de acesso válido.  Se ainda não fez isso, crie um aplicativo básico do Azure AD (Azure Active Directory) e uma entidade de serviço, conforme descrito em [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md). Em seguida, use o aplicativo ou uma solicitação HTTP POST simples para verificar o acesso.  Você incluirá a ID do Locatário, ID do Aplicativo, ID de Objeto e a chave secreta para obter o token de acesso, conforme mostrado na imagem a seguir:

![Solução de problemas de erro 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Erro "Proibido"

Se você receber um erro `403 forbidden`, verifique se a entidade de serviço correta foi adicionada à conta do publicador no Portal do Cloud Partner.
Siga as etapas na página [Pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para adicionar a entidade de serviço ao portal.

Se a entidade de serviço correta foi adicionada, verifique todas as outras informações. Atente-se à ID de Objeto inserida no portal. Há duas IDs de Objeto na página de registro de aplicativo do Azure Active Directory e você deve usar a ID de Objeto local. É possível localizar o valor correto, acessando a página **Registros de aplicativo** do aplicativo e clicando no nome do aplicativo em **Aplicativo gerenciado no diretório local**. Isso irá direcioná-lo às propriedades locais do aplicativo onde será possível localizar a ID de Objeto correto na página **Propriedades**, conforme mostrado na figura a seguir. Além disso, certifique-se de usar a ID do publicador correto ao adicionar a entidade de serviço e fazer chamada à API.

![Solução de problemas de erro 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
