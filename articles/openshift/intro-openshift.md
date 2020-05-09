---
title: Introdução ao Red Hat OpenShift no Azure
description: Aprenda os recursos e benefícios do Red Hat OpenShift no Azure na Microsoft para implantar e gerenciar aplicativos baseados em contêiner.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628513"
---
# <a name="azure-red-hat-openshift"></a>Red Hat OpenShift no Azure

O serviço *Red Hat OpenShift no Azure* da Microsoft permite que você implante clusters [OpenShift](https://www.openshift.com/) totalmente gerenciados.

O Red Hat OpenShift no Azure amplia o [Kubernetes](https://kubernetes.io/). A execução de contêineres em produção com o Kubernetes exige ferramentas e recursos adicionais. Isso geralmente inclui a necessidade de fazer malabarismos com registros de imagem, gerenciamento de armazenamento, soluções de rede e ferramentas de registro em log e monitoramento – tudo isso deve ter controle de versão e ser testado em conjunto. Criar aplicativos baseados em contêiner requer mais trabalho de integração com o middleware, estruturas, bancos de dados e ferramentas de CI/CD. O Red Hat OpenShift no Azure combina tudo isso em uma única plataforma, facilitando as operações das equipes de TI enquanto fornece o aplicativo que elas necessitam para trabalhar.

O Red Hat OpenShift no Azure é desenvolvido, operado e compatível com Red Hat e Microsoft para fornecer uma experiência de suporte integrado em conjunto. Não existem máquinas virtuais para operar e nenhuma aplicação de patch é necessária. Os nós mestre, de infraestrutura e de aplicativo tem aplicação de patch, atualização e monitoramento em seu nome pela Red Hat e pela Microsoft. Os clusters do Red Hat OpenShift no Azure são implantados em sua assinatura do Azure e são incluídos na sua fatura do Azure.

Você pode escolher suas próprias soluções de registro, rede, armazenamento e CI/CD, ou usar as soluções internas para gerenciamento automatizado de código fonte, criações de contêiner e aplicativo, implantações, dimensionamento, gerenciamento de integridade e muito mais. O Red Hat OpenShift no Azure fornece uma experiência de logon integrada por meio do Azure Active Directory.

Para começar, conclua o tutorial [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md).

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitoramento

Para gerenciamento e segurança aprimorados, o Red Hat OpenShift no Azure permite integrar com o AAD (Azure Active Directory) e usar RBAC (controle de acesso baseado em função) do Kubernetes. Você também pode monitorar a integridade do cluster e dos recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Os nós do Red Hat OpenShift no Azure são executados em máquinas virtuais do Azure. Você pode conectar o armazenamento a nós e pods, além de atualizar componentes do cluster.

## <a name="next-steps"></a>Próximas etapas

Conheça os pré-requisitos do Red Hat OpenShift no Azure:

> [!div class="nextstepaction"]
> [Configurar seu ambiente de desenvolvimento](tutorial-create-cluster.md)
