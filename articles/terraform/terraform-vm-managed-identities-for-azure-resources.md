---
title: Tutorial - criar uma VM do Linux com uma identidade gerenciada da imagem do Azure Marketplace usando o Terraform
description: Criar a VM do Linux Terraform com uma identidade gerenciada e gerenciamento de estado remoto usando a imagem do Azure Marketplace
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 233012d6caf1280914a6d2439ae856d69570fff7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838044"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Tutorial: Criar uma VM do Linux com uma identidade gerenciada da imagem do Azure Marketplace usando o Terraform

Este artigo mostra como utilizar uma [imagem do Marketplace do Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para criar uma VM de Linux Ubuntu (16.04 LTS) com a última versão do [Terraform](https://www.terraform.io/intro/index.html) instalada e configurada usando [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-service-identity/overview). Essa imagem também configura um back-end remoto para habilitar o gerenciamento de [estado remoto](https://www.terraform.io/docs/state/remote.html) utilizando o Terraform. 

A imagem do Marketplace do Terraform facilita o uso do Terraform no Azure, sem a necessidade de instalar e configurar o Terraform manualmente. 

Não há encargos de software para essa imagem de VM do Terraform. Você paga apenas as tarifas de uso de hardware do Azure com base no tamanho da VM provisionada. 

Para obter mais informações sobre os valores de computação, consulte a [Página de preços de VM do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar uma VM do Terraform do Linux, você deverá ter uma assinatura do Azure. Se você não tiver uma, consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Criar a VM do Terraform 

A seguir são apresentadas as etapas para criar uma instância de uma VM do Terraform do Linux: 

1. No Portal do Azure, vá até a lista [Criar um Recurso](https://ms.portal.azure.com/#create/hub).

1. Na barra de pesquisa **Pesquisar no Marketplace**, procure **Terraform**. 

1. Selecione o botão **Criar**. 

1. As seções a seguir fornecem entradas para cada uma das etapas no assistente para criar a VM do Terraform Linux. A seção a seguir lista as entradas que são necessárias para configurar cada uma dessas etapas.

## <a name="details-on-the-create-terraform-tab"></a>Detalhes na guia Criar Terraform

Digite os seguintes detalhes na guia **Criar Terraform**:

1. **Noções básicas**
    
   * **Nome**: O nome da sua VM Terraform.
   * **Nome de Usuário**: A primeira ID de entrada da conta.
   * **Senha**: A primeira senha da conta. (Você pode usar uma chave pública SSH, em vez de senha).
   * **Assinatura**: A assinatura na qual o computador será criado e cobrado. Você deve ter privilégios de criação de recurso nessa assinatura.
   * **Grupo de recursos**: Um grupo de recursos novo ou existente.
   * **Localização**: O datacenter que é mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para oferecer acesso mais rápido à rede.

2. **Configurações adicionais**

   * **Tamanho**: O tamanho da VM. 
   * **Tipo de disco da VM**: SSD ou HD.

3. **Resumo do Terraform**

   * Verifique se todas as informações inseridas estão corretas. 

4. **Comprar**

   * Para iniciar o processo de provisionamento, selecione **Comprar**. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa de tamanho.

A imagem de VM do Terraform executa as etapas a seguir:

* Cria uma VM com identidade atribuída pelo sistema baseado na imagem LTS do Ubuntu 16.04.
* Instala as identidades gerenciadas para a extensão de recursos do Azure na VM para permitir que os tokens OAuth sejam emitidos para recursos do Azure.
* Atribui permissões de RBAC para identidade gerenciada, concedendo direitos do proprietário para o grupo de recursos.
* Cria uma pasta de modelos do Terraform (tfTemplate).
* Pré-configura um estado remoto do Terraform com o back-end do Azure.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Acessar e configurar uma VM do Terraform para Linux

Depois de criar a VM, execute as seguintes etapas:

1. Entre na VM usando SSH. Use as credenciais de conta que você criou na seção anterior. No Windows, você pode baixar uma ferramenta de cliente SSH como o [Putty](https://www.putty.org/).

1. Conceda permissões de colaborador para toda a assinatura para identidades gerenciadas para recursos do Azure na VM. 

    A permissão de colaborador ajuda as identidades gerenciadas dos recursos do Azure na VM a usarem o Terraform para criar recursos fora do grupo de recursos da VM. Você pode fazer isso executando o seguinte script: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Esse script usa o mecanismo de [logon interativo da CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) para autenticação com o Azure. Esse processo atribui a [permissão de colaborador de identidade gerenciada ](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para toda a assinatura. 

1. A VM tem um back-end de estado remoto do Terraform. Para habilitá-lo na implantação do Terraform, você deve copiar o arquivo `remoteState.tf` para a raiz dos scripts do Terraform.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Para obter mais informações sobre o Gerenciamento de Estado Remoto, consulte [o estado remoto do Terraform](https://www.terraform.io/docs/state/remote.html). A chave de acesso de armazenamento é exposta neste arquivo. Exclua-a antes de confirmar os arquivos de configuração do Terraform no controle de origem.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)