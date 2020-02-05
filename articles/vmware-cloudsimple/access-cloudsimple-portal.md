---
title: Acessar soluções VMware do Azure (AVS)-Portal
description: Descreve como acessar as soluções do Azure VMware (AVS) no portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015943"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Acessar soluções VMware do Azure (AVS) no portal do Azure

O logon único tem suporte para acesso ao portal da AVS. Depois de entrar no portal do Azure, você poderá acessar o portal da AVS sem entrar novamente. Na primeira vez que você acessar o portal da AVS, você será solicitado a autorizar o aplicativo de [autorização do serviço AVS](#consent-to-avs-service-authorization-application) . A autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os usuários com funções de **colaborador** e **proprietário** interno podem acessar o portal da AVS. As funções devem ser configuradas no grupo de recursos em que o serviço AVS é implantado. As funções também podem ser configuradas no objeto de serviço do AVS. Para obter mais informações sobre como verificar sua função, consulte o artigo [exibir atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Somente os usuários com funções de **proprietário** e **colaborador** internas podem acessar o portal da AVS. As funções devem ser configuradas na assinatura. Para obter mais informações sobre como verificar sua função, consulte o artigo [exibir atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Se você estiver usando funções personalizadas, a função deverá ter qualquer uma das operações a seguir em ```Actions```.  Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Se qualquer uma das operações fizer parte do ```NotActions```, o usuário não poderá acessar o portal da AVS. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Acessar o portal da AVS

1. Selecione **Todos os serviços**.

2. Procure **serviços de sincronização automática**.

3. Selecione o serviço AVS no qual você deseja criar sua nuvem privada.

4. Na página **visão geral** , clique em **ir para o portal da AVS**. Se estiver acessando o portal da AVS da portal do Azure pela primeira vez, você será solicitado a autorizar o aplicativo de [autorização do serviço AVS](#consent-to-avs-service-authorization-application) . 

    ![Iniciar o portal de sincronização automática](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se você selecionar uma operação de nuvem privada (como criar ou expandir uma nuvem privada) diretamente da portal do Azure, o portal da AVS será aberto na página indicada.

No portal da AVS, selecione **início** no menu lateral para exibir informações de resumo sobre sua nuvem privada de AVS. Os recursos e a capacidade da sua nuvem privada de AVS são mostrados, juntamente com alertas e tarefas que exigem atenção. Para tarefas comuns, clique nos ícones nomeados na parte superior da página.

![Página inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Consentimento para o aplicativo de autorização de serviço AVS

Iniciar o portal de AVS da portal do Azure pela primeira vez requer seu consentimento para o aplicativo de autorização do serviço AVS. Selecione **aceitar** para conceder permissões solicitadas e acessar o portal da AVS.

![Consentimento para a autorização do serviço AVS-administradores](media/cloudsimple-azure-consent.png)

Se você tiver um privilégio de administrador global, poderá consentir para sua organização. Selecione **consentimento em nome da sua organização**.

![Consentimento para a autorização do serviço AVS-administrador global](media/cloudsimple-azure-consent-global-admin.png)

Se suas permissões não permitirem acesso ao portal da AVS, entre em contato com o administrador global do seu locatário para conceder as permissões necessárias. Um administrador global pode consentir em nome de sua organização.

![Consentimento para a autorização do serviço AVS-requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Próximos passos

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
