---
title: Introdução ao Azure Security Benchmark
description: Introdução do Security Benchmark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945329"
---
# <a name="azure-security-benchmark-introduction"></a>Introdução do benchmark de segurança do Azure

Você pode ter vários anos ou até décadas de experiência com computação local. Você sabe como proteger essas implantações; mas a nuvem é diferente. Como você sabe se suas implantações na nuvem estão seguras? Quais são as diferenças entre as práticas de segurança para sistemas locais e implantações em nuvem?

Há uma grande coleção de white papers, melhores práticas, arquiteturas de referência, orientação web, ferramentas de código aberto, soluções comerciais, feeds de inteligência e muito mais, que podem ser usados para ajudar a proteger a nuvem. Qual opção você deve usar? O que você pode fazer para obter um nível aceitável de segurança na nuvem? 

Uma das melhores maneiras de proteger suas implantações na nuvem é focar nas recomendações de benchmark de segurança na nuvem. Recomendações de benchmark, para garantir qualquer serviço, começam com uma compreensão fundamental do risco de cibersegurança e como gerenciá-lo. Em seguida, você pode usar esse entendimento adotando recomendações de segurança de benchmark do seu provedor de serviços em nuvem para ajudar a selecionar configurações específicas de configuração de segurança em seu ambiente. 

O Azure Security Benchmark inclui uma coleção de recomendações de segurança de alto impacto que você pode usar para ajudar a proteger a maioria dos serviços que você usa no Azure. Você pode pensar nessas recomendações como "geral" ou "organizacional", pois elas são aplicáveis à maioria dos serviços do Azure. As recomendações do Azure Security Benchmark são então personalizadas para cada serviço do Azure, e essa orientação personalizada está contida em artigos de recomendações de serviço. 

A documentação do Azure Security Benchmark especifica controles de segurança e recomendações de serviço.

- **Controles de segurança**: As recomendações do Azure Security Benchmark são categorizadas por controles de segurança. Os controles de segurança representam requisitos de segurança agnósticos de alto nível, como segurança de rede e proteção de dados. Cada controle de segurança tem um conjunto de recomendações e instruções de segurança que ajudam a implementar essas recomendações. 
- **Recomendações de serviço :** Quando disponível, as recomendações de benchmark para os serviços do Azure incluirão recomendações do Azure Security Benchmark que são adaptadas especificamente para esse serviço. 

Os termos "Controle", "Benchmark" e "Linha de Base" são usados frequentemente na documentação do Azure Security Benchmark e é importante entender como o Azure usa esses termos. 

| Termo | Descrição | Exemplo |
|--|--|--|
| Control | Um **controle** é uma descrição de alto nível de um recurso ou atividade que precisa ser abordado e não é específico para uma tecnologia ou implementação. | A Proteção de Dados é um dos controles de segurança. Este controle contém ações específicas que precisam ser abordadas para ajudar a garantir que os dados sejam protegidos. |
| Parâmetro de comparação | Um **benchmark** contém recomendações de segurança para uma tecnologia específica, como o Azure. As recomendações são categorizadas pelo controle a que pertencem. | O benchmark Azure Security compreende as recomendações de segurança específicas da plataforma Azure  |
| Linha de base | Uma **linha de base** são os requisitos de segurança para uma organização. Os requisitos de segurança são baseados em recomendações de benchmark. Cada organização decide quais recomendações de benchmark incluir em sua linha de base. | A empresa Contoso cria sua linha de base de segurança, optando por exigir recomendações específicas no Azure Security Benchmark. |

Damos as boas-vindas ao seu feedback sobre o Azure Security Benchmark! Solicitamos atenção no uso das informações fornecidas. Se você preferir compartilhar sua entrada de forma mais privada com a equipe do Azure Security Benchmark, você é bem-vindo para preencher o formulário emhttps://aka.ms/AzSecBenchmark 
