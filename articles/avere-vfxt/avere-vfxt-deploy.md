---
title: Implantar o Avere vFXT para Azure
description: Etapas para implantar o cluster do Avere vFXT no Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255463"
---
# <a name="deploy-the-vfxt-cluster"></a>Implantar cluster vFXT

Este procedimento explica como usar o assistente de implantação disponível no Azure Marketplace. O assistente implanta automaticamente o cluster usando um modelo do Azure Resource Manager. Depois que você insere os parâmetros no formulário e clica em **Criar**, o Azure automaticamente conclui estas etapas:

* Cria o controlador de cluster, que é uma VM básica que contém o software necessário para implantar e gerenciar o cluster.
* Configura o grupo de recursos e a infraestrutura de rede virtual, incluindo a criação de novos elementos.
* Cria as VMs do nó de cluster e as configura como o cluster avere.
* Se solicitado, cria um novo contêiner de BLOBs do Azure e o configura como um arquivo de núcleo de cluster.

Depois de seguir as instruções neste documento, você terá uma rede virtual, uma sub-rede, um controlador e um cluster vFXT, conforme mostrado no diagrama a seguir. Este diagrama mostra o filer principal do blob do Azure opcional, que inclui um novo contêiner de armazenamento de BLOBs (em uma nova conta de armazenamento, não mostrado) e um ponto de extremidade de serviço para o armazenamento da Microsoft na sub-rede. 

![diagrama mostrando três retângulos concêntricos com componentes de cluster avere. O retângulo externo é rotulado como ' grupo de recursos ' e contém um hexágono rotulado como ' armazenamento de BLOBs (opcional) '. O próximo retângulo em é rotulado como ' rede virtual: 10.0.0.0/16 ' e não contém nenhum componente exclusivo. O retângulo mais interno é rotulado ' subnet: 10.0.0.0/24 ' e contém uma VM rotulada ' cluster Controller ', uma pilha de três VMs rotuladas como ' vFXT Nodes (cluster vFXT) ' e um hexágono rotulado como ' Service Endpoint '. Há uma seta para conectar o ponto de extremidade de serviço (que está dentro da sub-rede) e o armazenamento de BLOBs (que está fora da sub-rede e vnet, no grupo de recursos). A seta passa pelos limites de sub-rede e rede virtual.](media/avere-vfxt-deployment.png)  

Antes de usar o modelo de criação, verifique se você resolveu estes pré-requisitos:  

