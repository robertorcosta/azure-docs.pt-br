---
title: Arquiteturas para implantar aplicativos Oracle em Máquinas Virtuais do Azure | Microsoft Docs
description: Arquiteturas de aplicativos para implantar aplicativos Oracle, incluindo E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft em máquinas virtuais Microsoft Azure com bancos de dados no Azure ou na Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: mimckitt
ms.custom: ''
ms.openlocfilehash: 20e751b322d06ac176ee5c634d92e0efe874baac
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263294"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arquiteturas para implantar aplicativos Oracle no Azure

A Microsoft e a Oracle trabalharam juntas para permitir que os clientes implantem aplicativos Oracle, como Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft na nuvem. Com a introdução da [interconectividade](configure-azure-oci-networking.md) de rede privada de visualização entre o Microsoft Azure e o Oracle Cloud Infrastructure (OCI), os aplicativos Oracle agora podem ser implantados no Azure com seus bancos de dados back-end no Azure ou OCI. Os aplicativos Oracle também podem ser integrados ao Azure Active Directory, permitindo que você configure o login único para que os usuários possam entrar no aplicativo Oracle usando suas credenciais do Azure Active Directory (Azure AD).

O OCI oferece várias opções de banco de dados Oracle para aplicativos Oracle, incluindo DBaaS, Exadata Cloud Service, Oracle RAC e Infrastructure-as-a-Service (IaaS). Atualmente, o Banco de Dados Autônomo não é um back-end suportado para aplicativos Oracle.

Existem [várias opções](oracle-overview.md) para implantar aplicativos Oracle no Azure, incluindo de forma altamente disponível e segura. O Azure também oferece [imagens VM de banco de dados Oracle](oracle-vm-solutions.md) que você pode implantar se optar por executar seus aplicativos Oracle inteiramente no Azure.

As seções a seguir descrevem as recomendações de arquitetura da Microsoft e da Oracle para implantar o Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft em uma configuração entre nuvens ou inteiramente no Azure. A Microsoft e a Oracle testaram esses aplicativos e confirmaram que o desempenho atende aos padrões estabelecidos pela Oracle para esses aplicativos.

## <a name="architecture-considerations"></a>Considerações de arquitetura

Os aplicativos Oracle são compostos por vários serviços, que podem ser hospedados nas mesmas ou múltiplas máquinas virtuais no Azure e opcionalmente no OCI. 

As instâncias de aplicação podem ser configuradas com pontos finais privados ou públicos. A Microsoft e a Oracle recomendam a criação de um *VM host de bastião* com um endereço IP público em uma sub-rede separada para o gerenciamento do aplicativo. Em seguida, atribua apenas endereços IP privados para as outras máquinas, incluindo o nível de banco de dados. 

Ao configurar um aplicativo em uma arquitetura de nuvem cruzada, o planejamento é necessário para garantir que o espaço de endereço IP na rede virtual Do Zure não se sobreponha ao espaço de endereço IP privado na rede de nuvem virtual OCI.

Para maior segurança, configure grupos de segurança de rede em um nível de sub-rede para garantir que apenas o tráfego em portas específicas e endereços IP seja permitido. Por exemplo, máquinas no nível intermediário só devem receber tráfego de dentro da rede virtual. Nenhum tráfego externo deve atingir diretamente as máquinas de nível intermediário.

Para alta disponibilidade, você pode configurar instâncias redundantes dos diferentes servidores no mesmo conjunto de disponibilidade ou diferentes zonas de disponibilidade. As zonas de disponibilidade permitem que você atinja um SLA de 99,99% de tempo de atividade, enquanto os conjuntos de disponibilidade permitem que você atinja um SLA de tempo de atividade de 99,95% na região. As arquiteturas de amostra mostradas neste artigo são implantadas em duas zonas de disponibilidade.

Ao implantar um aplicativo usando a interconexão entre nuvens, você pode continuar a usar um circuito ExpressRoute existente para conectar seu ambiente Azure à sua rede local. No entanto, você precisa de um circuito ExpressRoute separado para a interconexão com o OCI do que aquele que se conecta à sua rede local.

## <a name="e-business-suite"></a>Suíte E-Business

