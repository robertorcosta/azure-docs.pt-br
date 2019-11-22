---
title: Usando o Terraform com o Azure
description: Introdução ao uso do Terraform para controlar a versão e implantar a infraestrutura do Azure.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 1c6ac9b67f556b039b9ffd5ed725ea1f24aeeb3a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969257"
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de software livre para provisionar e gerenciar infraestruturas de nuvem. Ele codifica a infraestrutura em arquivos de configuração que descrevem a topologia dos recursos de nuvem. Esses recursos incluem máquinas virtuais, contas de armazenamento e adaptadores de rede. A CLI do Terraform fornece um mecanismo simples para implantar e controlar a versão de arquivos de configuração para o Azure.

Este artigo descreve os benefícios de usar o Terraform para gerenciar a infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatizar o gerenciamento de infraestrutura.

Os arquivos de configuração com base em modelo do Terraform permitem definir, provisionar e configurar recursos do Azure de forma repetível e previsível. Automatizar a infraestrutura traz várias vantagens:

- Reduz a possibilidade de erros humanos durante a implantação e gerenciamento da infraestrutura.
- Implanta o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste ao criá-los sob demanda.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Entender as alterações de infraestrutura antes de serem aplicadas

À medida que um recurso de topologia se torna complexo, pode ser difícil entender o significado e o impacto das alterações de infraestrutura.

A CLI do Terraform permite que os usuários validem e visualizem as alterações de infraestrutura antes do aplicativo. Visualizar as alterações de infraestrutura de maneira segura traz diversos benefícios:
- Os membros da equipe podem colaborar com mais eficiência compreendendo rapidamente as alterações propostas e seu impacto.
- Alterações inadvertidas podem ser detectadas logo no início do processo de desenvolvimento

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implantar a infraestrutura para várias nuvens

O Terraform é adepto à implantação de uma infraestrutura em vários provedores de nuvem. Ele permite que os desenvolvedores usem ferramentas consistentes para gerenciar cada definição de infraestrutura.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Terraform e seus benefícios, aqui estão as próximas etapas sugeridas:

- Comece [instalando e configurando o Terraform para usá-lo no Azure](/azure/virtual-machines/linux/terraform-install-configure).
- [Criar uma máquina virtual do Azure usando o Terraform](/azure/virtual-machines/linux/terraform-create-complete-vm)
- Explorar o [módulo do Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 