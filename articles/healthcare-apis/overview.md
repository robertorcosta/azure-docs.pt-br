---
title: O que é a API do Azure para FHIR? - API do Azure para FHIR
description: A API do Azure para FHIR permite a troca rápida de dados por meio de APIs do FHIR. Ingira, gerencie e mantenha as informações de integridade protegidas PHI com um serviço de nuvem gerenciado.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "84819926"
---
# <a name="what-is-azure-api-for-fhirreg"></a>O que é a API do Azure para FHIR&reg;?

A API do Azure para FHIR permite a troca rápida de dados por meio de APIs do FHIR® (Fast Healthcare Interoperability Resources), respaldada por uma oferta de PaaS (plataforma como serviço) gerenciada na nuvem. Ela torna fácil para qualquer pessoa que trabalhe com os dados de integridade para ingerir, gerenciar e manter [PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) (informações de saúde protegidas) na nuvem: 

- Serviço FHIR gerenciado, provisionado na nuvem em minutos 
- Ponto de extremidade baseado em FHIR® de nível empresarial no Azure para acesso a dados e armazenamento no formato FHIR®
- Alto desempenho, baixa latência
- Gerenciamento seguro de PHI (dados de saúde protegidos) em um ambiente de nuvem em conformidade
- SMART on FHIR para implementações móveis e da Web
- Controlar seus dados em escala com RBAC (Controle de Acesso Baseado em Função)
- Acompanhamento de log de auditoria para acesso, criação, modificação e leituras em cada armazenamento de dados

A API do Azure para FHIR permite que você crie e implante um serviço FHIR em poucos minutos para aproveitar a escala elástica da nuvem.  Você paga apenas pela taxa de transferência e pelo armazenamento de que precisa. Os serviços do Azure que capacitam a API do Azure para FHIR são projetados para desempenho rápido, independentemente do tamanho dos conjuntos de dados que você está gerenciando.

A API do FHIR e o armazenamento de dados em conformidade permitem que você se conecte e interaja com segurança com qualquer sistema que utilize APIs do FHIR.  A Microsoft assume as operações, a manutenção, as atualizações e os requisitos de conformidade na oferta de PaaS para que você possa liberar seus recursos operacionais e de desenvolvimento. 

O vídeo a seguir apresenta uma visão geral da API do Azure para FHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Como aproveitar o poder dos seus dados com o FHIR

