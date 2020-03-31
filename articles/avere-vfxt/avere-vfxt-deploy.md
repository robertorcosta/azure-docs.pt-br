---
title: Implantar o Avere vFXT para Azure
description: Etapas para implantar o cluster do Avere vFXT no Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252591"
---
# <a name="deploy-the-vfxt-cluster"></a>Implantar cluster vFXT

Este procedimento explica como usar o assistente de implantação disponível no Azure Marketplace. O assistente implanta automaticamente o cluster usando um modelo do Azure Resource Manager. Depois de inserir os parâmetros no formulário e clicar em **Criar**, o Azure completa automaticamente essas tarefas:

* Cria o controlador de cluster, que é uma VM básica que contém o software necessário para implantar e gerenciar o cluster.
* Configura grupo de recursos e infra-estrutura de rede virtual, incluindo a criação de novos elementos.
* Cria as VMs do nó de cluster e as configura como o cluster Avere.
* Se solicitado, cria um novo contêiner Azure Blob e configura-o como um arquivo de núcleo de cluster.

Depois de seguir as instruções deste documento, você terá uma rede virtual, uma sub-rede, um controlador de cluster e um cluster vFXT, conforme mostrado no diagrama a seguir. Este diagrama mostra o arquivo núcleo azure Blob opcional, que inclui um novo contêiner de armazenamento Blob (em uma nova conta de armazenamento, não mostrado) e um ponto final de serviço para armazenamento Microsoft dentro da sub-rede.

![diagrama mostrando três retângulos concêntricos com componentes do cluster Avere. O retângulo externo é rotulado como 'Grupo de recursos' e contém um hexágono rotulado 'Blob storage (opcional)'. O próximo retângulo é rotulado como 'Rede virtual: 10.0.0.0/16' e não contém nenhum componente único. O retângulo mais interno é rotulado como 'Subnet:10.0.0.0/24' e contém um VM rotulado 'Controlador de cluster', uma pilha de três VMs rotuladas como 'nodes vFXT (cluster vFXT)' e um hexágono rotulado 'Ponto final de serviço'. Há uma seta conectando o ponto final do serviço (que está dentro da sub-rede) e o armazenamento blob (que está fora da sub-rede e da vnet, no grupo de recursos). A seta passa pelos limites da sub-rede e da rede virtual.](media/avere-vfxt-deployment.png)

Antes de usar o modelo de criação, verifique se você resolveu estes pré-requisitos:  

* [Nova assinatura](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Permissões do proprietário da assinatura](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Ponto final do serviço de armazenamento (se necessário)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) - Necessário para implantações que usam uma rede virtual existente e criam armazenamento blob

