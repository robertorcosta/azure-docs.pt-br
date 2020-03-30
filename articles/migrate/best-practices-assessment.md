---
title: Avaliações de práticas recomendadas na avaliação do servidor migrado do Azure
description: Dicas para criar avaliações com a Avaliação do Servidor Migrado do Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185973"
---
# <a name="best-practices-for-creating-assessments"></a>Melhores práticas para criar avaliações

As [Migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.

Este artigo resume as melhores práticas ao criar avaliações usando a ferramenta Azure Migrate Server Assessment.

## <a name="about-assessments"></a>Sobre avaliações

As avaliações criadas com o Azure Migrate Server Assessment são um instantâneo pontual dos dados. Existem dois tipos de avaliações no Azure Migrate.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho coletados | A recomendação de tamanho vm é baseada em dados de utilização de CPU e memória.<br/><br/> A recomendação do tipo de disco (HDD/SSD padrão ou discos gerenciados premium) é baseada no IOPS e no throughput dos discos on-premises.
**As-is on-premises** | Avaliações que não usam dados de desempenho para fazer recomendações. | A recomendação do tamanho da VM é baseada no tamanho da VM no local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração do tipo de armazenamento para a avaliação.

### <a name="example"></a>Exemplo
Como exemplo, se você tiver uma VM no local com quatro núcleos com 20% de utilização e memória de 8 GB com 10% de utilização, as avaliações serão as seguintes:

- **Avaliação baseada em desempenho:**
    - Identifica núcleos e memória eficazes com base na utilização do núcleo (4 x 0,20 = 0,8) e da memória (8 GB x 0,10 = 0,8).
    - Aplica o fator de conforto especificado nas propriedades de avaliação (digamos 1,3x) para obter os valores a serem usados para dimensionamento. 
    - Recomenda o tamanho de VM mais próximo no Azure que pode suportar ~1,04 núcleos (0,8 x 1,3) e ~1,04 GB (0,8 x 1,3) de memória.

- **Avaliação as-is (como no local):**
    -  Recomenda uma VM com quatro núcleos; 8 GB de memória.

## <a name="best-practices-for-creating-assessments"></a>Melhores práticas para criar avaliações

O aparelho Azure Migrate faz um perfil contínuo do ambiente no local e envia dados de metadados e desempenho para o Azure. Siga estas melhores práticas para avaliações de servidores descobertos usando um aparelho:

- **Crie avaliações como está:** Você pode criar avaliações como está imediatamente assim que suas máquinas aparecerem no portal Azure Migrate.
- **Crie uma avaliação baseada em desempenho**: Após configurar a descoberta, recomendamos que você espere pelo menos um dia antes de executar uma avaliação baseada em desempenho:
    - Coletar dados de desempenho leva tempo. Esperar pelo menos um dia garante que haja pontos de dados de desempenho suficientes antes de executar a avaliação.
    - Quando estiver executando avaliações baseadas em desempenho, certifique-se de traçar o perfil do seu ambiente durante a duração da avaliação. Por exemplo, se você criar uma avaliação com uma duração de desempenho definida para uma semana, você precisa esperar pelo menos uma semana após iniciar a descoberta, para que todos os pontos de dados sejam coletados. Se você não fizer isso, a avaliação não terá uma classificação de cinco estrelas.
- **Recalcule avaliações**: Como as avaliações são instantâneos pontuais, elas não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, você precisa recalculá-los.

Siga estas melhores práticas para avaliações de servidores importados para o Azure Migrate via . Arquivo CSV:

- **Crie avaliações como está:** Você pode criar avaliações como está imediatamente assim que suas máquinas aparecerem no portal Azure Migrate.
- **Crie uma avaliação baseada em desempenho**: Isso ajuda a obter uma melhor estimativa de custo, especialmente se você tiver capacidade de servidor superprovisionada no local. No entanto, a precisão da avaliação baseada no desempenho depende dos dados de desempenho especificados por você para os servidores. 
- **Recalcule avaliações**: Como as avaliações são instantâneos pontuais, elas não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados importados mais recentes, você precisa recalculá-los.

## <a name="best-practices-for-confidence-ratings"></a>Melhores práticas para índices de confiança

Quando você executa avaliações baseadas em desempenho, uma avaliação de confiança de 1 estrela (mais baixa) a 5 estrelas (mais alta) é atribuída à avaliação. Para usar as classificações de confiança de forma eficaz:
- A Avaliação do Servidor Migrado do Azure precisa dos dados de utilização da CPU/memória VM.
- Para cada disco conectado à VM no local, ele precisa dos dados de leitura/gravação do IOPS/throughput.
- Para cada adaptador de rede conectado à VM, ele precisa dos dados de inecto/desacoplamento da rede.

Dependendo da porcentagem de pontos de dados disponíveis para a duração selecionada, o índice de confiança para uma avaliação é fornecido conforme resumido na tabela a seguir.

   **Disponibilidade do ponto de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas


## <a name="common-assessment-issues"></a>Questões comuns de avaliação

Veja como abordar algumas questões ambientais comuns que afetam as avaliações.

###  <a name="out-of-sync-assessments"></a>Avaliações fora de sincronia

Se você adicionar ou remover máquinas de um grupo após criar uma avaliação, a avaliação criada será marcada **fora de sincronia**. Execute a avaliação novamente **(Recalcular)** para refletir as mudanças de grupo.

### <a name="outdated-assessments"></a>Avaliações desatualizadas

Se houver alterações no local em VMs que estejam em um grupo avaliado, a avaliação será marcada **desatualizada**. Para refletir as mudanças, execute a avaliação novamente.

### <a name="low-confidence-rating"></a>Baixo índice de confiança

Uma avaliação pode não ter todos os pontos de dados por uma série de razões:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você estiver criando uma *avaliação baseada em desempenho* com duração de desempenho definida para uma semana, você precisa esperar pelo menos uma semana depois de iniciar a descoberta para que todos os pontos de dados sejam coletados. Você sempre pode clicar em **Recalcular** para ver a última classificação de confiança aplicável. O índice de confiança só é aplicável quando você cria uma avaliação *baseada em desempenho.*

- Algumas VMs foram desativadas durante o período para o qual a avaliação é calculada. Se algumas VMs foram desligadas por algum tempo, a Avaliação de Servidor não poderá coletar os dados de desempenho daquele período.

- Algumas VMs foram criadas após o início da descoberta na Avaliação de Servidor. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesse caso, os dados de desempenho das novas VMs não estariam disponíveis durante todo o período e a classificação de confiança seria baixa.


## <a name="next-steps"></a>Próximas etapas

- [Saiba](concepts-assessment-calculation.md) como as avaliações são calculadas.
- [Aprenda](how-to-modify-assessment.md) a personalizar uma avaliação.