O setor de saúde está transformando rapidamente os dados de saúde para o padrão emergente [FHIR&reg;](https://hl7.org/fhir) (Fast Healthcare Interoperability Resources). O FHIR possibilita um modelo de dados robusto e extensível com semântica e troca de dados padronizadas que permite que todos os sistemas que usam o FHIR funcionem juntos.  Transformar seus dados no FHIR permite que você conecte rapidamente as fontes de dados existentes, como os sistemas de registro eletrônico de saúde ou os bancos de dados de pesquisa. O FHIR também permite a troca rápida de dados em implementações modernas de desenvolvimento para a Web e para dispositivos móveis. O mais importante é que o FHIR pode simplificar a ingestão de dados e acelerar o desenvolvimento com ferramentas de análise e machine learning.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Gerenciar com segurança os dados de saúde na nuvem

A API do Azure para FHIR permite a troca de dados por meio de APIs consistentes, RESTful e FHIR com base na especificação HL7 FHIR. Apoiado por uma oferta de PaaS gerenciada no Azure, ela também fornece um ambiente escalonável e seguro para o gerenciamento e o armazenamento de dados de PHI (informações de saúde protegidas) no formato FHIR nativo.  

### <a name="free-up-your-resources-to-innovate"></a>Libere seus recursos para inovar

Você poderia investir recursos criando e executando seu próprio serviço FHIR, mas, com a API do Azure para FHIR, a Microsoft assume a carga de trabalho de operações, manutenção, atualizações e requisitos de conformidade, permitindo que você libere seus recursos operacionais e de desenvolvimento.

### <a name="enable-interoperability-with-fhir"></a>Habilitar a interoperabilidade com o FHIR

Usar a API do Azure para FHIR permite que você se conecte a qualquer sistema que usa as APIs do FHIR para funções de leitura, gravação, pesquisa e outras.  Ela pode ser usada como uma ferramenta poderosa para consolidar, normalizar e aplicar o machine learning a dados clínicos de registros eletrônicos de saúde, painéis clínicos e pacientes, programas de monitoramento remoto ou bancos de dado fora do seu sistema que têm APIs do FHIR.

### <a name="control-data-access-at-scale"></a>Controlar o acesso a dados em escala

Você controla seus dados. O RBAC (Controle de Acesso Baseado em Função) permite que você gerencie como os dados são armazenados e acessados.  Fornecendo maior segurança e reduzindo a carga de trabalho administrativa, você determina quem tem acesso aos conjuntos de dados que você cria dependendo das definições de função criadas para o seu ambiente.  

### <a name="audit-logs-and-tracking"></a>Logs e acompanhamento de auditoria 

Use logs de auditoria internos para controlar rapidamente para onde seus dados estão indo. Acompanhe o acesso, a criação, a modificação e as leituras em cada armazenamento de dados.

### <a name="secure-your-data"></a>Proteja seus dados

Proteja seu PHI com inteligência de segurança incomparável.  Seus dados são isolados em uma instância exclusiva do banco de dados por API e protegidos com failover de várias regiões. A API do Azure para FHIR implementa uma defesa aprofundada em camadas e proteção avançada contra ameaças para seus dados.  

## <a name="applications-for-a-fhir-service"></a>Aplicativos para um serviço FHIR

Os servidores FHIR são ferramentas essenciais para a interoperabilidade dos dados de saúde.  A API do Azure para FHIR é projetada como uma API e um serviço que você pode criar, implantar e começar a usar rapidamente.  À medida que o padrão FHIR se expande na área de saúde, os casos de uso continuam crescendo, mas alguns aplicativos cliente iniciais em que a API do Azure para FHIR é útil são apresentados abaixo: 

- **Inicialização/IoT e desenvolvimento de aplicativos:**  os clientes que desenvolvem um aplicativo centrado no paciente ou provedor (móvel ou Web) podem aproveitar a API do Azure para FHIR como um serviço de back-end totalmente gerenciado. A API do Azure para FHIR fornece um recurso valioso no qual os clientes podem gerenciar e trocar dados em um ambiente de nuvem seguro desenvolvido para dados de saúde, usar as diretrizes de implementação SMART on FHIR e permitir que sua tecnologia seja utilizada por todos os sistemas de provedor (por exemplo, a maioria dos EHRs habilitou APIs de leitura do FHIR).   
- **Ecossistemas de saúde:**  embora EHRs exista como a principal "fonte de verdade" em muitas configurações clínicas, não é incomum que os provedores tenham vários bancos de dados que não estão conectados entre si ou que armazenam dados em formatos diferentes.  Utilizar a API do Azure para FHIR como um serviço que fica sobre esses sistemas permite padronizar os dados no formato FHIR.  Isso ajuda a habilitar a troca de dados em vários sistemas com um formato de dados consistente. 

- **Pesquisa:** os pesquisadores de saúde considerarão o padrão FHIR em geral e a API do Azure para FHIR úteis, pois normalizam dados em um modelo de dados do FHIR comum e reduzem a carga de trabalho para machine learning e compartilhamento de dados.
A troca de dados por meio da API do Azure para FHIR fornece logs de auditoria e controles de acesso que ajudam a controlar o fluxo de dados e quem tem acesso a quais tipos de dados. 

## <a name="fhir-from-microsoft"></a>FHIR da Microsoft

As funcionalidades de FHIR da Microsoft estão disponíveis em duas configurações:

* API do Azure para FHIR – uma oferta de PaaS no Azure, facilmente provisionada no portal do Azure e gerenciada pela Microsoft.
* Servidor FHIR para Azure – um projeto de software livre que pode ser implantado em sua assinatura do Azure, disponível no GitHub em https://github.com/Microsoft/fhir-server.

Para casos de uso que exigem estender ou personalizar o servidor FHIR ou acesso aos serviços subjacentes, como o banco de dados, sem passar pelas APIs do FHIR, os desenvolvedores devem escolher o servidor FHIR de software livre para o Azure.   Para a implementação de uma API do FHIR pronta para produção e serviço de back-end em que os dados persistentes só devem ser acessados por meio da API do FHIR, os desenvolvedores devem escolher a API do Azure para FHIR

## <a name="next-steps"></a>Próximas etapas

Para começar a trabalhar com a API do Azure para FHIR, siga o início rápido de 5 minutos para implantar a API do Azure para FHIR.

>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)

FHIR é uma marca registrada da HL7, usada com permissão da HL7.
