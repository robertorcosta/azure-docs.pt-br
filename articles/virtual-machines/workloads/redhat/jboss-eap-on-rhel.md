---
title: Início Rápido – JBoss EAP (Enterprise Application) no RHEL (Red Hat Enterprise Linux) para a VM e o Conjunto de Dimensionamento de Máquinas Virtuais do Azure
description: Como implantar aplicativos Java empresariais usando o JBoss EAP da Red Hat em uma VM do RHEL e um Conjunto de Dimensionamento de Máquinas Virtuais do Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134900"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Implantar aplicativos Java empresariais no Azure com o EAP do JBoss no Red Hat Enterprise Linux

Esses modelos de início rápido mostrarão como implantar o [EAP do JBoss](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) com o [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) em VMs (máquinas virtuais) e em Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Você terá um aplicativo Java de exemplo na implantação para validar a implantação. O JBoss EAP é uma plataforma de servidor de aplicativos de software livre. Ele fornece segurança, escalabilidade e desempenho de nível empresarial para seus aplicativos Java. O RHEL é uma plataforma de SO (sistema operacional) de software livre. Ele permite a escala de aplicativos existentes e a distribuição de tecnologias emergentes em todos os ambientes. O JBoss EAP e o RHEL incluem tudo o que é necessário para compilar, executar, implantar e gerenciar aplicativos Java empresariais em qualquer ambiente. É uma excelente solução de software livre para ambientes virtuais, locais e em nuvens privadas, públicas ou híbridas.

## <a name="prerequisite"></a>Pré-requisito 

