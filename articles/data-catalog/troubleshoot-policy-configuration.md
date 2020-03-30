---
title: Como solucionar problemas do Catálogo de Dados do Azure
description: Este artigo descreve preocupações comuns de solução de problemas para os recursos do Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879549"
---
# <a name="troubleshooting-azure-data-catalog"></a>Solução de problemas do Catálogo de Dados do Azure

Este artigo descreve preocupações comuns de solução de problemas para os recursos do Catálogo de Dados do Azure. 

## <a name="functionality-limitations"></a>Limitações de funcionalidade

Ao usar o Azure Data Catalog, a seguinte funcionalidade é limitada:

- As contas com o tipo **De Função convidada** não são suportadas. Você não pode adicionar contas de hóspedes como usuários do Azure Data Catalog, e os usuários convidados não podem usar o portal em [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).

- A criação de recursos do Catálogo de Dados do Azure usando modelos do Azure Resource Manager ou comandos Azure PowerShell não é suportada.

- O recurso Azure Data Catalog não pode ser movido entre os inquilinos do Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configuração de política do Azure Active Directory

Pode haver uma situação em que você pode entrar no portal do Catálogo de Dados do Azure, mas ao tentar entrar na ferramenta de registro de fonte de dados, poderá receber uma mensagem de erro impedindo a entrada. Esse erro pode ocorrer quando você está na rede da empresa ou quando você está se conectando de fora da rede da empresa.

A ferramenta de registro usa a *Autenticação de Formulários* para validar logons de usuário no Azure Active Directory. Para entrar com êxito, um administrador do Azure Active Directory deve habilitar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, você pode habilitar a autenticação separadamente para conexões de intranet e extranet, conforme mostrado na imagem a seguir. Erros de login podem ocorrer se a autenticação de formulários não estiver ativada para a rede a partir da qual você está se conectando.

 ![Política de Autenticação Global do AzureActive Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para saber mais, confira [Configurando políticas de autenticação](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Próximas etapas

* [Criar um Catálogo de Dados do Azure](data-catalog-get-started.md)