Para obter mais informações sobre as etapas e o planejamento de implantação de cluster, leia [Plan your Avere vFXT system](avere-vfxt-deploy-plan.md) (Planejar seu sistema vFXT do Avere) e [Visão geral da implantação](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Criar o Avere vFXT para Azure

Acesse o modelo de criação no portal Azure procurando por Avere e selecionando "Avere vFXT for Azure ARM Template".

![Janela do navegador mostrando o portal do Azure com a navegação estrutural "Novo > Marketplace > Tudo". Na página Everything, o campo de pesquisa tem o termo "avere" e o segundo resultado, "Avere vFXT for Azure ARM Template" é descrito em vermelho para destacá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes na página Avere vFXT for Azure ARM Template, clique no botão **Criar** para começar.

![O Azure marketplace com a primeira página do modelo de implantação exibida](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro etapas – duas páginas de coleta de informações mais validação e confirmação das etapas.

* A página um reúne configurações para o controlador de cluster VM.
* A página dois coleta parâmetros para criar o cluster e recursos adicionais, como sub-redes e armazenamento.
* A página três resume suas escolhas e valida a configuração.
* A página quatro explica os termos e condições de software e permite que você inicie o processo de criação do cluster.

## <a name="page-one-parameters---cluster-controller-information"></a>Parâmetros da página um – informações do controlador de cluster

A primeira página do modelo de implantação se concentra no controlador de cluster.

![Primeira página do modelo de implantação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome do controlador de cluster** – defina o nome para a VM do controlador de cluster.

* **Nome de usuário do controlador** - Defina o nome de usuário raiz para o controlador de cluster VM.

* **Tipo de autenticação** – escolha autenticação por senha ou chave pública SSH para se conectar ao controlador. O método de chave pública SSH é recomendado. Leia [Como criar e usar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de ajuda.

* **Senha** ou **Chave pública SSH** – dependendo do tipo de autenticação selecionado, você deve fornecer uma chave pública RSA ou uma senha nos próximos campos. Essa credencial é usada com o nome de usuário fornecido anteriormente.

* **Assinatura** – selecione a assinatura para o Avere vFXT.

* **Grupo de recursos** - escolha um grupo de recursos vazio já criado para o cluster do Avere vFXT ou clique em "Criar novo" e insira um novo nome de grupo de recursos.

* **Localização** – selecione a localização do Azure para seu cluster e recursos.

Clique **em OK** quando terminar.

> [!NOTE]
> Se você quiser que o controlador de cluster tenha um endereço IP público, crie uma nova rede virtual para o cluster, em vez de selecionar uma rede existente. Essa configuração está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parâmetros da página dois – informações do cluster do vFXT

A segunda página do modelo de implantação permite que você defina o tamanho do cluster, o tipo de nó, o tamanho do cache e os parâmetros de armazenamento, entre outras configurações.

![Segunda página do modelo de implantação](media/avere-vfxt-deploy-2.png)

* **Contagem de nó de cluster Avere vFXT** - Escolha o número de nós no cluster. O mínimo é de três nós e o máximo é de doze.

* **Senha de administração de cluster** – crie a senha de administração de cluster. Esta senha é usada ```admin``` com o nome de usuário para entrar no painel de controle de cluster, onde você pode monitorar o cluster e configurar configurações de cluster.

* **Nome do cluster do Avere vFXT** – dê ao cluster um nome exclusivo.

* **Tamanho** - Esta seção mostra o tipo de VM que será usado para os nós de cluster. Embora exista apenas uma opção recomendada, o link **Tamanho da Alteração** abre uma tabela com detalhes sobre esse tipo de ocorrência e um link para uma calculadora de preços.

* **Tamanho do cache por nó** - O cache de cluster está espalhado pelos números do cluster, de modo que o tamanho total do cache no cluster Avere vFXT será este tamanho multiplicado pelo número de nós.

  Configuração recomendada: Use 4 TB por nó para Standard_E32s_v3 os nós.

* **Rede virtual** - Defina uma nova rede virtual para abrigar o cluster ou selecione uma rede existente que atenda aos pré-requisitos descritos no [Plano seu sistema Avere vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Se você criar uma nova rede virtual, o controlador de cluster terá um endereço IP público para que você possa acessar a nova rede privada. Se você escolher uma rede virtual existente, o controlador de cluster será configurado sem um endereço IP público.
  >
  > Um endereço IP publicamente visível no controlador de cluster fornece acesso mais fácil para o cluster vFXT, mas gera um pequeno risco de segurança.
  >* Um endereço IP público no controlador de cluster permite que você o use como um host de atalho para se conectar ao cluster do Avere vFXT de fora da sub-rede privada.
  >* Se você não tiver um endereço IP público no controlador, você precisará de outro host de salto, uma conexão VPN ou ExpressRoute para acessar o cluster. Por exemplo, use uma rede virtual existente que já tenha uma conexão VPN configurada.
  >* Se você criar um controlador com um endereço IP público, você deverá proteger a VM do controlador com um grupo de segurança de rede. Por padrão, a implantação Avere vFXT for Azure cria um grupo de segurança de rede que restringe o acesso de entrada à porta 22 apenas para controladores com endereços IP públicos. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

  Uma nova rede virtual também é configurada com um ponto final de serviço de armazenamento para armazenamento Azure Blob e com o controle de acesso de rede bloqueado para permitir apenas IPs da sub-rede de cluster.

* **Sub-rede** - Escolha uma sub-rede ou crie uma nova.

* **Criar e usar o armazenamento blob** - Escolha o **verdadeiro** para criar um novo contêiner Azure Blob e configure-o como armazenamento back-end para o novo cluster Avere vFXT. Essa opção também cria uma nova conta de armazenamento no grupo de recursos do cluster e cria um ponto final de serviço de armazenamento da Microsoft dentro da sub-rede de cluster.
  
  Se você fornecer uma rede virtual existente, ela deve ter um ponto final de serviço de armazenamento antes de criar o cluster. (Para obter mais informações, leia [O sistema Avere vFXT](avere-vfxt-deploy-plan.md).)

  Defina este campo como **false** se não quiser criar um novo contêiner. Neste caso, você deve anexar e configurar o armazenamento depois de criar o cluster. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções.

* **(Nova) Conta de armazenamento** - Se criar um novo contêiner Azure Blob, digite um nome para a nova conta de armazenamento.

## <a name="validation-and-purchase"></a>Validação e compra

A página três resume a configuração e valida os parâmetros. Após o sucesso da validação, verifique o resumo e clique no botão **OK.**

> [!TIP]
> Você pode salvar as configurações de criação deste cluster clicando no modelo de download e no link **parâmetros** ao lado do botão **OK.** Essas informações podem ser úteis se você precisar criar um cluster semelhante mais tarde - por exemplo, para criar um cluster de substituição em um cenário de recuperação de desastres. (Leia [a orientação de recuperação de desastres](disaster-recovery.md) para saber mais.)

![Terceira página do modelo de implantação – validação](media/avere-vfxt-deploy-3.png)

A página quatro fornece os termos de uso e links para informações de privacidade e preços.

Digite qualquer informação de contato ausente, em seguida, clique no botão **Criar** para aceitar os termos e criar o cluster Avere vFXT for Azure.

![Quarta página do modelo de implantação – termos e condições, botão criar](media/avere-vfxt-deploy-4.png)

A implantação do cluster leva de 15 a 20 minutos.

## <a name="gather-template-output"></a>Coletar a saída de modelo

Quando o modelo Avere vFXT termina de criar o cluster, ele produz informações importantes sobre o novo cluster.

> [!TIP]
> Certifique-se de copiar o **endereço IP** de gerenciamento da saída do modelo. Você precisa desse endereço para administrar o cluster.

Para encontrar as informações:

1. Vá para o grupo de recursos do seu controlador de cluster.

1. No lado esquerdo, clique em **Implantações** e, em seguida, em **microsoft-avere.vfxt-template**.

   ![Página do portal do grupo de recursos com implantações selecionadas à esquerda e microsoft-avere.vfxt-template sendo exibido em uma tabela embaixo do nome da implantação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **Saídas**. Copie os valores em cada um dos campos.

   ![produz a página mostrando os valores SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP em campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Próximas etapas

Agora que o cluster está sendo executado e você conhece seu endereço IP de gerenciamento, [conecte-se à ferramenta de configuração de cluster](avere-vfxt-cluster-gui.md).

Use a interface de configuração para personalizar seu cluster, incluindo essas tarefas de configuração:

* [Habilitar o suporte](avere-vfxt-enable-support.md)
* [Adicionar armazenamento](avere-vfxt-add-storage.md) (se necessário)
