---
title: Implantar a plataforma de contêiner OpenShift 4. x no Azure
description: Implante a plataforma de contêiner OpenShift 4. x no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759485"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implantar a plataforma de contêiner OpenShift 4. x no Azure

Agora há suporte para a implantação da OpenShift (plataforma de contêiner) 4,2 no Azure por meio do modelo de IPI (infraestrutura provisionada) do instalador.  A página de aterrissagem para experimentar o OpenShift 4 é [try.openshift.com](https://try.openshift.com/). Para instalar o OCP 4,2 no Azure, visite a página do [Gerenciador de cluster do Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  As credenciais do Red Hat são necessárias para acessar este site.


## <a name="notes"></a>Anotações 

 - Um SP (entidade de serviço) Azure Active Directory (AAD) é necessário para instalar e executar o OCP 4. x no Azure
     - O SP deve receber a permissão de API de **Application. ReadWrite. OwnedBy** para o grafo Azure Active Directory
     - Um Administrador de Locatários do AAD deve conceder consentimento de administrador para que a permissão da API entre em vigor
     - O SP deve receber as funções **colaborador** e **administrador de acesso do usuário** para a assinatura
 - O modelo de instalação para OCP 4. x é diferente de 3. x e não há modelos de Azure Resource Manager disponíveis para implantar o OCP 4. x no Azure
 - Se forem encontrados problemas durante o processo de instalação, entre em contato com a empresa apropriada (Microsoft ou Red Hat)

| Descrição do problema | Ponto de contato |
|-------------------|---------------|
| Problemas específicos do Azure (AAD, SP, assinatura do Azure, etc.)                              | Microsoft |
| Problemas específicos do OpenShift (falhas de instalação/erros, assinatura do Red Hat, etc.) |  Red Hat  |




## <a name="next-steps"></a>Próximas etapas

- [Introdução ao OpenShift Container Platform](https://docs.openshift.com)