O Oracle E-Business Suite (EBS) é um conjunto de aplicativos, incluindo O Gerenciamento da Cadeia de Suprimentos (SCM) e o CRM (Customer Relationship Management, gerenciamento de relacionamento com o cliente). Para aproveitar o portfólio de banco de dados gerenciado da OCI, o EBS pode ser implantado usando a interconexão entre o Microsoft Azure e o OCI. Nesta configuração, os níveis de apresentação e aplicativo são executados no Azure e o nível de banco de dados no OCI, conforme ilustrado no diagrama de arquitetura a seguir (Figura 1).

![Arquitetura de nuvem cruzada do E-Business Suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Arquitetura de nuvem cruzada do E-Business Suite* 

Nesta arquitetura, a rede virtual no Azure está conectada a uma rede de nuvem virtual em OCI usando a interconexão entre nuvens. O nível de aplicativo é configurado no Azure, enquanto o banco de dados é configurado em OCI. Recomenda-se implantar cada componente em sua própria sub-rede com grupos de segurança de rede para permitir o tráfego apenas de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O seguinte diagrama (Figura 2) é um exemplo deste padrão arquitetônico:

![Arquitetura somente para E-Business Suite Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Arquitetura somente para E-Business Suite Azure*

As seções a seguir descrevem os diferentes componentes em um nível alto.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Nível de aplicação (intermediário)

O nível de aplicação está isolado em sua própria sub-rede. Existem várias máquinas virtuais configuradas para tolerância a falhas e fácil gerenciamento de patches. Essas VMs podem ser suportadas por armazenamento compartilhado, que é oferecido por Arquivos Azure NetApp e Ultra SSDs. Esta configuração permite uma implantação mais fácil de patches sem tempo de inatividade. As máquinas no nível de aplicação devem ser enfrentadas por um balanceador de carga pública para que as solicitações ao nível de aplicação EBS sejam processadas mesmo que uma máquina no nível esteja offline devido a uma falha.

### <a name="load-balancer"></a>Balanceador de carga

Um balanceador de carga do Azure permite que você distribua tráfego em várias instâncias de sua carga de trabalho para garantir alta disponibilidade. Neste caso, um balanceador de carga pública é configurado, porque os usuários podem acessar o aplicativo EBS pela web. O balanceador de carga distribui a carga para ambas as máquinas no nível médio. Para maior segurança, permita que o tráfego somente de usuários que acessam o sistema a partir de sua rede corporativa usando uma VPN local-para-site ou ExpressRoute e grupos de segurança de rede.

### <a name="database-tier"></a>Camada de banco de dados

Esse nível hospeda o banco de dados Oracle e é separado em sua própria sub-rede. Recomenda-se adicionar grupos de segurança de rede que só permitem tráfego do nível de aplicativo para o nível de banco de dados na porta de banco de dados 1521 específica da Oracle.

A Microsoft e a Oracle recomendam uma configuração de alta disponibilidade. A alta disponibilidade no Azure pode ser alcançada configurando dois bancos de dados Oracle em duas zonas de disponibilidade com o Oracle Data Guard, ou usando o Oracle Database Exadata Cloud Service no OCI. Ao usar o Oracle Database Exadata Cloud Service, seu banco de dados é implantado em duas sub-redes. Você também pode configurar o Oracle Database em VMs em OCI em dois domínios de disponibilidade com o Oracle Data Guard.


### <a name="identity-tier"></a>Nível de identidade

O nível de identidade contém o EBS Asserter VM. O EBS Asserter permite sincronizar identidades do Oracle Identity Cloud Service (IDCS) e do Azure AD. O EBS Asserter é necessário porque o EBS não suporta protocolos de logon únicos como o SAML 2.0 ou o OpenID Connect. O EBS Asserter consome o token de conexão OpenID (gerado pelo IDCS), valida-o e, em seguida, cria uma sessão para o usuário no EBS. 

Embora essa arquitetura mostre a integração do IDCS, o acesso unificado do Azure AD e o logon único também podem ser habilitados com o Oracle Access Manager com o Oracle Internet Directory ou o Oracle Unified Directory. Para obter mais informações, consulte os whitepapers sobre [a implantação do Oracle EBS com a Integração IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) ou a implantação do Oracle [EBS com integração OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Para alta disponibilidade, recomenda-se que você implante servidores redundantes do EBS Asserter em várias zonas de disponibilidade com um balanceador de carga na frente dele.

Uma vez que sua infra-estrutura esteja configurada, o E-Business Suite pode ser instalado seguindo o guia de instalação fornecido pela Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

O JD Edwards EnterpriseOne da Oracle é um conjunto integrado de aplicativos de software abrangente de planejamento de recursos corporativos. É um aplicativo de vários níveis que pode ser configurado com um backend de banco de dados Oracle ou SQL Server. Esta seção discute detalhes sobre a implantação do JD Edwards EnterpriseOne com um back-end de banco de dados Oracle no OCI ou no Azure.

Na arquitetura recomendada a seguir (Figura 3), as camadas de administração, apresentação e meio são implantadas na rede virtual no Azure. O banco de dados é implantado em uma rede de nuvem virtual no OCI.

Assim como no E-Business Suite, você pode configurar um nível de bastião opcional para fins administrativos seguros. Use o host VM bastião como um servidor de salto para acessar as instâncias de aplicativo e banco de dados.

![JD Edwards EnterpriseOne arquitetura de nuvem cruzada](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: Arquitetura transversal JD Edwards EnterpriseOne*

Nesta arquitetura, a rede virtual no Azure está conectada à rede de nuvem virtual em OCI usando a interconexão entre nuvens. O nível de aplicativo é configurado no Azure, enquanto o banco de dados é configurado em OCI. Recomenda-se implantar cada componente em sua própria sub-rede com grupos de segurança de rede para permitir o tráfego apenas de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O seguinte diagrama (Figura 4) é um exemplo deste padrão arquitetônico:

![JD Edwards EnterpriseOne Arquitetura somente azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: Arquitetura somente para JD Edwards EnterpriseOne Azure*

As seções a seguir descrevem os diferentes componentes em um nível alto.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Nível administrativo

Como o nome sugere, esse nível é usado para tarefas administrativas. Você pode esculpir uma sub-rede separada para o nível administrativo. Os serviços e servidores neste nível são usados principalmente para instalação e administração do aplicativo. Portanto, instâncias únicas desses servidores são suficientes. Instâncias redundantes não são necessárias para a alta disponibilidade de sua aplicação.

Os componentes deste nível são os seguintes:
    
 - **Servidor de provisionamento** - Este servidor é usado para implantação de ponta a ponta dos diferentes componentes do aplicativo. Ele se comunica com as instâncias nos outros níveis, incluindo as instâncias no nível de banco de dados, sobre a porta 22. Ele hospeda o Console do Gerenciador de Servidores para JD Edwards EnterpriseOne.
 - **Servidor de implantação** - Este servidor é principalmente necessário para a instalação do JD Edwards EnterpriseOne. Durante o processo de instalação, este servidor atua como o repositório central para arquivos e pacotes de instalação necessários. O software é distribuído ou implantado para outros servidores e clientes deste servidor.
 - **Cliente de desenvolvimento** - Este servidor contém componentes executados em um navegador da Web, bem como aplicativos nativos.

### <a name="presentation-tier"></a>Camada de apresentação

Esse nível contém vários componentes, como AIS (Application Interface Services, Application Development Framework ( ADF) e Java Application Servers (JAS). Os servidores neste nível se comunicam com os servidores no nível intermediário. Eles são encabeçados por um balanceador de carga que direciona o tráfego para o servidor necessário com base no número da porta e na URL em que o tráfego é recebido. Recomenda-se que você implante várias instâncias de cada tipo de servidor para alta disponibilidade.

A seguir estão os componentes neste nível:
    
- **Application Interface Services (AIS)** - O servidor AIS fornece a interface de comunicação entre os aplicativos corporativos móveis JD Edwards EnterpriseOne e JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** - O JAS recebe solicitações do balanceador de carga e passa-o para o nível intermediário para executar tarefas complicadas. O JAS tem a capacidade de executar lógica de negócios simples.
- **BI Publisher Server (BIP)** - Este servidor apresenta relatórios com base nos dados coletados pelo aplicativo JD Edwards EnterpriseOne. Você pode projetar e controlar como o relatório apresenta os dados com base em diferentes modelos.
- **Business Services Server (BSS)** - O BSS permite a troca e interoperabilidade de informações com outros aplicativos Oracle.
- **RTE (Real-Time Events Server, servidor de eventos em tempo real)** - O RTE Server permite configurar notificações para sistemas externos sobre transações que ocorrem no sistema JDE EnterpriseOne. Ele usa um modelo de assinante e permite que sistemas de terceiros se inscrevam em eventos. Para carregar solicitações de equilíbrio para ambos os servidores RTE, certifique-se de que os servidores estão em um cluster.
- **Servidor ADF (Application Development Framework)** - O servidor ADF é usado para executar aplicativos JD Edwards EnterpriseOne desenvolvidos com o Oracle ADF. Isso é implantado em um servidor Oracle WebLogic com tempo de execução ADF.

### <a name="middle-tier"></a>Camada intermediária

O nível intermediário contém o servidor lógico e o servidor de lote. Neste caso, ambos os servidores estão instalados na mesma máquina virtual. No entanto, para cenários de produção, recomenda-se que você implante servidor lógico e servidor em lote em servidores separados. Vários servidores são implantados no nível intermediário em duas zonas de disponibilidade para maior disponibilidade. Um balanceador de carga do Azure deve ser criado e esses servidores devem ser colocados em seu pool de backend para garantir que ambos os servidores estejam ativos e processando as solicitações.

Os servidores do nível intermediário recebem solicitações dos servidores na camada de apresentação e apenas do balanceador de carga pública. As regras do grupo de segurança da rede devem ser configuradas para negar o tráfego de qualquer endereço que não seja a sub-rede de nível de apresentação e o balanceador de carga. Uma regra NSG também pode ser criada para permitir o tráfego na porta 22 do host bastião para fins de gerenciamento. Você pode ser capaz de usar o balanceador de carga pública para carregar solicitações de equilíbrio entre as VMs no nível intermediário.

Os dois componentes a seguir estão no nível intermediário:

- **Logic server** - Contenha a lógica de negócios ou funções de negócios.
- **Servidor em lote** - Usado para processamento em lote

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>Peoplesoft

O pacote de aplicativos PeopleSoft da Oracle contém software para recursos humanos e gestão financeira. O conjunto de aplicativos é multi-hierárquico e os aplicativos incluem sistemas de gerenciamento de recursos humanos (HRMS), gerenciamento de relacionamento com clientes (CRM), gestão de finanças e cadeia de suprimentos (FSCM) e gerenciamento de desempenho corporativo (EPM).

Recomenda-se que cada camada do conjunto de software seja implantada em sua própria sub-rede. Um banco de dados Oracle ou o Microsoft SQL Server são necessários como banco de dados back-end para o aplicativo. Esta seção discute detalhes sobre a implantação do PeopleSoft com um backend de banco de dados Oracle.

A seguir está uma arquitetura canônica para implantar o pacote de aplicativos PeopleSoft em uma arquitetura entre nuvens (Figura 5).

![Arquitetura de nuvem cruzada PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Arquitetura de nuvem cruzada PeopleSoft*

Nesta arquitetura de exemplo, a rede virtual no Azure está conectada à rede de nuvem virtual em OCI usando a interconexão entre nuvens. O nível de aplicativo é configurado no Azure, enquanto o banco de dados é configurado em OCI. Recomenda-se implantar cada componente em sua própria sub-rede com grupos de segurança de rede para permitir o tráfego apenas de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O seguinte diagrama (Figura 6) é um exemplo deste padrão arquitetônico:

![Arquitetura somente peopleSoft Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Arquitetura somente para PessoasSoft Azure*

As seções a seguir descrevem os diferentes componentes em um nível alto.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Camada de aplicativo

O nível do aplicativo contém instâncias dos servidores de aplicativos PeopleSoft, servidores web PeopleSoft, pesquisa elástica e PeopleSoft Process Scheduler. Um balanceador de carga do Azure é configurado para aceitar solicitações de usuários que são roteadas para o servidor apropriado no nível do aplicativo.

Para obter alta disponibilidade, considere configurar instâncias redundantes de cada servidor no nível do aplicativo em diferentes regiões de disponibilidade. O balanceador de carga Do Azure pode ser configurado com vários pools de back-end para direcionar cada solicitação para o servidor certo.

### <a name="peopletools-client"></a>Cliente PeopleTools

O PeopleTools Client é usado para executar atividades de administração, como desenvolvimento, migração e upgrade. Como o PeopleTools Client não é necessário para obter alta disponibilidade do seu aplicativo, servidores redundantes do PeopleTools Client não são necessários.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Próximas etapas

Use [scripts Terraform](https://github.com/microsoft/azure-oracle) para configurar aplicativos Oracle no Azure e estabelecer conectividade entre nuvens com o OCI.

Para obter mais informações e whitepapers sobre o OCI, consulte a documentação do [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
