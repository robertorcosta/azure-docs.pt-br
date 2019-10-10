---
title: Usando Ansible com o Azure
description: Introdução ao uso do Ansible para automatizar o provisionamento de nuvem, o gerenciamento de configurações e implantações de aplicativos.
keywords: ansible, azure, devops, visão geral, provisionamento de nuvem, gerenciamento de configuração, implantação de aplicativo, módulos ansible, manuais do ansible
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b6b9656edc43f38a4cb005be53c33bb98781679c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241506"
---
# <a name="using-ansible-with-azure"></a>Usando Ansible com o Azure

[Ansible](https://www.ansible.com) é um produto de código-fonte aberto que automatiza o provisionamento de nuvem, o gerenciamento de configurações e as implantações de aplicativos. Usando o Ansible você pode provisionar máquinas virtuais, contêineres e rede e completar infraestruturas de nuvem. Além disso, o Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente.

Este artigo fornece uma visão geral básica de alguns dos benefícios de usar o Ansible com o Azure.

## <a name="ansible-playbooks"></a>Manuais do Ansible

[Guias estratégicos do Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) permitem que você direcione o Ansible para configurar seu ambiente. Os guias estratégicos são codificados usando YAML para serem legíveis por humanos. A seção Tutoriais fornece muitos exemplos de uso de guias estratégicos para instalar e configurar recursos do Azure. 

## <a name="ansible-modules"></a>Módulos do Ansible

O Ansible inclui um conjunto de [módulos do Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) que são executados diretamente em hosts remotos ou através de [guias estratégicos](https://docs.ansible.com/ansible/latest/playbooks.html). Os usuários podem criar seus próprios módulos. OS módulos são usados para controlar recursos do sistema – como serviços, pacotes ou arquivos – ou executar comandos do sistema.

Para interagir com os serviços do Azure, o Ansible inclui um conjunto de [módulos de nuvem do Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Esses módulos permitem criar e orquestrar sua infraestrutura no Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrar cargas de trabalho existentes para o Azure

Depois que você tiver usado o Ansible para definir sua infraestrutura, você pode aplicar o manual do seu aplicativo permitindo que o Azure dimensione automaticamente seu ambiente, conforme necessário. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizar o aplicativo nativo de nuvem no Azure

O Ansible permite que você automatize aplicativos nativos de nuvem no Azure usando os microsserviços do Azure como o [Azure Functions](https://azure.microsoft.com//services/functions/) e o [Kubernetes no Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gerenciar implantações com inventário dinâmico

O Ansible oferece, por meio do recurso [inventário dinâmico](https://docs.ansible.com/ansible/intro_dynamic_inventory.html), a capacidade de fazer um inventário dos recursos do Azure. Você pode então marcar as implantações existentes do Azure e gerenciar as implantações marcadas através do Ansible.

## <a name="additional-azure-marketplace-options"></a>Opções adicionais do Azure Marketplace

A [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) é uma imagem do Azure Marketplace da Red Hat. 

O Ansible Tower é uma interface do usuário e painel do Ansible com os seguintes recursos:

* Permite definir o controle de acesso baseado em função, o agendamento de tarefas e o gerenciamento de inventário gráfico. 
* Inclui uma API REST e CLI para que você possa inserir o Tower em ferramentas e processos existentes. 
* Suporta a saída em tempo real de execuções do guia estratégico. 
* Criptografa as credenciais, tais como chaves do Azure e SSH, para que você pode delegar tarefas sem expor credenciais.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Matriz de módulo e versão Ansible do Azure

O Ansible inclui um conjunto de módulos para uso no provisionamento e configuração de recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços de rede e serviços de contêiner. A matriz do [Ansible](./ansible-matrix.md) lista os módulos do Ansible para o Azure e as versões do Ansible nas quais eles são fornecidos.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Implantar o modelo de solução Ansible para Azure para o CentOS](./ansible-deploy-solution-template.md)
- [Início Rápido: Configurar máquinas virtuais do Linux no Azure usando o Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)