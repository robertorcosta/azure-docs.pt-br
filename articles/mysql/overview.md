---
title: Visão geral – Banco de Dados do Azure para MySQL
description: Saiba mais sobre o Banco de Dados do Azure para MySQL, um serviço de banco de dados relacional no Microsoft Cloud baseado no MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: c5758fa367ac17ceb687212b4073fd2c1a5c18f6
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206410"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é o Banco de Dados do Azure para MySQL?

O Banco de Dados do Azure para MySQL é um serviço de banco de dados relacional no Microsoft Cloud com base no mecanismo de banco de dados [MySQL Community Edition](https://www.mysql.com/products/community/) (disponível sob a licença GPLv2), versões 5.6, 5.7 e 8.0. O Banco de Dados do Azure para MySQL fornece:

- Alta disponibilidade interna sem nenhum custo adicional.
- Desempenho previsível, com preços pré-pagos inclusivos.
- Escale conforme necessário em segundos.
- Seguro para proteger dados confidenciais em repouso e em movimento.
- Backups automáticos e restauração pontual por até 35 dias.
- Segurança e conformidade de nível empresarial.

Esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e em acelerar seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos ao gerenciamento de máquinas virtuais e de infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, tudo isso sem precisar aprender novas habilidades.

![Diagrama conceitual do Banco de Dados do Azure para MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados do Azure para MySQL relacionados a desempenho, escalabilidade e gerenciabilidade, com links para explorar os detalhes. Veja estes inícios rápidos para começar:

- [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para ver diversos exemplos da CLI do Azure, consulte:

- [Exemplos da CLI do Azure para o Banco de Dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e a escala em segundos
O serviço Banco de Dados do Azure para MySQL oferece várias camadas de serviço: Básico, Uso Geral e Otimizado para Memória. Cada camada oferece diferentes níveis de desempenho e recursos para dar suporte a cargas de trabalho de banco de dados leves e pesadas. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja  [Tipos de preço](concepts-service-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Como você decide quando aumentar e reduzir? Use os recursos internos de alerta e monitoramento de desempenho, em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução dos vCores com base nas suas necessidades de desempenho atuais ou previstas. Veja [Alertas](howto-alert-on-metric.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
O SLA (Contrato de Nível de Serviço) de disponibilidade de 99,99% do Azure, que é líder do setor e é alimentado por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução de forma ininterrupta. Com cada servidor do Banco de Dados do Azure para MySQL, você tira proveito dos recursos internos de segurança, tolerância a falhas e proteção de dados que, em outras situações, seria necessário comprar ou projetar, criar e gerenciar. Com o Banco de Dados do Azure para MySQL, você pode usar a restauração pontual para recuperar um servidor para um estado anterior, com alcance de até 35 dias.

## <a name="secure-your-data"></a>Proteja seus dados
Os serviços de banco de dados do Azure têm uma tradição de segurança de dados que o Banco de Dados do Azure para MySQL mantém, com recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam a monitorar atividades. Visite a [Central de Confiabilidade do Azure](https://www.microsoft.com/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure. Para obter mais informações sobre os recursos de segurança do Banco de Dados do Azure para MySQL, confira a [visão geral de segurança](concepts-security.md).

## <a name="contacts"></a>Contatos
Para quaisquer dúvidas ou sugestões que você possa ter sobre como trabalhar com o Banco de Dados do Azure para MySQL, envie um e-mail para o Banco de Dados do Azure para a Equipe do MySQL ([@Ask Azure DB for MySQL ](mailto:AskAzureDBforMySQL@service.microsoft.com)). Esse endereço de email não é um alias de suporte técnico.

Além disso, considere os seguintes pontos de contato, conforme apropriado:

- Para entrar em contato com o Suporte do Azure, [crie um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Próximas etapas
Agora que leu uma introdução ao Banco de Dados do Azure para MySQL e respondeu à pergunta "O que é o Banco de Dados do Azure para MySQL?", você está pronto para:

- Consultar a página de preços para ver comparações de custo e calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece com a criação do seu primeiro servidor. [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Crie seu primeiro aplicativo usando sua linguagem preferida:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
