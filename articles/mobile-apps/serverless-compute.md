---
title: Construa um back-end de aplicativo móvel sem servidor com funções do Azure e outros serviços
description: Conheça os serviços de computação usados para construir um back-end de aplicativo móvel sólido e sem servidor.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240138"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Construa componentes back-end móveis com serviços de computação
Todo aplicativo móvel precisa de um back-end responsável pelo armazenamento de dados, lógica de negócios e segurança. O gerenciamento da infra-estrutura para hospedar e executar o código back-end requer que você dimensione, provisione e dimensione vários servidores. Você também tem que gerenciar as atualizações do sistema operacional e o hardware envolvido e aplicar patches de segurança. Em seguida, você precisa monitorar todos esses componentes de infra-estrutura para obter desempenho, disponibilidade e tolerância a falhas. 

A arquitetura sem servidor é útil para este tipo de cenário porque você não tem servidores para gerenciar e nenhum sistema operacional ou atualizações de software ou hardware relacionadas para gerenciar. A arquitetura sem servidor economiza tempo e custo do desenvolvedor, o que significa mais tempo para comercializar e energia focada na construção de aplicativos.

## <a name="benefits-of-compute"></a>Benefícios do computação
- A abstração de servidores significa que não há necessidade de se preocupar com hospedagem, patches e segurança, o que permite que você se concentre apenas no código.
- O dimensionamento instantâneo e eficiente garante que os recursos sejam provisionados automaticamente ou demanda em qualquer escala que você precisar.
- Alta disponibilidade e tolerância a falhas.
- Micro-faturamento garante que você só é cobrado para quando seu código está realmente em execução.
- O código é executado na nuvem escrito no idioma de sua escolha.

Use os seguintes serviços para habilitar recursos de computação sem servidor em seus aplicativos móveis.

