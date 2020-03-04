---
title: Tutorial – Validar uma rede hub e spoke no Azure usando o Terraform
description: Tutorial para validar a topologia de rede de hub e spoke com todas as redes virtuais conectadas umas às outras.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159197"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Tutorial: Validar uma rede hub e spoke no Azure usando o Terraform

Neste artigo, você executará os arquivos do terraform criados no artigo anterior desta série. O resultado é uma validação da conectividade entre as redes virtuais de demonstração.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Usar HCL (HashiCorp Language) para implementar a VNet do Hub na topologia hub-spoke
> * Usar o plano do Terraform para verificar os recursos a serem implantados
> * Usar o Terraform apply para criar os recursos no Azure
> * Verificar a conectividade entre redes diferentes
> * Usar o Terraform para destruir todos os recursos

## <a name="prerequisites"></a>Prerequisites

1. [Criar uma topologia de rede híbrida de hub e spoke com o Terraform no Azure](./terraform-hub-spoke-introduction.md).
1. [Criar rede virtual local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md).
1. [Criar uma rede virtual de hub com o Terraform no Azure](./terraform-hub-spoke-hub-network.md).
1. [Criar um dispositivo de rede virtual de hub com o Terraform no Azure](./terraform-hub-spoke-hub-nva.md).
1. [Crie redes virtuais spoke com o Terraform no Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Verifique sua configuração

Depois de concluir os [pré-requisitos](#prerequisites), verifique se os arquivos de configuração apropriados estão presentes.

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se você não selecionou um ambiente anteriormente, selecione **Bash** como o ambiente.

    ![Aviso do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Altere os diretórios para o novo diretório:

    ```bash
    cd hub-spoke
    ```

1. Execute o comando `ls` para verificar se os arquivos de configuração `.tf` criados nos tutoriais anteriores estão listados:

    ![Arquivos de configuração de demonstração do Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Implantar os recursos

1. Inicialize o provedor do Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Exemplo de resultados do comando “terraform init”](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Execute o comando `terraform plan` para ver o efeito da implantação antes da execução:

    ```bash
    terraform plan
    ```
    
    ![Exemplo de resultados do comando “terraform plan”](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Implante a solução:

    ```bash
    terraform apply
    ```
    
    Insira `yes` quando for solicitado a confirmar a implantação.

    ![Exemplo de resultados do comando “terraform apply”](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>TEstar VNet do hub e VNets do spoke

Esta seção mostra como testar a conectividade do ambiente local simulado para a VNet do hub.

1. No portal do Azure, navegue até o grupo de recursos **onprem-vnet-rg**.

1. Na guia **onprem-vnet-rg**, selecione a VM denominada **onprem-vm**.

1. Selecione **Conectar**.

1. Ao lado do texto **Logon usando a conta local da VM**, copie o comando **ssh** para a área de transferência.

1. Em um prompt do Linux, execute `ssh` para se conectar ao ambiente local simulado. Use a senha especificada no arquivo de parâmetro `on-prem.tf`.

1. Execute o comando `ping` para testar a conectividade com a VM jumpbox na VNet do hub:

   ```bash
   ping 10.0.0.68
   ```

1. Execute o comando `ping` para testar a conectividade com a VM jumpbox em cada spoke:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Para sair da sessão ssh na máquina virtual **onprem-vm**, insira `exit` e pressione &lt;Enter>.

## <a name="troubleshoot-vpn-issues"></a>Solucionar problemas de VPN

Para saber mais sobre como resolver erros de VPN, confira o artigo [Solucionar problemas de uma conexão VPN híbrida](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua os recursos criados na série de tutoriais.

1. Remova os recursos declarados no plano:

    ```bash
    terraform destroy
    ```

    Insira `yes` quando for solicitado a confirmar a remoção dos recursos.

1. Altere os diretórios para o diretório pai:

    ```bash
    cd ..
    ```

1. Exclua o diretório `hub-scope` (incluindo todos os seus arquivos):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)