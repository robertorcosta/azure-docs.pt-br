---
title: Lançamentos do Microsoft Security Code Analysis
description: Este artigo descreve os próximos lançamentos para a extensão Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462029"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Lançamentos e roteiros do Microsoft Security Code Analysis

A equipe de Análise de Código de Segurança da Microsoft em parceria com o Suporte ao Desenvolvedor tem o orgulho de anunciar melhorias recentes e futuras em nossa extensão MSCA. Veja o Roteiro abaixo.

![Lançamentos](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Credential Scanner v2.0: Lançado em 1 de abril de 2020

### <a name="innovations--improvements"></a>Inovações & Melhorias

- **Motor central**

   - Atualização média de desempenho de 25% com tempos de execução quase lineares
   - Pesquisa e classificação baseadas em contexto/evidência para maior precisão
   - Melhorias nas detecções gerais de senha e na lógica de correspondência para espaços reservados óbvios (por exemplo, fakePassword)

- **Cobertura** - Suporte para mais de 25 tipos secretos, incluindo o seguinte top solicitado:

   - Senha do certificado de conta de malha
   - Chave de segredo do cliente/API
   - Cabeçalho de autorização HTTP
   - Chave de acesso secreto do cliente Amazon S3
   - Token de acesso ao cliente do azure Active Directory
   - Chave mestre/API da função do azure
   - Chave de acesso power BI
   - Padrão de senha do modelo do Azure Resource Manager

- **outputs**

   - Suporte para formatos de arquivo de saída de arquivos SARIF 2.1 e CSV

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: Será lançado em abril de 2020

### <a name="improvements"></a>Aprimoramentos

- RECURSO: Atualize para o SARIF v2 final (versão 2.1.16). Isso permite o cache de resultados ao passar --hashes na linha de comando, uma melhoria significativa de desempenho ao analisar recursivamente diretórios com várias cópias de metas de varredura.
- CORREÇÃO DE ERROS: Corrigir erro de digitação em BA2021. DoNotMarkGravitableSectionsSaídaexecutável saída.
- DESEMPENHO: Elimine o carregamento do PDB para todos os modos não mistos para conjuntos gerenciados, incluindo binários il Library (antes do tempo compilado).
- CORREÇÃO NEGATIVA FALSA: Verifique se um PDB colocado ao lado de um binário realmente corresponde ao binário em análise
- RECURSO: Forneça --argumento local-símbolo-diretórios para especificar locais adicionais (locais, não-símbolo-servidor) PDB locais
- CORREÇÃO FALSA POSITIVA: Pule a análise orientada pelo PDB para o bootstrap exe nativo do núcleo .NET gerado (que não é um código controlável pelo usuário).

## <a name="whats-next-in-fy20"></a>O que vem a seguir em FY20?

- Ferramenta Java Security Analysis
- Ferramenta de análise de segurança python
- ES Lint para substituir TS Lint por TypeScript e JavaScript

## <a name="next-steps"></a>Próximas etapas

Para obter instruções sobre como embarcar e instalar a Análise de Código de Segurança da Microsoft, consulte nosso [guia de onboarding e instalação](security-code-analysis-onboard.md).

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, confira nossa [página de perguntas frequentes](security-code-analysis-faq.md).
