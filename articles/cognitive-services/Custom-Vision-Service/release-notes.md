---
title: Notas de versão – Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Obtenha as últimas informações sobre novos lançamentos da equipe de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647568"
---
# <a name="custom-vision-service-release-notes"></a>Notas de versão do Serviço de Visão Personalizada

## <a name="may-2-2019-and-may-10-2019"></a>2 de maio de 2019 e 10 de maio de 2019

- Correções de bugs e melhorias no backend

## <a name="may-23-2019"></a>23 de maio de 2019

- Experiência de UX do portal melhorada relacionada às assinaturas do Azure, facilitando a seleção de seus diretórios do Azure.

## <a name="april-18-2019"></a>18 de abril de 2019 

- Adicionada exportação de detecção de objetos para o Kit de Dev Vision AI.
- Ajustes de iu, incluindo pesquisa de projetos.

## <a name="april-3-2019"></a>3 de abril de 2019

- Aumento do limite no número de caixas delimitadoras por imagem para 200. 
- Bugfixes, incluindo atualização substancial de desempenho para modelos exportados para TensorFlow. 

## <a name="march-26-2019"></a>26 de março de 2019

- O Custom Vision Service entrou em disponibilidade geral no Azure!
- Adicionado recurso de Treinamento Avançado com um novo backend de aprendizado de máquina para melhor desempenho, especialmente em conjuntos de dados desafiadores e classificação de grãos finos. Com o treinamento avançado, você pode especificar um orçamento de tempo computado para treinamento e a Visão Personalizada identificará experimentalmente as melhores configurações de treinamento e aumento. Para iterações rápidas, você pode continuar a usar o treinamento rápido existente.
- Introduziu 3.0 APIs. Anunciado a antecipação de APIs pré-3.0 em 1º de outubro de 2019. Consulte a documentação que começa rapidamente para [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)ou [Go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) para exemplos sobre como começar.
- Substituiu "Iterações padrão" por Publicar/Despublicar nas APIs 3.0.
- Novas metas de exportação do modelo foram adicionadas. A exportação do arquivo docker foi atualizada para suportar arm para Raspberry Pi 3. O suporte à exportação foi adicionado ao [Kit Dev Vision AI.](https://visionaidevkit.com/). .
- Aumento do limite de Tags por projeto para 500 para o nível S0. Aumento do limite de imagens por projeto para 100.000 para o nível S0.
- Removido domínio adulto. Em vez disso, recomenda-se o domínio geral.
- [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) anunciados para disponibilidade geral.  

## <a name="february-25-2019"></a>25 de fevereiro de 2019

- Anunciou o fim dos projetos de Teste Limitado (projetos não associados a um recurso do Azure), à medida que a Visão Personalizada se aproxima da conclusão de sua mudança para a pré-visualização pública do Azure. A partir de 25 de março de 2019, o site CustomVision.ai só suportará a visualização de projetos associados a um recurso do Azure, como o recurso free Custom Vision. Até 1º de outubro de 2019, você ainda poderá acessar seus projetos de teste limitados existentes através das APIs de Visão Personalizada. Isso lhe dará tempo para atualizar as chaves da API para quaisquer aplicativos que você tenha escrito com o Custom Vision. Após 1º de outubro de 2019, todos os projetos de teste limitados que você não tenha movido para o Azure serão excluídos.

## <a name="january-22-2019"></a>22 janeiro de 2019

- Apoio adicionado para novas regiões azure: West US 2, East US, East US 2, West Europe, North Europe, Southeast Asia, Australia East, Central India, UK South, Japan East e North Central US. O suporte continua para o Centro-Sul dos EUA.

## <a name="december-12-2018"></a>12 de dezembro de 2018

- Suporte para exportação de modelos de Detecção de Objetos (introduzido o Domínio compacto de detecção de objetos).
- Corrigidos diversos problemas de acessibilidade para aprimorar o suporte para navegação por teclado e leitor de tela.
- Atualizações da experiência do usuário para o visualizador de imagens e melhor experiência de marcação para detecção de objetos para uma marcação mais rápida.  
- Atualização do modelo base para o Domínio de detecção de objetos para uma detecção de objetos de melhor qualidade.
- Correções de bugs.

## <a name="november-6-2018"></a>6 de novembro de 2018

- Adicionado suporte para o domínio do logotipo na Detecção de objetos.

## <a name="october-9-2018"></a>9 de outubro de 2018

- A Detecção de Objetos entra em visualização paga. Agora você pode criar projetos da Detecção de Objetos com um recurso do Azure.
- Adicionado ao site o recurso "Mover para o Azure", a fim de facilitar a atualização de um projeto de Avaliação Limitada para vincular-se a um Azure. projeto vinculado a recursos (F0 ou S0.) Você pode encontrá-lo na página Configurações para o seu produto.  
- Adicionada a exportação para ONNX 1.2, para dar suporte à versão de atualização de outubro de 2018 da ML do Windows.
Correções de bugs, inclusive para exportação ONNX com caracteres especiais.

## <a name="august-14-2018"></a>14 de agosto de 2018

- Adicionado widget "Iniciar" ao site customvision.ai para orientar os usuários através do treinamento do projeto.
- Melhorias adicionais no pipeline de aprendizado de máquina para beneficiar projetos de vários rótulos (nova camada de perda).

## <a name="june-28-2018"></a>28 de junho de 2018

- Correções de bugs & melhorias no backend.
- Classificação multiclasse habilitada, para projetos onde as imagens têm exatamente um rótulo. Em Previsões para o modo multiclasse, as probabilidades serão somadas a uma (todas as imagens são classificadas entre suas Tags especificadas).

## <a name="june-13-2018"></a>13 de junho de 2018

- Atualização UX, focada na facilidade de uso e acessibilidade.
- Melhorias no pipeline de aprendizado de máquina para beneficiar projetos de vários rótulos com um grande número de marcas.
- Corrigido o bug na exportação do TensorFlow. Habilitado o controle de versão do modelo exportado, para que as iterações possam ser exportadas mais de uma vez.

## <a name="may-7-2018"></a>7 de maio de 2018

- Introdução da versão prévia do recurso de Detecção de Objetos para projetos de Avaliação Limitada.
- Atualização para APIs 2.0
- Camada S0 expandida para até 250 marcas e 50.000 imagens.
- Melhorias significativas de back-end para o pipeline de aprendizado de máquina para projetos de classificação de imagem. Projetos treinados depois de 27 de abril de 2018 irão se beneficiar dessas atualizações.
- Adicionada a exportação de modelo para ONNX, para uso com o Windows ML.
- Adicionada a exportação de modelo para o Dockerfile. Isso permite que você baixe os artefatos para criar seus próprios contêineres do Windows ou Linux, incluindo um DockerFile, o modelo do TensorFlow e o código de serviço.
- Para modelos recentemente treinados exportados para TensorFlow nos domínios Geral (Compacto) e Ponto de referência (Compacto), os [Valores Médios agora são (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), para manter a consistência em todos os projetos.

## <a name="march-1-2018"></a>1º de março de 2018

- Inseriu pré-visualização paga e a bordo no portal Azure. Os projetos agora podem ser anexados aos recursos do Azure com a camada F0 (Gratuita) ou S0 (Standard). Introdução dos projetos da camada S0, que permite até 100 marcas e 25.000 imagens.
- O back-end é alterado para o parâmetro de normalização/pipeline de aprendizado de máquina. Isso dará aos clientes um melhor controle das compensações de precisão e recall ao ajustar o Limite de Probabilidade. Como parte dessas alterações, o Limite de Probabilidade padrão no portal do CustomVision.ai foi definido para ser 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Exportar para Android (TensorFlow) adicionado, além da exportação previamente lançada para iOS (CoreML.) Isso permite que a exportação de um modelo compacto treinado seja executada offline em um aplicativo.
- Adicionados os domínios “compactos” Varejo e Ponto de referência para habilitar a exportação de modelo para esses domínios.
- Lançamento das versões [API de Treinamento 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [API de Previsão 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas oferecem suporte à exportação de modelo, uma nova operação de Previsão que não salva as imagens em “Previsões” e introdução das operações em lote para a API de Treinamento.
- Ajustes de experiência do usuário, incluindo a capacidade de ver qual domínio foi usado para treinar uma iteração.
- Atualização do [SDK e exemplo de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).