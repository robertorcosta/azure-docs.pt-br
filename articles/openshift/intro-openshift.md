---
title: Introdução ao Red Hat OpenShift no Azure
description: Aprenda os recursos e benefícios do Red Hat OpenShift no Azure na Microsoft para implantar e gerenciar aplicativos baseados em contêiner.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276067"
---
# <a name="azure-red-hat-openshift"></a>Red Hat OpenShift no Azure

O serviço *Red Hat OpenShift no Azure* da Microsoft permite que você implante clusters [OpenShift](https://www.openshift.com/) totalmente gerenciados.

O Red Hat OpenShift no Azure amplia o [Kubernetes](https://kubernetes.io/). A execução de contêineres em produção com o Kubernetes requer ferramentas e recursos adicionais, como um registro de imagens, gerenciamento de armazenamento, soluções de rede e ferramentas de registro e monitoramento, e todos devem ser testados e ter controle de versão. Criar aplicativos baseados em contêiner requer mais trabalho de integração com o middleware, estruturas, bancos de dados e ferramentas de CI/CD. O Red Hat OpenShift no Azure combina tudo isso em uma única plataforma, facilitando as operações das equipes de TI enquanto fornece o aplicativo que elas necessitam para trabalhar.

O Red Hat OpenShift no Azure é desenvolvido, operado e compatível com Red Hat e Microsoft para fornecer uma experiência de suporte integrado em conjunto. Não existem máquinas virtuais para operar e nenhuma aplicação de patch é necessária. Os nós mestre, de infraestrutura e de aplicativo são corrigidos, atualizados e monitorados em seu nome pela Red Hat e Microsoft. Os clusters do Red Hat OpenShift no Azure são implantados em sua assinatura do Azure e são incluídos na sua fatura do Azure.

Você pode escolher suas próprias soluções de registro, rede, armazenamento e CI/CD, ou usar as soluções internas para gerenciamento automatizado de código fonte, criações de contêiner e aplicativo, implantações, dimensionamento, gerenciamento de integridade e muito mais. O Red Hat OpenShift no Azure fornece uma experiência de logon integrada por meio do Azure Active Directory.

Para começar, conclua o tutorial [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md).

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitoramento

Para gerenciamento e segurança aprimorados, o Red Hat OpenShift no Azure permite integrar com o AAD (Azure Active Directory) e usar RBAC (controle de acesso baseado em função) do Kubernetes. Você também pode monitorar a integridade do cluster e dos recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Os nós do Red Hat OpenShift no Azure são executados em máquinas virtuais do Azure. Você pode conectar o armazenamento a nós e pods, atualizar componentes do cluster e usar GPUs.

## <a name="virtual-networks-and-ingress"></a>Entrada e redes virtuais

Você pode [conectar um cluster do Red Hat OpenShift no Azure a uma rede virtual existente](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) por meio de emparelhamento. Nessa configuração, pods podem se conectar a outros serviços em uma rede virtual emparelhada.

Para saber detalhes, confira [Conectar a rede virtual do cluster a uma rede virtual existente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network).

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O serviço do Red Hat OpenShift no Azure foi certificado na CNCF como compatível com o Kubernetes.

## <a name="next-steps"></a>Próximas etapas

Conheça os pré-requisitos do Red Hat OpenShift no Azure:

> [!div class="nextstepaction"]
> [Configurar seu ambiente de desenvolvimento](howto-setup-environment.md)