1. [Nova assinatura](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Permissões do proprietário da assinatura](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Ponto de extremidade do serviço de armazenamento (se necessário)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – necessário para implantações usando uma rede virtual existente e criando o armazenamento de BLOBs

Para obter mais informações sobre as etapas e o planejamento de implantação de cluster, leia [Plan your Avere vFXT system](avere-vfxt-deploy-plan.md) (Planejar seu sistema vFXT do Avere) e [Visão geral da implantação](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Criar o Avere vFXT para Azure

Acesse o modelo de criação no portal do Azure procurando por avere e selecionando "avere vFXT for Azure ARM template". 

![Janela do navegador mostrando o portal do Azure com a navegação estrutural "Novo > Marketplace > Tudo". Na página tudo, o campo de pesquisa tem o termo "avere" e o segundo resultado, "avere vFXT para o modelo ARM do Azure" é descrito em vermelho para realçá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes na página de modelo do avere vFXT para o Azure ARM, clique em **criar** para começar. 

![O Azure marketplace com a primeira página do modelo de implantação exibida](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro etapas – duas páginas de coleta de informações mais validação e confirmação das etapas. 

* A página um concentra-se nas configurações para a VM do controlador de cluster. 
* A página dois coleta parâmetros para criar o cluster e os recursos associados, como sub-redes e armazenamento. 
* A página três resume as configurações e valida a configuração. 
* A página quatro explica os termos e condições de software e permite que você inicie o processo de criação do cluster. 

## <a name="page-one-parameters---cluster-controller-information"></a>Parâmetros da página um – informações do controlador de cluster

A primeira página do modelo de implantação coleta informações sobre o controlador de cluster. 

![Primeira página do modelo de implantação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome do controlador de cluster** – defina o nome para a VM do controlador de cluster.

* **Nome de usuário do controlador** – preencha o nome de usuário raiz para a VM do controlador de cluster. 

* **Tipo de autenticação** – escolha autenticação por senha ou chave pública SSH para se conectar ao controlador. O método de chave pública SSH é recomendado. Leia [Como criar e usar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de ajuda.

* **Senha** ou **Chave pública SSH** – dependendo do tipo de autenticação selecionado, você deve fornecer uma chave pública RSA ou uma senha nos próximos campos. Essa credencial é usada com o nome de usuário fornecido anteriormente.

* **Assinatura** – selecione a assinatura para o Avere vFXT. 

* **Grupo de recursos** - escolha um grupo de recursos vazio já criado para o cluster do Avere vFXT ou clique em "Criar novo" e insira um novo nome de grupo de recursos. 

* **Localização** – selecione a localização do Azure para seu cluster e recursos.

Clique em **OK** quando terminar. 

> [!NOTE]
> Se você quiser que o controlador de cluster tenha um endereço IP público, crie uma nova rede virtual para o cluster, em vez de selecionar uma rede existente. Essa configuração está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parâmetros da página dois – informações do cluster do vFXT

A segunda página do modelo de implantação permite que você defina o tamanho do cluster, o tipo de nó, o tamanho do cache e os parâmetros de armazenamento, entre outras configurações. 

![Segunda página do modelo de implantação](media/avere-vfxt-deploy-2.png)

* **Contagem de nó de cluster do Avere vFXT** – escolha o número de nós a serem usados no cluster. O mínimo é de três nós e o máximo é de doze. 

* **Senha de administração de cluster** – crie a senha de administração de cluster. Essa senha será usada com o nome de usuário ```admin``` para entrar no painel de controle do cluster para monitorar o cluster e definir as configurações.

* **Nome do cluster do Avere vFXT** – dê ao cluster um nome exclusivo. 

* **Tamanho** – esta seção mostra o tipo de VM que será usado para os nós de cluster. Embora haja apenas uma opção recomendada, o link **alterar tamanho** abre uma tabela com detalhes sobre esse tipo de instância e um link para uma calculadora de preços.  

* **Tamanho do cache por nó** – o cache do cluster é disseminado entre os nós de cluster, portanto, o tamanho do cache total no cluster do Avere vFXT será o tamanho do cache por nó multiplicado pelo número de nós. 

  A configuração recomendada é usar 4 TB por nó para nós Standard_E32s_v3.

* **Rede virtual** – defina uma nova vnet para hospedar o cluster ou selecione uma vnet existente que atenda aos pré-requisitos descritos em [planejar seu sistema avere vFXT](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Se você criar uma nova VNET, o controlador de cluster terá um endereço IP público para que você possa acessar a nova rede privada. Se você escolher uma rede virtual existente, o controlador de cluster será configurado sem um endereço IP público. 
  > 
  > Um endereço IP publicamente visível no controlador de cluster fornece acesso mais fácil para o cluster vFXT, mas gera um pequeno risco de segurança. 
  >  * Um endereço IP público no controlador de cluster permite que você o use como um host de atalho para se conectar ao cluster do Avere vFXT de fora da sub-rede privada.
  >  * Se você não configurar um endereço IP público no controlador, deverá usar outro host de atalho, uma conexão VPN ou o ExpressRoute para acessar o cluster. Por exemplo, crie o controlador dentro da rede virtual que já tem uma conexão VPN configurada.
  >  * Se você criar um controlador com um endereço IP público, você deverá proteger a VM do controlador com um grupo de segurança de rede. Por padrão, a implantação do Avere vFXT para Azure cria um grupo de segurança de rede e restringe o acesso de entrada somente à porta 22 para os controladores com endereços IP públicos. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

  O modelo de implantação também configura a nova vnet com um ponto de extremidade de serviço de armazenamento para o armazenamento de BLOBs do Azure e com o controle de acesso à rede bloqueado somente para IPs da sub-rede do cluster. 

* **Sub-rede** – escolha uma sub-rede de sua rede virtual existente ou crie uma nova. 

* **Criar e usar o armazenamento de BLOBs** -escolha **true** para criar um novo contêiner de blob do Azure e configurá-lo como armazenamento de back-end para o novo cluster avere vFXT. Essa opção também cria uma nova conta de armazenamento dentro do mesmo grupo de recursos que o cluster e um ponto de extremidade do serviço de armazenamento da Microsoft dentro da sub-rede do cluster. 
  
  Se você fornecer uma rede virtual existente, ela deverá ter um ponto de extremidade de serviço de armazenamento antes de criar o cluster. (Para obter mais informações, leia [planejar o sistema avere vFXT](avere-vfxt-deploy-plan.md).)

  Defina este campo como **false** se não quiser criar um novo contêiner. Nesse caso, você deve anexar e configurar o armazenamento depois de criar o cluster. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções. 

* **(Novo) conta de armazenamento** -se estiver criando um novo contêiner de blob do Azure, insira um nome para a nova conta de armazenamento. 

## <a name="validation-and-purchase"></a>Validação e compra

A página três resume a configuração e valida os parâmetros. Após a validação ser bem-sucedida, clique no botão **OK** para continuar. 

![Terceira página do modelo de implantação – validação](media/avere-vfxt-deploy-3.png)

Na página quatro, insira as informações de contato necessárias e clique no botão **criar** para aceitar os termos e criar o avere vFXT para o cluster do Azure. 

![Quarta página do modelo de implantação – termos e condições, botão criar](media/avere-vfxt-deploy-4.png)

A implantação do cluster leva de 15 a 20 minutos.

## <a name="gather-template-output"></a>Coletar a saída de modelo

Quando o modelo do Avere vFXT termina de criar o cluster, ele produz algumas informações importantes sobre o novo cluster. 

> [!TIP]
> Copie o endereço IP de gerenciamento da saída do modelo. Você precisa desse endereço para administrar o cluster.

Para localizar essas informações, siga este procedimento:

1. Vá para o grupo de recursos do seu controlador de cluster.

1. No lado esquerdo, clique em **Implantações** e, em seguida, em **microsoft-avere.vfxt-template**.

   ![Página do portal do grupo de recursos com implantações selecionadas à esquerda e microsoft-avere.vfxt-template sendo exibido em uma tabela embaixo do nome da implantação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **Saídas**. Copie os valores em cada um dos campos. 

   ![produz a página mostrando os valores SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP em campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Próxima etapa

Agora que o cluster está em execução e você conhece seu endereço IP de gerenciamento, é possível [conectar-se à ferramenta de configuração de cluster](avere-vfxt-cluster-gui.md) para habilitar o suporte, adicionar o armazenamento se necessário e personalizar outras configurações do cluster.
