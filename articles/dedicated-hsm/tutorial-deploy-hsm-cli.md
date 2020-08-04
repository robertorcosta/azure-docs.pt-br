---
title: Tutorial – Implantar em uma rede virtual existente usando a CLI do Azure – HSM Dedicado do Azure | Microsoft Docs
description: Tutorial mostrando como implantar um HSM dedicado usando a CLI em uma rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c5484c421807f5657fe5fc460342d39d442bcda
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048581"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Tutorial: Implantar HSMs em uma rede virtual existente usando a CLI

O HSM dedicado do Azure fornece um dispositivo físico para uso exclusivo do cliente, com controle administrativo e responsabilidade de gerenciamento completos. O uso de dispositivos físicos faz com que a Microsoft precise controlar a alocação de dispositivos para gerenciar a capacidade com eficiência. Como resultado, dentro de uma assinatura do Azure, o serviço HSM dedicado normalmente não estará visível para o provisionamento de recursos. Qualquer cliente do Azure que precise de acesso ao serviço de HSM dedicado deve, primeiro, entrar em contato com o responsável pela conta da Microsoft para solicitar o registro no serviço. O provisionamento será possível depois que esse processo tiver sido concluído com êxito. 

Este tutorial mostra um processo de provisionamento típico no qual:

- Um cliente já tem uma rede virtual
- Já tem uma máquina virtual
- E precisa adicionar recursos de HSM ao ambiente existente.

Uma arquitetura de implantação típica, com várias regiões e com alta disponibilidade deve se parecer com isto:

