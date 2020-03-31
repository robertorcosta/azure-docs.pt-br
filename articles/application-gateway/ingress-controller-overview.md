---
title: O que é o Azure Application Gateway Ingress Controller?
description: Este artigo fornece uma introdução ao que é o Application Gateway Ingress Controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335812"
---
# <a name="what-is-application-gateway-ingress-controller"></a>O que é o Controlador de Entrada do Gateway de Aplicativo?
O Application Gateway Ingress Controller (AGIC) é um aplicativo Kubernetes, o que torna possível que os clientes [do Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) aproveitem o [balanceador](https://azure.microsoft.com/services/application-gateway/) de carga nativo do Azure Gateway L7 para expor o software em nuvem à Internet. A AGIC monitora o cluster Kubernetes em que está hospedado e atualiza continuamente um Gateway de aplicativo, para que os serviços selecionados sejam expostos à Internet.

O Controlador Ingress é executado em seu próprio pod no AKS do cliente. AGIC monitora um subconjunto de Recursos Kubernetes para alterações. O estado do cluster AKS é traduzido para configuração específica do Application Gateway e aplicado ao [ARM (Azure Resource Manager, gerente de recursos do Azure).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="benefits-of-application-gateway-ingress-controller"></a>Benefícios do Controlador de Entrada do Gateway de Aplicativo
O AGIC permite que sua implantação controle vários clusters AKS com um único controlador de entrada de gateway de aplicativo. O AGIC também ajuda a eliminar a necessidade de ter outro IP balanceador/público de carga em frente ao cluster AKS e evita vários saltos em seu datapath antes que as solicitações cheguem ao cluster AKS. O Application Gateway conversa com pods usando seu IP privado diretamente e não requer serviços NodePort ou KubeProxy. Isso também traz melhor desempenho para suas implantações.

O Ingress Controller é suportado exclusivamente por Standard_v2 e WAF_v2 SKUs, o que também traz benefícios de autodimensionamento. O Application Gateway pode reagir em resposta a um aumento ou diminuição da carga de tráfego e escala em conformidade, sem consumir nenhum recurso do seu cluster AKS.

O uso do Application Gateway, além do AGIC, também ajuda a proteger seu cluster AKS, fornecendo a diretiva TLS e a funcionalidade WAF (Web Application Firewall).

![Gateway de aplicativo Azure + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

O AGIC é configurado através do recurso Kubernetes [Ingress,](https://kubernetes.io/docs/user-guide/ingress/)juntamente com Serviços e Implantações/Pods. Ele fornece uma série de recursos, aproveitando o balanceador de carga nativo do Gateway L7 do Azure. A saber:
  - Roteamento de URL
  - Afinidade baseada em cookie
  - Rescisão tls
  - TLS de ponta a ponta
  - Suporte para sites públicos, privados e híbridos
  - Firewall integrado de aplicativos web

O AGIC é capaz de lidar com vários namespaces e tem Targets proibidos, o que significa que a AGIC pode configurar o Gateway de aplicativo especificamente para clusters AKS sem afetar outros backends existentes. 

## <a name="next-steps"></a>Próximas etapas

- [**Implantação greenfield**](ingress-controller-install-new.md): Instruções sobre a instalação de AGIC, AKS e Gateway de aplicativo em infra-estrutura de ardósia em branco.
- [**Implantação brownfield**](ingress-controller-install-existing.md): instale o AGIC em um AKS e o Gateway de aplicativo existentes.

