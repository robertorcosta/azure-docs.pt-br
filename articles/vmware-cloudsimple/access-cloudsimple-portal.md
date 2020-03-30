---
title: Acesse a solução Azure VMware pela CloudSimple - Portal
description: Descreve como acessar a solução VMware pelo portal CloudSimple do portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244726"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Acesse o portal VMware solution by CloudSimple do portal Azure

O logon único é suportado para acesso ao portal CloudSimple. Depois de entrar no portal Azure, você pode acessar o portal CloudSimple sem fazer login novamente. A primeira vez que você acessa o portal CloudSimple, você é solicitado a autorizar o aplicativo [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application)  A autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os usuários com funções **de Proprietário** e **Contribuinte** incorporados podem acessar o portal CloudSimple.  As funções devem ser configuradas no grupo de recursos onde o serviço CloudSimple é implantado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre como verificar sua função, consulte Exibir artigo [de atribuições de função.](https://docs.microsoft.com/azure/role-based-access-control/check-access) Somente usuários com funções de **Proprietário** e **Contribuinte** incorporados podem acessar o portal CloudSimple.  As funções devem ser configuradas na assinatura.  Para obter mais informações sobre como verificar sua função, consulte Exibir artigo [de atribuições de função.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Se você estiver usando funções personalizadas, a ```Actions```função deve ter qualquer uma das seguintes operações.  Para obter mais informações sobre funções personalizadas, consulte [Funções personalizadas para os recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Se alguma das operações ```NotActions```faz parte, o usuário não pode acessar o portal CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **Serviços CloudSimple**.

3. Selecione o serviço CloudSimple no qual deseja criar sua Nuvem Privada.

4. Na página **Visão Geral,** clique **em Ir para o portal CloudSimple**.  Se você estiver acessando o portal CloudSimple do portal Azure pela primeira vez, você será solicitado a autorizar o aplicativo [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application) 

    ![Inicie o portal CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se você selecionar uma operação de Nuvem Privada (como criar ou expandir uma Nuvem Privada) diretamente do portal Azure, o portal CloudSimple será aberto para a página indicada.

No portal CloudSimple, selecione **Home** no menu lateral para exibir informações de resumo sobre suas Nuvens Privadas. Os recursos e a capacidade de suas Nuvens Privadas são mostrados, juntamente com alertas e tarefas que requerem atenção. Para tarefas comuns, clique nos ícones nomeados na parte superior da página.

![Página inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentimento para o aplicativo de autorização de serviço suscetido ao CloudSimple

O lançamento do portal CloudSimple do portal Azure pela primeira vez requer seu consentimento para o aplicativo CloudSimple Service Authorization.  Selecione **Aceitar** para conceder permissões solicitadas e acessar o portal CloudSimple.

![Consentimento para autorização de serviço sinuosa - administradores](media/cloudsimple-azure-consent.png)

Se você tem privilégio de administrador global, você pode consentir com sua organização.  Selecione **Consentir em nome de sua organização**.

![Consentimento para autorização de serviço cloudsimple - admin global](media/cloudsimple-azure-consent-global-admin.png)

Se suas permissões não permitirem acesso ao portal CloudSimple, entre em contato com o administrador global do seu inquilino para conceder as permissões necessárias.  Um administrador global pode consentir em nome de sua organização.

![Consentimento para autorização de serviço cloudsimple - requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