![implantação com várias regiões](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Este tutorial se concentra na integração de HSMs e do Gateway do ExpressRoute exigido (consulte a Sub-rede 1 acima) a uma rede virtual existente (consulte a VNET 1 acima).  Todos os outros recursos são recursos padrão do Azure. O mesmo processo de integração pode ser usado para HSMs na sub-rede 4 na VNET 3 ou acima.

## <a name="prerequisites"></a>Pré-requisitos

O HSM dedicado do Azure não está disponível atualmente no portal do Azure. Toda a interação com o serviço ocorrerá por meio da linha de comando ou usando o PowerShell. Este tutorial usará a CLI (interface de linha de comando) no Azure Cloud Shell. Se você estiver familiarizado com a CLI do Azure, siga estas instruções de introdução: [Introdução à CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Suposições:

- Você concluiu o processo de registro do HSM dedicado do Azure
- Você recebeu aprovação para usar o serviço. Se não for o caso, entre em contato com seu representante da conta Microsoft para obter detalhes.
- Você criou um Grupo de Recursos para esses recursos, e os novos que foram implantados neste tutorial serão incluídos nesse grupo.
- Você já criou a rede virtual, a sub-rede e as máquinas virtuais necessárias de acordo com o diagrama acima e agora deseja integrar dois HSMs à implantação.

Todas as instruções abaixo pressupõem que você já navegou até o portal do Azure e abriu o Cloud Shell (selecione "\>\_" no canto superior direito do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionar um HSM Dedicado

O provisionamento de HSMs e a integração deles a uma rede virtual existente por meio do Gateway do ExpressRoute serão validados usando SSH. Essa validação ajuda a garantir a acessibilidade e a disponibilidade básica do dispositivo HSM para atividades de configuração futuras. Os comandos a seguir usarão um modelo do Azure Resource Manager para criar os recursos de HSM e os recursos de rede associados.

### <a name="validating-feature-registration"></a>Validar o registro de recursos

Conforme mencionado anteriormente, qualquer atividade de provisionamento requer que o serviço HSM Dedicado esteja registrado na sua assinatura. Para validá-lo, execute os comandos a seguir no Cloud Shell do portal do Azure.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Os comandos devem retornar um status "Registrado" (conforme mostrado abaixo). Se os comandos não retornarem "Registrado", você precisará se registrar nesse serviço entrando em contato com seu representante da conta Microsoft.

![status da assinatura](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criar recursos do HSM

Um HSM é provisionado na rede virtual dos clientes e, por isso, é necessário ter uma rede virtual e uma sub-rede. A dependência para que o HSM habilite a comunicação entre a rede virtual e o dispositivo físico é um Gateway do ExpressRoute; por fim, é necessária uma máquina virtual para acessar o dispositivo HSM usando o software de cliente Gemalto. Esses recursos foram coletados em um arquivo de modelo, com o arquivo de parâmetros correspondente, para facilitar o uso. Os arquivos são disponibilizados entrando em contato diretamente com a Microsoft pelo email HSMrequest@Microsoft.com.

Assim que tiver os arquivos, você deverá editar o arquivo de parâmetros a fim de inserir seus nomes preferenciais para os recursos. Edite linhas com "valor": "".

- `namingInfix` Prefixo para nomes de recursos do HSM
- `ExistingVirtualNetworkName` Nome da rede virtual usada para os HSMs
- `DedicatedHsmResourceName1` Nome do recurso do HSM no carimbo 1 do datacenter
- `DedicatedHsmResourceName2` Nome do recurso do HSM no carimbo 2 do datacenter
- `hsmSubnetRange` Intervalo de endereços IP de sub-rede para HSMs
- `ERSubnetRange` Intervalo de endereços IP de sub-rede para o gateway de VNET

Um exemplo dessas alterações é apresentado abaixo:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

O arquivo de modelo do Azure Resource Manager associado criará seis recursos com essas informações:

- Uma sub-rede para os HSMs na VNET especificada
- Uma sub-rede para o gateway de rede virtual
- Um gateway de rede virtual que conecta a VNET aos dispositivos HSM
- Um endereço IP público para o gateway
- Um HSM no carimbo 1
- Um HSM no carimbo 2

Assim que os valores de parâmetro estiverem definidos, os arquivos precisarão ser carregados no compartilhamento de arquivos de Cloud Shell do portal do Azure para serem usados. No portal do Azure, clique no símbolo "\>\_" do Cloud Shell no canto superior direito, o que transformará a parte inferior da tela em um ambiente de comando. As opções para isso são BASH e PowerShell, sendo que você deve selecionar BASH caso ainda não o tenha feito.

O shell de comando tem uma opção de upload/download na barra de ferramentas, e você deve selecionar essa opção para fazer o upload dos arquivos de modelo e parâmetros para seu compartilhamento de arquivos:

![compartilhamento de arquivos](media/tutorial-deploy-hsm-cli/file-share.png)

Assim que os arquivos forem carregados, você poderá criar recursos. Antes da criação de novos recursos do HSM, alguns recursos de pré-requisito devem estar em uso. Você deve ter uma rede virtual com intervalos de sub-rede para computação, HSMs e gateway. Os comandos a seguir servem como exemplo do que criaria essa rede virtual.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>A configuração mais importante a ser observada em relação à rede virtual é que a sub-rede do dispositivo HSM deve ter as delegações definidas como "Microsoft.HardwareSecurityModules/dedicatedHSMs".  O provisionamento de HSM não funcionará sem isso.

Assim que todos os pré-requisitos forem atendidos, execute o comando a seguir para usar o modelo do Azure Resource Manager, após atualizar os valores com seus nomes exclusivos (pelo menos o nome do grupo de recursos):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Essa implantação deve levar aproximadamente de 25 a 30 minutos para ser concluída; a maior parte desse tempo deve ser consumida pelos dispositivos HSM

![status do provisionamento](media/tutorial-deploy-hsm-cli/progress-status.png)

Quando a implantação for concluída com êxito, "provisioningState": "Êxito" será exibido. Você pode se conectar à máquina virtual existente e usar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificando a implantação

Para verificar os dispositivos que foram provisionados e ver os atributos do dispositivo, execute o conjunto de comandos a seguir. Verifique se o grupo de recursos está definido corretamente e se o nome do recurso é exatamente igual ao que você tem no arquivo de parâmetros.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![saída do provisionamento](media/tutorial-deploy-hsm-cli/progress-status2.png)

Agora você também pode ver os recursos usando o [Azure Resource Explorer](https://resources.azure.com/).   Uma vez no Explorer, expanda "Assinaturas" à esquerda, expanda sua assinatura específica do HSM Dedicado, expanda "grupos de recursos", expanda o grupo de recursos que você usou e, por fim, selecione o item "recursos".

## <a name="testing-the-deployment"></a>Testar a implantação

Para o teste da implantação, você se conecta a uma máquina virtual que pode acessar o HSM(s) para depois se conectar diretamente ao dispositivo HSM. Essas ações confirmarão que o HSM está acessível.
A ferramenta SSH é usada para se conectar à máquina virtual. O comando será semelhante ao apresentado a seguir, mas com o nome de administrador e o nome DNS especificados no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

O endereço IP da VM também pode ser usado no lugar do nome DNS no comando acima. Se o comando for bem-sucedido, ele solicitará uma senha; insira essa senha. Depois de fazer logon na máquina virtual, você poderá entrar no HSM usando o endereço IP encontrado no portal para o recurso de adaptador de rede associado ao HSM.

![lista de componentes](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Observe a caixa de seleção "Mostrar tipos ocultos", que, quando selecionada, exibirá recursos do HSM.

Na captura de tela acima, um clique em "HSM1_HSMnic" ou "HSM2_HSMnic" mostraria o endereço IP privado pertinente. Caso contrário, o comando `az resource show` usado acima é outra forma de identificar o endereço IP correto. 

Quando você tiver o endereço IP correto, execute o seguinte comando substituindo o endereço:

`ssh tenantadmin@10.0.2.4`

Se tiver êxito, você receberá uma solicitação de senha. A senha padrão é PASSWORD; primeiro, o HSM solicitará que você altere sua senha, ou seja, defina uma senha forte e use o mecanismo preferido de sua organização para armazenar a senha e evitar sua perda.

>[!IMPORTANT]
>Se você perder a senha, o HSM precisará ser redefinido, o que significa que você perderá as chaves.

Quando estiver conectado ao HSM usando SSH, execute o comando a seguir para ter certeza de que o HSM está funcionando.

`hsm show`

A saída deve se parecer com a mostrada na imagem abaixo:

![lista de componentes](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

A essa altura, você alocou todos os recursos para ter uma implantação de dois HSMs altamente disponíveis e validou o acesso e o estado operacional. Outras configurações ou testes envolvem mais trabalho com o próprio dispositivo HSM. Para inicializar o HSM e criar partições, siga as instruções no Capítulo 7 do Guia de administração do Gemalto Luna Network HSM 7. Toda a documentação e o software estão disponíveis para download diretamente na Gemalto depois que você tiver se registrado no Portal de suporte do cliente da Gemalto e tiver uma ID de cliente. Baixe o software cliente versão 7.2 para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Excluir ou limpar recursos

Caso tenha terminado de usar somente o dispositivo HSM, ele poderá ser excluído como um recurso e devolvido ao pool gratuito. O motivo óbvio de preocupação aqui diz respeito a dados confidenciais de clientes que estejam no dispositivo. A melhor maneira de "zerar" um dispositivo é inserir a senha incorreta do administrador do HSM três vezes (observação: esse não é o administrador do dispositivo, é o administrador real do HSM). Como medida de segurança para proteger o material da chave, o dispositivo não pode ser excluído como um recurso do Azure até que esteja no estado zerado.

> [!NOTE]
> Caso tenha problema com alguma configuração do dispositivo Gemalto, entre em contato com o [atendimento ao cliente da Gemalto](https://safenet.gemalto.com/technical-support/).


Ao concluir o uso de todos os recursos desse grupo de recursos, remova-os com o seguinte comando:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Próximas etapas

Após a conclusão das etapas do tutorial, os recursos do HSM dedicado terão sido provisionados e você terá uma rede virtual com os HSMs necessários, além de outros componentes de rede que permitem a comunicação com o HSM.  Agora você pode complementar essa implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferencial. Para saber mais sobre como ajudar a planejar sua implantação, confira os documentos de Conceitos.
Recomendamos o uso de um design com dois HSMs em uma região primária abordando a disponibilidade no nível do rack e dois HSMs em uma região secundária abordando a disponibilidade regional. O arquivo de modelo usado neste tutorial pode ser usado facilmente como base para uma implantação de dois HSM, mas precisa ter seus parâmetros modificados para atender às suas necessidades.

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoring](monitoring.md)