## <a name="azure-functions"></a>Funções do Azure
[Azure Functions](https://azure.microsoft.com/services/functions/) é uma experiência computacional orientada a eventos que você pode usar para executar seu código, escrito na linguagem de programação de sua escolha, sem se preocupar com servidores. Você não precisa gerenciar o aplicativo ou a infra-estrutura para executá-lo. As funções são dimensionadas demanda, e você paga apenas pelo tempo que seu código é executado. As funções do Azure são uma ótima maneira de implementar uma API para um aplicativo móvel. Eles são fáceis de implementar e manter e são acessíveis através de HTTP.

**Principais características**
- Orientado por eventos e escalável onde você pode usar gatilhos e vinculações para definir quando uma função é invocada e a quais dados ela se conecta.
- Traga suas próprias dependências porque as funções suportam NuGet e NPM, para que você possa usar suas bibliotecas favoritas.
- Segurança integrada para que você possa proteger funções acionadas pelo HTTP com provedores OAuth, como Azure Active Directory, Facebook, Google, Twitter e Microsoft Account.
- Integração simplificada com [diferentes serviços](/azure/azure-functions/functions-overview) e softwares do Azure como serviço (SaaS).
- Desenvolvimento flexível para que você possa codificar suas funções no portal Azure ou configurar integração contínua e implantar seu código através do GitHub, Azure DevOps Services e outras ferramentas de desenvolvimento suportadas.
- O tempo de execução das funções é de código aberto e está disponível no [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Experiência de desenvolvimento aprimorada onde você pode codificar, testar e depurar localmente usando seu editor preferido ou interface web fácil de usar com monitoramento com ferramentas integradas e recursos DevOps incorporados.
- Variedade de linguagens de programação e opções de hospedagem para desenvolvimento, como C#, Node.js, Java, JavaScript ou Python.
- O modelo de preços por uso significa que você paga apenas pelo tempo gasto executando seu código.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação de Funções do Azure](/azure/azure-functions/)
- [Guia do desenvolvedor do Azure Functions](/azure/azure-functions/functions-reference)
- [Inícios rápidos](/azure/azure-functions/functions-create-first-function-vs-code)
- [Amostras](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Serviço de aplicativo do Azure
Com [o Azure App Service,](https://azure.microsoft.com/services/app-service/)você pode criar e hospedar aplicativos web e APIs RESTful na linguagem de programação de sua escolha sem gerenciar a infra-estrutura. Ele oferece autodimensionamento e alta disponibilidade, suporta windows e Linux e permite implantações automatizadas do GitHub, Azure DevOps ou qualquer repo do Git.

**Principais características**
- Suporte a várias linguagens e estruturas para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Você também pode executar o PowerShell e outros scripts ou executáveis como serviços em segundo plano.
- Otimização do DevOps através da integração e implantação contínuas com o Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Gerencie aplicativos no Serviço de Aplicativo usando o Azure PowerShell ou a CLI (interface de linha de comando de plataforma cruzada).
- Escala global com alta disponibilidade para escalar ou desfazer manualmente ou automaticamente.
- Conexões com plataformas SaaS e dados locais para escolher entre mais de 50 conectores para sistemas corporativos como SAP, serviços SaaS, como Salesforce, e serviços de internet como o Facebook. Acesse dados locais usando conexões híbridas e Redes Virtuais Azure.
- O Azure App Service é compatível com ISO, SOC e PCI. Autenticar usuários com o Azure Active Directory ou com login para mídias sociais como Google, Facebook, Twitter e Microsoft. Crie Restrições de endereço IP e gerencie identidades de serviço.
- Modelos de aplicativos para escolher entre uma extensa lista de modelos de aplicativos no Azure Marketplace, como WordPress, Joomla e Drupal.
- A integração do Visual Studio com ferramentas dedicadas no Visual Studio agiliza o trabalho de criação, implantação e depuração.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação do Serviço de Aplicativos Do Azure](/azure/app-service/)
- [Inícios rápidos](/azure/app-service/app-service-web-get-started-dotnet)
- [Amostras](/azure/app-service/samples-cli)
- [Tutoriais](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure
O [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/) gerencia seu ambiente Kubernetes hospedado. O AKS acelera e facilita a implantação e o gerenciamento de aplicativos em contêineres sem conhecimento de orquestração de contêineres. Também elimina o ônus das operações e manutenção em andamento. Provisões, upgrades e dimensionamento de recursos da AKS demanda, sem que seus aplicativos sejam desligados.

**Principais características**
- Migrar facilmente os aplicativos existentes para contêineres e executar dentro de AKS.
- Simplifique a implantação e o gerenciamento de aplicativos baseados em microsserviços.
- DevOps seguros para AKS para alcançar o equilíbrio entre velocidade e segurança e fornecer código mais rápido em escala.
- Dimensione com facilidade usando aks e azure container instances para provisionar pods dentro de Instâncias de contêiner que começam em segundos.
- Implantar e gerenciar dispositivos IoT demanda.
- Treine modelos de aprendizado de máquina com o uso de ferramentas como TensorFlow e KubeFlow.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação do Serviço Azure Kubernetes](/azure/aks/)
- [Inícios rápidos](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriais](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure
[O Azure Container Instances](https://azure.microsoft.com/services/container-instances/) é uma ótima solução para qualquer cenário que possa operar em contêineres isolados, como aplicativos simples, automação de tarefas e trabalhos de construção. Desenvolva aplicativos rapidamente sem gerenciar VMs.

**Principais características**
- Tempos de inicialização rápidos como Instâncias de Contêiner podem iniciar contêineres no Azure em segundos, sem a necessidade de provisionar e gerenciar VMs.
- Conectividade IP pública e nome DNS personalizado.
- A segurança no nível do hipervisor que garante que sua aplicação esteja tão isolada em um contêiner quanto em uma VM.
- Tamanhos personalizados para uma utilização ideal, permitindo especificações exatas de núcleos de CPU e memória. Você paga com base no que precisar e é cobrado por segundo, assim, é possível ajustar seus gastos eficientemente com base em suas necessidades reais.
- Armazenamento persistente para recuperar e persistir o estado. As instâncias de contêiner oferecem montagem direta dos compartilhamentos de arquivos do Azure.
- Contêineres Linux e Windows agendados com a mesma API.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação de Instâncias de Contêineres do Azure](/azure/container-instances/)
- [Inícios rápidos](/azure/container-instances/container-instances-quickstart-portal)
- [Amostras](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriais](/azure/container-instances/container-instances-tutorial-prepare-app)