* Uma conta do Azure com uma assinatura ativa. Para obter uma assinatura do Azure, ative seus [créditos do Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [crie uma conta gratuitamente](https://azure.microsoft.com/pricing/free-trial).

* Instalação do JBoss EAP – Você precisa ter uma conta do Red Hot com o direito do RHSM (Red Hat Subscription Management) para o JBoss EAP. Esse direito permitirá que você baixe o Red Hat testado e a versão do JBoss EAP certificada.  Se você não tiver um direito do EAP, obtenha uma [assinatura de avaliação do JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar. Para criar uma assinatura do Red Hat, acesse [portal do cliente do Red Hat](https://access.redhat.com/) e configure uma conta.
F
* [Interface de linha de comando do Azure](https://docs.microsoft.com/cli/azure/overview).

* Opções do RHEL – escolha entre PAYG (pagamento conforme o uso) ou BYOS (traga sua própria assinatura). Com a opção BYOS, ative a imagem gold do RHEL do [Red Hat Cloud Access](https://access.redhat.com/) para implantar o modelo de início rápido.

## <a name="java-ee--jakarata-ee-application-migration"></a>Migração de Aplicativos Java EE/Jakarta EE

### <a name="migrate-to-jboss-eap"></a>Fazer a migração para o JBoss EAP
O JBoss EAP 7.2 e 7.3 são implementações certificadas do Java EE (Edição Enterprise) 8 e especificações do Jacarta EE 8. O JBoss EAP fornece opções pré-configuradas para recursos como clustering de HA (alta disponibilidade), mensagens e cache distribuído. Ele também permite que os usuários escrevam, implantem e executem aplicativos usando as várias APIs e os vários serviços que o JBoss EAP fornece.  Para obter mais informações sobre o JBoss EAP, acesse [Introdução ao JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) ou [introdução ao JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Aplicativos no JBoss EAP

* Aplicativos dos Serviços Web – os serviços Web fornecem um meio padrão de interoperação entre diferentes aplicativos de software. Cada aplicativo pode ser executado em diferentes plataformas e estruturas. Esses serviços Web facilitam a comunicação interna e heterogênea do subsistema. Para saber mais, acesse [Desenvolver Aplicativos de Serviços Web no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) ou [Desenvolver Aplicativos de Serviços Web no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Aplicativos EJB (Enterprise Java Beans) – EJB 3.2 é uma API para desenvolver aplicativos Java EE e Jacarta EE distribuídos, transacionais, seguros e portáteis. O EJB usa componentes do lado do servidor chamados Enterprise Beans para implementar a lógica de negócios de um aplicativo de uma forma dissociada que incentiva a reutilização. Para saber mais, acesse [Desenvolver Aplicativos EJB no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) ou [Desenvolver Aplicativos EJB no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Aplicativos Hibernate – os desenvolvedores e administradores podem desenvolver e implantar a JPA (Java Persistence API)/aplicativos Hibernate com o JBoss EAP. O Hibernate Core é uma estrutura de mapeamento relacional de objeto para a linguagem Java. Ele fornece uma estrutura para mapear um modelo de domínio orientado a objeto para um banco de dados relacional, permitindo que os aplicativos evitem a interação direta com o banco de dados. O Hibernate Entity Manager implementa interfaces de programação e regras de ciclo de vida conforme definido pela [especificação JPA 2.1](https://www.jcp.org/en/jsr/overview). Junto com o Hibernate Annotations, esse wrapper implementa uma solução JPA completa (e autônoma) além do Hibernate Core maduro. Para saber mais sobre o Hibernate, acesse [JPA no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) ou o [Jacarta Persistence no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>MTA (Kit de Ferramentas de Migração para Aplicativos) do Red Hat
O [MTA (Kit de Ferramentas para Aplicativos) do Red Hat](https://developers.redhat.com/products/mta/overview) é uma ferramenta de migração para servidores de aplicativos Java. Use essa ferramenta para fazer a migração de outro servidor de aplicativos para o JBoss EAP. Ele funciona com plug-ins do IDE para o [IDE do Eclipse](https://www.eclipse.org/ide/), [Workspaces CodeReady do Red Hat](https://developers.redhat.com/products/codeready-workspaces/overview) e [VS Code (Visual Studio Code)](https://code.visualstudio.com/docs/languages/java) para Java.  O MTA é uma ferramenta de software livre e gratuita que vai:
* Automatizar a análise de aplicativos
* Dar suporte à estimativa de esforço
* Acelerar a migração de código
* Dar suporte à conteinerização
* Integrar-se ao Construtor de Carga de Trabalho do Azure

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrar o JBoss EAP do local para o Azure
A oferta do Azure Marketplace do JBoss EAP no RHEL será instalada e provisionada em VMs do Azure em menos de 20 minutos. Você pode acessar essas ofertas no [Azure Marketplace](https://azuremarketplace.microsoft.com/)

Esta oferta do Marketplace inclui várias combinações de versões do EAP e RHEL para dar suporte aos seus requisitos. O JBoss EAP é sempre BYOS (Traga sua Própria Assinatura), mas, para o RHEL OS, você pode escolher entre BYOS ou PAYG (Pagamento Conforme o Uso). A oferta do Azure Marketplace inclui opções de plano para o JBoss EAP no RHEL como VMs autônomas ou clusterizadas:

* JBoss EAP 7.2 na VM do RHEL 7.7 (PAYG)
* JBoss EAP 7.2 na VM do RHEL 8.0 (PAYG)
* JBoss EAP 7.3 na VM do RHEL 8.0 (PAYG)
* JBoss EAP 7.2 na VM do RHEL 7.7 (BYOS)
* JBoss EAP 7.2 na VM do RHEL 8.0 (BYOS)
* JBoss EAP 7.3 na VM do RHEL 8.0 (BYOS)

Juntamente com as ofertas do Azure Marketplace, há modelos de Início rápido disponibilizados para você começar a usar seu percurso de migração do Azure. Esses Inícios rápidos incluem o script e modelos do ARM (Azure Resource Manager) predefinidos para implantar o JBoss EAP no RHEL em várias configurações e combinações de versão. Você terá:

* LB (Load Balancer)
* IP privado para balanceamento de carga e VMs
* VNET (Rede Virtual) com uma sub-rede
* Configuração da VM (de cluster ou autônoma)
* Um aplicativo Java de exemplo

A arquitetura da solução para esses modelos inclui:

* JBoss EAP na VM do RHEL autônoma
* JBoss EAP clusterizado em várias VMs do RHEL
* JBoss EAP clusterizado usando um Conjunto de Dimensionamento de Máquinas Virtuais do Azure

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migração de Carga de Trabalho do Linux para o JBoss EAP
O Construtor de Carga de Trabalho do Azure simplificará o processo de POC (prova de conceito), avaliação e migração para aplicativos Java locais para o Azure. O Criador de Carga de Trabalho se integra à ferramenta de Descoberta das Migrações para Azure para identificar servidores do JBoss EAP. Em seguida, ele vai gerar dinamicamente o guia estratégico do Ansible para a implantação do servidor do JBoss EAP. Ele aproveita a ferramenta MTA do Red Hat para migrar servidores de outros servidores de aplicativos para o JBoss EAP. As etapas para simplificar a migração incluem:
* Avaliação – clusters do JBoss EAP que usam uma VM ou um Conjunto de Dimensionamento de Máquinas Virtuais do Azure
* Avaliação – verifica aplicativos e infraestrutura
* Configuração de infraestrutura – cria o perfil de carga de trabalho
* Implantação e teste – implanta, migra e testa a carga de trabalho
* Configuração pós-implantação – integra-se a dados, monitoramento, segurança, backup e muito mais

## <a name="server-configuration-choice"></a>Opção de configuração do servidor

Para a implantação da VM do RHEL, você pode escolher entre PAYG ou BYOS. As imagens do [Azure Marketplace](https://azuremarketplace.microsoft.com) têm como padrão PAYG. Implante uma VM do RHEL do tipo BYOS se você tiver a própria imagem do sistema operacional RHEL. Verifique se sua conta do RHSM tem direito de BYOS por meio do F Cloud Access antes de implantar as VMs ou o Conjunto de Dimensionamento de Máquinas Virtuais.

O JBoss EAP tem funcionalidades de gerenciamento avançadas e fornece funcionalidades e APIs aos aplicativos dele. Essas funcionalidades de gerenciamento são diferentes dependendo de qual modo operacional é usado para iniciar o JBoss EAP. Ele tem suporte no RHEL e no Windows Server. O JBoss EAP oferece um modo de operação de servidor autônomo para gerenciar instâncias discretas. Ele também oferece um modo operacional de domínio gerenciado para gerenciar grupos de instâncias de um ponto de controle. Observação: Os domínios gerenciados pelo JBoss EAP não têm suporte no Microsoft Azure, pois o recurso de HA (alta disponibilidade) é gerenciado pelos serviços de infraestrutura do Azure. A variável de ambiente chamada *EAP_HOME* é usada para indicar o caminho até a instalação do JBoss EAP.

**Iniciar o JBoss EAP como um servidor autônomo** – o comando a seguir é como você inicia o serviço EAP em modo autônomo.

```
$EAP_HOME/bin/standalone.sh
```
    
Esse script de inicialização usa o arquivo EAP_HOME/bin/standalone.conf para definir algumas preferências padrão, como opções de JVM. As configurações podem ser personalizadas neste arquivo. O JBoss EAP usa o arquivo de configuração standalone.xml para inicialização no modo autônomo por padrão, mas pode ser iniciado usando um modo diferente. Para detalhes sobre arquivos de configuração autônomo disponíveis e como usá-los, confira a seção [Arquivos de Configuração de Servidor Autônomo para EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) ou [Arquivos de Configuração de Servidor Autônomo para EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). Para iniciar o JBoss EAP com uma configuração diferente, use o argumento --server-config. Por exemplo:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Para obter uma lista completa de todos os argumentos de script de inicialização disponíveis e as finalidades deles, use o argumento --help ou confira a seção [Argumentos de Runtime do Servidor no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) ou [Argumentos de Runtime do Servidor no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
O JBoss EAP também pode funcionar em modo de cluster. Confira [Visão Geral de Clusters no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) ou [ Visão Geral de Clusters no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) para saber mais. As mensagens de cluster do JBoss EAP permitem o agrupamento de servidores de mensagens do JBoss EAP para compartilhar a carga de processamento de mensagens. Cada nó ativo no cluster é um servidor de mensagens do JBoss EAP ativo, que gerencia as próprias mensagens e manipula as próprias conexões.

## <a name="support-and-subscription-notes"></a>Notas de suporte e assinatura
Esses modelos de início rápido são oferecidos como: 

- O RHEL OS é oferecido como PAYG ou BYOS por meio do modelo de imagem gold do Red Hat
- O JBoss EAP é oferecido como somente BYOS

#### <a name="using-rhel-os-with-payg-model"></a>Usar o RHEL OS com o Modelo PAYG

Por padrão, esses modelos de início rápido usam a imagem PAYG do RHEL 7.7 ou 8.0 sob demanda da Galeria do Azure. As imagens PAYG terão um custo adicional de assinatura do RHEL por hora, além dos custos normais de computação, rede e armazenamento. Ao mesmo tempo, a instância será registrada em sua assinatura do Red Hat. Isso significa que você usará um de seus direitos. Essa imagem PAYG levará a uma "dupla cobrança". Você pode evitar esse problema criando a própria imagem do RHEL. Para saber mais, leia este artigo da KB (base de dados de conhecimento) do Red Hat sobre [Como provisionar uma VM do RHEL para Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Ou ative sua imagem gold do RHEL do [Red Hat Cloud Access](https://access.redhat.com/).

Confira os [Preços do Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) para obter detalhes sobre os preços da VM PAYG. Para usar o RHEL no modelo PAYG, você precisará de uma Assinatura do Azure com a forma de pagamento especificada para o [Azure Marketplace do RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) ou o [Azure Marketplace do RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Essas ofertas exigem que uma forma de pagamento seja especificada na assinatura do Azure.

#### <a name="using-rhel-os-with-byos-model"></a>Usar o RHEL OS com o modelo BYOS

Para usar o BYOS para o RHEL OS, você precisa ter uma assinatura do Red Hat válida com direitos para usar o RHEL OS no Azure. Conclua os seguintes pré-requisitos antes de implantar o modelo de início rápido:

1. Verifique se você tem os direitos do RHEL OS e do JBoss EAP anexados à sua assinatura do Red Hat.
2. Autorize sua ID da assinatura do Azure para usar imagens do RHEL BYOS. Siga a [documentação do RHSM (Red Hat Subscription Management)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) para concluir o processo, que inclui estas etapas:

    2.1 Habilitar o Microsoft Azure como um provedor no seu painel do Red Hat Cloud Access.

    2.2 Adicionar suas IDs de assinatura do Azure.

    2.3 Habilitar novos produtos para o Cloud Access no Microsoft Azure.
    
    2.4 Ativar imagens gold do Red Hat para sua assinatura do Azure. Para obter mais informações, leia o capítulo sobre [Como habilitar e manter assinaturas para o Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) para obter mais detalhes.

    2.5 Aguardar as imagens gold do Red Hat estejam disponíveis em sua assinatura do Azure. Essas imagens gold normalmente estão disponíveis em até 3 horas de envio.
    
3. Aceite os Termos e Condições do Azure Marketplace para imagens do RHEL BYOS. Você pode concluir esse processo executando comandos da CLI (Interface de linha de comando) do Azure, conforme indicado abaixo. Para obter mais informações, confira a documentação de [Imagens gold do RHEL BYOS no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) para obter mais detalhes. É importante que você esteja executando a versão mais recente da CLI do Azure.

    3.1 Iniciar uma sessão da CLI do Azure e fazer a autenticação com sua conta do Azure. Veja [Como entrar com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) para obter assistência. Verifique se você está executando a versão mais recente da CLI do Azure antes de continuar.

    3.2 Verificar se as imagens do RHEL BYOS estão disponíveis em sua assinatura executando o comando da CLI a seguir. Se você não obtiver nenhum resultado aqui, veja o nº 2 e verifique se sua assinatura do Azure está ativada para imagens do RHEL BYOS.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 Executar o comando a seguir para aceitar os Termos do Marketplace para RHEL 7.7 BYOS e RHEL 8.0 BYOS, respectivamente.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Sua assinatura agora está pronta para implantar o RHEL 7.7 ou 8.0 BYOS em máquinas virtuais do Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>Como usar o JBoss EAP com o modelo BYOS

O JBoss EAP está disponível no Azure somente por meio do modelo BYOS. Ao implantar esse modelo, você precisa fornecer suas credenciais do RHSM juntamente com a ID de Pool do RHSM com direitos do EAP válidos. Se você não tiver um direito do EAP, obtenha uma [assinatura de avaliação do JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar.

## <a name="how-to-consume"></a>Como consumir

Você pode implantar o modelo das seguintes três maneiras:

- Use o PowerShell – Implantar o modelo executando os seguintes comandos: (Confira [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) para obter informações sobre como instalar e configurar o Azure PowerShell.)

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Use a CLI do Azure – Implante o modelo executando os seguintes comandos: Confira [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter detalhes sobre como instalar e configurar a CLI do Azure.

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Usar o portal do Azure – você pode fazer a implantação no portal do Azure acessando os *modelos de início rápido do Azure* conforme descrito na seção abaixo. Quando estiver no início rápido, clique em **Fazer a implantação no Azure** ou o botão **Procurar no GitHub**.

## <a name="azure-quickstart-templates"></a>Modelos de início rápido do Azure

Você pode começar com um dos modelos de início rápido da combinação do JBoss EAP no RHEL que atende à sua meta de implantação. A seguir está a lista de modelos de início rápido disponíveis.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP no RHEL (VM autônoma)</a> – isso implantará um aplicativo Web chamado JBoss-EAP no Azure no JBoss EAP 7.2 ou 7.3 em execução na VM do RHEL 7.7 ou 8.0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP no RHEL (clusterizado, várias VMs)</a> – isso implantará um aplicativo Web chamado eap-session-replication no cluster do JBoss EAP 7.2 ou 7.3 em execução em um número "n" de VMs do RHEL 7.7 ou 8.0. O valor "n" é decidido pelo usuário. Todas as VMs são adicionadas ao pool de back-end de um Load Balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP no RHEL (clusterizado, Conjunto de Dimensionamento de Máquinas Virtuais)</a> – isso implantará um aplicativo Web chamado eap-session-replication no cluster do JBoss EAP 7.2 ou 7.3 em execução em instâncias de Conjunto de Dimensionamento de Máquinas Virtuais do RHEL 7.7 ou 8.0.

## <a name="resource-links"></a>Links de recursos:

* [Benefícios Híbridos do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Configurar um aplicativo Java para o Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP no Red Hat OpenShift no Azure](https://azure.microsoft.com/services/openshift/)
* Início Rápido do [JBoss EAP no Linux do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/quickstart-java)
* Tutorial de [Como implantar o JBoss EAP no Serviço de Aplicativo do Azure](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Saiba mais sobre o [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Saiba mais sobre o [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft Docs para o [Red Hat no Azure](https://aka.ms/rhel-docs)
* Implantar o [JBoss EAP no Conjunto de Dimensionamento de Máquinas Virtuais/na VM do RHEL do Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Implantar [JBoss EAP no Conjunto de Dimensionamento de Máquinas Virtuais/na VM do RHEL do início rápido do Azure](https://aka.ms/Quickstart-JBoss-EAP)
