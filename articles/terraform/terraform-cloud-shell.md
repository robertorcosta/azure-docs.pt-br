---
title: Tutorial – Configurar o Azure Cloud Shell para o Terraform
description: Use o Terraform com o Azure Cloud Shell para simplificar a autenticação e a configuração do modelo.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 1259d5004bd547e33f65571333b6d0721d1253c0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078722"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Tutorial: Configurar o Azure Cloud Shell para o Terraform

O Terraform funciona bem por meio de uma linha de comando do Bash no macOS, no Windows ou no Linux. Executar suas configurações do Terraform na experiência do Bash do [Azure Cloud Shell](/azure/cloud-shell/overview) traz algumas vantagens exclusivas. Este tutorial mostra como gravar scripts Terraform que são implantados no Azure usando o Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credencial

O Terraform vem instalado e imediatamente disponível no Cloud Shell. Os scripts do Terraform são autenticados com o Azure quando conectado ao Cloud Shell para gerenciar a infraestrutura sem nenhuma configuração adicional. A autenticação automática ignora dois processos manuais:
- Criar uma entidade de serviço do Azure Active Directory
- Configurar as variáveis do provedor Terraform do Azure


## <a name="use-modules-and-providers"></a>Usar módulos e provedores

Os módulos Terraform do Azure exigem credenciais para acessar e modificar os recursos do Azure. Para usar os módulos Terraform no Cloud Shell, adicione o seguinte código:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

O Cloud Shell passa os valores necessários ao provedor `azurerm` por meio de variáveis de ambiente quando você usa qualquer um dos comandos `terraform` da CLI.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de desenvolvedor do Cloud Shell

Arquivos e estados de shell são mantidos no Armazenamento do Azure entre as sessões do Cloud Shell. Use o [Gerenciador de Armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar arquivos do seu computador local para o Cloud Shell.

A CLI do Azure está disponível no Cloud Shell e é uma ótima ferramenta para testar configurações e verificar seu trabalho após o término de `terraform apply` ou `terraform destroy`.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um pequeno cluster de VM usando o Registro do módulo](terraform-create-vm-cluster-module.md)
