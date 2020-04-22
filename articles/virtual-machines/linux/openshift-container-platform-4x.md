---
title: Implantar plataforma de contêineres OpenShift 4.x no Azure
description: Implantar plataforma de contêiner OpenShift 4.x no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759485"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implantar plataforma de contêineres OpenShift 4.x no Azure

A implantação do OCP (OpenShift Container Platform, plataforma de contêineres openshift) 4.2 agora é suportada no Azure através do modelo IPI (Installer-Provisioned Infrastructure, infra-estrutura provisionada com provisionamento do instalador).  A página de aterrissagem para tentar o OpenShift 4 é [try.openshift.com](https://try.openshift.com/). Para instalar o OCP 4.2 no Azure, visite a página [do Red Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  As credenciais da Red Hat são necessárias para acessar este site.


## <a name="notes"></a>Observações 

 - Um Azure Active Directory (AAD) Service Principal (SP) é necessário para instalar e executar o OCP 4.x no Azure
     - A SP deve receber a permissão de API do **Application.ReadWrite.OwnedBy** para o Gráfico de Diretório Ativo do Azure
     - Um administrador de inquilinos AAD deve conceder consentimento de administração para que esta permissão de API entre em vigor
     - A SP deve ser concedida funções **de Administrador de Acesso ao** **Usuário** e à assinatura
 - O modelo de instalação do OCP 4.x é diferente do 3.x e não há modelos do Azure Resource Manager disponíveis para implantação do OCP 4.x no Azure
 - Se os problemas forem encontrados durante o processo de instalação, entre em contato com a empresa apropriada (Microsoft ou Red Hat)

| Descrição do problema | Ponto de Contato |
|-------------------|---------------|
| Problemas específicos do Azure (AAD, SP, Assinatura Do Azure, etc.)                              | Microsoft |
| Problemas específicos do OpenShift (falhas/erros de instalação, assinatura red hat, etc.) |  Red Hat  |




## <a name="next-steps"></a>Próximas etapas

- [Introdução ao OpenShift Container Platform](https://docs.openshift.com)
