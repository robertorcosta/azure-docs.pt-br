---
title: Tutorial – Criar uma topologia de rede híbrida de hub e spoke no Azure usando o Terraform
description: Tutorial que ilustra como criar uma arquitetura de referência de rede híbrida inteira no Azure usando o Terraform
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472172"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Tutorial: Criar uma topologia de rede híbrida de hub e spoke no Azure usando o Terraform

Esta série de tutoriais mostra como usar o Terraform para implementar no Azure uma [topologia de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Uma topologia de hub e spoke é uma maneira de isolar as cargas de trabalho enquanto se compartilham serviços comuns. Esses serviços incluem a identidade e segurança. O hub é uma rede virtual (VNet) no Azure que atua como ponto central de conectividade para sua rede local. Os spokes são VNets que se emparelham com o hub. Os serviços compartilhados são implantados no hub, enquanto as cargas de trabalho individuais são implantadas dentro das redes de spoke.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Usar o HCL (HashiCorp Language) para dispor recursos de arquitetura de referência de rede híbrida hub e spoke
> * Usar o Terraform para criar recursos de dispositivo de rede de hub
> * Usar o Terraform para criar a rede de hub no Azure para atuar como um ponto comum para todos os recursos
> * Usar o Terraform para criar cargas de trabalho individuais como as redes virtuais spoke no Azure
> * Usar o Terraform para estabelecer gateways e conexões entre os locais e redes do Azure
> * Usar o Terraform para criar os emparelhamentos de rede virtual para as redes de spoke

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Instalar e configurar o Terraform**: Para provisionar máquinas virtuais e outra infraestrutura no Azure, [instale e configure o Terraform](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>Arquitetura de topologia de hub e spoke

Na topologia de hub e spoke, o hub é uma rede virtual. As redes virtuais agem como um ponto central de conectividade para a sua rede local. Os raios são VNets que ponto a ponto com o hub e pode ser usado para isolar as cargas de trabalho. Fluxos de tráfego entre o datacenter local e o hub por meio de uma conexão de ExpressRoute ou gateway de VPN. A imagem a seguir demonstra os componenentes em uma topologia hub e spoke:

![Arquitetura de topologia hub e spoke no Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Benefícios da topologia hub e spoke

A topologia de rede hub e spoke é uma forma de isolar as cargas de trabalho ao compartilhar serviços em comum. Esses serviços incluem a identidade e segurança. O hub é uma rede virtual no Azure que atua como ponto central de conectividade para sua rede local. Os spokes são VNets que se emparelham com o hub. Os serviços compartilhados são implantados no hub, enquanto as cargas de trabalho individuais são implantadas dentro das redes de spoke. Aqui estão alguns benefícios da topologia de rede de hub e spoke:

- **Economia de custos** centralizando serviços em um único local que pode ser compartilhado por várias cargas de trabalho. Essas cargas de trabalho incluem dispositivos de rede virtual e servidores DNS.
- **Superar os limites de assinaturas** emparelhando VNets de assinaturas diferentes com o hub central.
- **Separação de preocupações** entre TI central (SecOps, InfraOps) e cargas de trabalho (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Usos típicos para a arquitetura de hub e spoke

Alguns dos usos típicos para uma arquitetura de hub e spoke incluem:

- Muitos clientes têm cargas de trabalho que são implantadas em ambientes diferentes. Esses ambientes incluem desenvolvimento, teste e produção. Muitas vezes, essas cargas de trabalho precisam compartilhar serviços como DNS, IDS, NTP ou AD DS. Esses serviços compartilhados podem ser colocados na rede virtual do hub. Dessa forma, cada ambiente é implantado em um spoke para manter o isolamento.
- As cargas de trabalho que não exigem conectividade entre si, mas precisam de acesso aos serviços compartilhados.
- Empresas que exigem controle centralizado sobre aspectos de segurança.
- Empresas que exigem gerenciamento segregado para cargas de trabalho em cada spoke.

## <a name="preview-the-demo-components"></a>Visualizar os componentes de demonstração

Conforme você trabalha com cada tutorial desta série, vários componentes são definidos nos scripts distintos do Terraform. A arquitetura de demonstração criada e implantada consiste dos seguintes componentes:

- **Rede local**. Uma rede de área local privada em execução com uma organização. Arquitetura de referência de hub e spoke, uma rede virtual no Azure é usada para simular uma rede local.

- **Dispositivo VPN**. Um dispositivo ou serviço VPN que fornece conectividade externa com a rede local. O dispositivo VPN pode ser um dispositivo de hardware ou uma solução de software. 

- **VNet do hub**. O hub é um ponto central de conectividade para sua rede local e um local para hospedar serviços. Esses serviços podem ser consumidos por diferentes cargas de trabalho hospedadas nas redes virtuais do spoke.

- **Gateway de sub-rede**. Os gateways de rede virtual são mantidos na mesma sub-rede.

- **VNets de spoke**. Spokes podem ser usados para isolar as cargas de trabalho em suas próprias VNets, gerenciadas separadamente de outros spokes. Cada carga de trabalho pode incluir várias camadas, com várias sub-redes conectadas por meio de balanceadores de carga do Azure. 

- **Emparelhamento VNet**. Duas redes virtuais podem ser conectadas usando uma conexão de emparelhamento. Conexões de emparelhamento são conexões não transitivas de baixa latência entre VNets. Quando emparelhadas, as redes virtuais trocam tráfego usando o backbone do Azure sem precisar de um roteador. Em uma topologia de rede hub e spoke, é necessário usar o emparelhamento de rede virtual para conectar o hub a cada spoke. Você pode parear redes virtuais na mesma região ou em regiões diferentes.

## <a name="create-the-directory-structure"></a>Crie a estrutura de diretórios

Crie o diretório que contém os arquivos de configuração do Terraform para o exercício.

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se você não selecionou um ambiente anteriormente, selecione **Bash** como o ambiente.

    ![Aviso do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Altere os diretórios para o novo diretório:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Declarar o provedor do Azure

Crie o arquivo de configuração Terraform que declara o provedor do Azure.

1. No Cloud Shell, abra um novo arquivo nomeado `main.tf`.

    ```bash
    code main.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Salve o arquivo e saia do editor.

## <a name="create-the-variables-file"></a>Criar o arquivo de variáveis

Crie o arquivo de configuração do Terraform para variáveis comuns que são usadas em scripts diferentes.

1. No Cloud Shell, abra um novo arquivo nomeado `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Salve o arquivo e saia do editor.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Criar rede virtual local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md)