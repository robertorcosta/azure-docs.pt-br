---
title: Use serviços de Plataforma como Serviço (PaaS) em Laboratórios Azure DevTest
description: Saiba como usar os serviços de Plataforma como Serviço (Pass) no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169201"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Use serviços de Plataforma como Serviço (PaaS) em Laboratórios Azure DevTest
O PaaS é suportado no DevTest Labs através do recurso de ambientes. Os ambientes no DevTest Labs são suportados por modelos pré-configurados do Azure Resource Manager em um repositório git. Os ambientes podem conter recursos PaaS e IaaS. Eles permitem criar sistemas complexos que podem incluir recursos do Azure, como máquinas virtuais, bancos de dados, redes virtuais e aplicativos web, que são personalizados para trabalhar em conjunto. Esses modelos permitem uma implantação consistente e um gerenciamento aprimorado de ambientes usando o controle de código-fonte. 

Um sistema de configuração adequada permite os seguintes cenários: 

- Desenvolvedores para ter ambientes independentes e múltiplos
- Testando diferentes configurações assíncronamente
- Integração em gasodutos de encenação e produção sem qualquer alteração de modelo
- Ter máquinas de desenvolvimento e ambientes dentro do mesmo laboratório melhora a facilidade de gerenciamento e relatórios de custos.  

O provedor de recursos Do DevTest Labs cria recursos em nome do usuário do laboratório, portanto, nenhuma permissão extra precisa ser dada ao usuário do laboratório para permitir o uso dos recursos do PaaS. A imagem a seguir mostra um cluster de malha de serviço como um ambiente no laboratório.

