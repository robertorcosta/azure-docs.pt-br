---
title: Solucionando problemas de erros comuns de autenticação | Azure Marketplace
description: Fornece assistência com erros de autenticação comuns ao usar as APIs do Portal do Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: bfcee6a6d9a8448315d59009ea8a6ef62fa71da0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535987"
---
# <a name="troubleshooting-common-authentication-errors"></a>Solução de problemas de erros de autenticação comuns

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Este artigo fornece assistência para erros de autenticação comuns ao usar as APIs do Portal do Cloud Partner.

## <a name="unauthorized-error"></a>Erro não autorizado

Se você constantemente receber erros `401 unauthorized`, verifique se há um token de acesso válido.  Se ainda não fez isso, crie um aplicativo básico do Azure AD (Azure Active Directory) e uma entidade de serviço, conforme descrito em [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md). Em seguida, use o aplicativo ou uma solicitação HTTP POST simples para verificar o acesso.  Você incluirá a ID do Locatário, ID do Aplicativo, ID de Objeto e a chave secreta para obter o token de acesso, conforme mostrado na imagem a seguir:

![Solução de problemas de erro 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)

## <a name="forbidden-error"></a>Erro "Proibido"

Se você receber um erro `403 forbidden`, verifique se a entidade de serviço correta foi adicionada à conta do publicador no Portal do Cloud Partner.
Siga as etapas na página [Pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para adicionar a entidade de serviço ao portal.

Se a entidade de serviço correta foi adicionada, verifique todas as outras informações. Atente-se à ID de Objeto inserida no portal. Há duas IDs de Objeto na página de registro de aplicativo do Azure Active Directory e você deve usar a ID de Objeto local. É possível localizar o valor correto, acessando a página **Registros de aplicativo** do aplicativo e clicando no nome do aplicativo em **Aplicativo gerenciado no diretório local**. Isso irá direcioná-lo às propriedades locais do aplicativo onde será possível localizar a ID de Objeto correto na página **Propriedades**, conforme mostrado na figura a seguir. Além disso, certifique-se de usar a ID do publicador correto ao adicionar a entidade de serviço e fazer chamada à API.

![Solução de problemas de erro 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