![Cluster de malha de serviço como um ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obter mais informações sobre a configuração de ambientes, consulte [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md). O DevTest Labs tem um repositório público de modelos do Azure Resource Manager que você pode usar para criar ambientes sem ter que se conectar a uma fonte externa do GitHub por si mesmo. Para obter mais informações sobre ambientes públicos, consulte [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Em grandes organizações, as equipes de desenvolvimento normalmente fornecem ambientes como ambientes de teste personalizados/isolados. Pode haver ambientes que devem ser usados por todas as equipes dentro de uma unidade de negócios ou de uma divisão. O grupo de TI pode querer fornecer seus ambientes que possam ser usados por todas as equipes da organização.  

## <a name="customizations"></a>Personalizações

#### <a name="sandbox"></a>Área restrita 
O proprietário do laboratório pode personalizar ambientes de laboratório para alterar a função do usuário de **leitor** para **contribuinte** dentro do grupo de recursos. Esse recurso está na página **Configurações** do laboratório a **Configuração e políticas** do laboratório. Essa mudança de função permite que o usuário adicione ou remova recursos dentro desse ambiente. Se você quiser restringir ainda mais o acesso, use as políticas do Azure. Essa funcionalidade permite personalizar os recursos ou a configuração sem o acesso ao nível de assinatura.

#### <a name="custom-tokens"></a>Tokens personalizados
Existem algumas informações de laboratório personalizadas que estão fora do grupo de recursos e são específicas para ambientes que o modelo pode acessar. Eis algumas delas: 

- Identificação da rede de laboratório
- Location
- Conta de armazenamento na qual os arquivos do Gerenciador de recursos são armazenados. 
 
#### <a name="lab-virtual-network"></a>Rede virtual de laboratório
Os [ambientes de conexão ao](connect-environment-lab-virtual-network.md) artigo de rede virtual do laboratório descrevem `$(LabSubnetId)` como modificar o modelo do Gerenciador de Recursos para usar o token. Quando um ambiente é `$(LabSubnetId)` criado, o token é substituído pela primeira marca de sub-rede onde o uso na opção **de criação de máquina virtual** é definido como **verdadeiro**. Ele permite que nosso ambiente use redes criadas anteriormente. Se você quiser usar os mesmos modelos do Gerenciador de `$(LabSubnetId)` recursos em ambientes em teste como estágio e produção, use como um valor padrão em um parâmetro de modelo do Gerenciador de recursos. 

#### <a name="environment-storage-account"></a>Conta de armazenamento de ambiente
O DevTest Labs suporta o uso de [modelos aninhados do Gerenciador](../azure-resource-manager/templates/linked-templates.md)de Recursos . O artigo[[Implantar azure Resource Manager para testar ambientes'](deploy-nested-template-environments.md) explica como usar `_artifactsLocation` e `_artifactsLocationSasToken` tokens para criar um modelo URI para um gerenciador de recursos na mesma pasta ou em uma pasta aninhada do modelo principal. Para obter mais informações sobre esses dois tokens, consulte a seção de artefatos de **implantação** do [Azure Resource Manager – Best Practices Guide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Experiência do Usuário

## <a name="developer"></a>Desenvolvedor
Os desenvolvedores usam o mesmo fluxo de trabalho para criar uma VM para criar um ambiente específico. Eles selecionam o ambiente versus a imagem da máquina e digitam as informações necessárias exigidas pelo modelo. Cada desenvolvedor com um ambiente permite a implantação de alterações e uma melhor depuração de loop interno. O ambiente pode ser criado a qualquer momento usando o modelo mais recente.  Esse recurso permite que os ambientes sejam destruídos e recriados para ajudar a reduzir o tempo de inatividade de ter que criar manualmente o sistema ou se recuperar de testes de falhas.  

### <a name="testing"></a>Testes
Os ambientes DevTest Labs permitem testes independentes de códigos e configurações específicos de forma assíncrona. A prática comum é configurar o ambiente com o código a partir da solicitação de puxar individual e iniciar qualquer teste automatizado. Uma vez que o teste automatizado tenha sido concluído, qualquer teste manual pode ser executado contra o ambiente específico. Geralmente esse processo é feito como parte do pipeline ci/cd. 

## <a name="management-experience"></a>Experiência de Gestão

### <a name="cost-tracking"></a>Controle de custos
O recurso de rastreamento de custos inclui recursos do Azure nos diferentes ambientes como parte da tendência geral de custos. O custo por recursos não desfaz os diferentes recursos dentro do ambiente, mas exibe o ambiente como um único custo.

### <a name="security"></a>Segurança
Uma assinatura Azure configurada corretamente com o DevTest Labs pode [limitar o acesso aos recursos do Azure apenas através do laboratório](devtest-lab-add-devtest-user.md). Com ambientes, um proprietário de laboratório pode permitir que os usuários acessem os recursos do PaaS com configurações aprovadas sem permitir o acesso a quaisquer outros recursos do Azure. No cenário em que os usuários de laboratório personalizam ambientes, o proprietário do laboratório pode permitir o acesso dos colaboradores. O acesso ao contribuinte permite que o usuário do laboratório adicione ou remova o recurso do Azure apenas dentro do grupo de recursos gerenciado. Permite um rastreamento e gerenciamento mais fáceis versus permitir que o contribuinte do usuário acesse a assinatura.

### <a name="automation"></a>Automação
A automação é um componente-chave para um ecossistema eficaz e em larga escala. A automação é necessária para lidar com o gerenciamento ou rastreamento de vários ambientes através de assinaturas e laboratórios.

### <a name="cicd-pipeline"></a>Gasoduto CI/CD
Os serviços paaS no DevTest Labs podem ajudar a melhorar o pipeline de CI/CD, tendo implantações focadas onde o acesso é controlado pelo laboratório.

## <a name="next-steps"></a>Próximas etapas
Veja os seguintes artigos para obter detalhes sobre ambientes: 

- 
- [Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Crie um ambiente com cluster de malha de serviço autônomo no Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Conecte um ambiente à rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integre ambientes em seus pipelines CI/CD do Azure DevOps](integrate-environments-devops-pipeline.md)
 





