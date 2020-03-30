---
title: SaaS oferece checklist de criação - Mercado Comercial para Azure
description: Os detalhes que você pode fornecer no processo de criação de ofertas SaaS. - Mercado Comercial para Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281333"
---
# <a name="saas-offer-creation-checklist"></a>Lista de verificação para criação da oferta de SaaS

O processo de criação da oferta SaaS irá levá-lo através de várias páginas. Aqui estão os detalhes que você pode fornecer em cada página, com links para saber mais sobre cada item.

Os itens que você é obrigado a fornecer ou especificar são anotados abaixo. Algumas áreas são opcionais ou têm valores padrão fornecidos, que você pode alterar conforme desejado. Você não tem que trabalhar nestas seções na ordem listada aqui.

| **Item**    | **Finalidade**  |
| :---------- | :-------------------|
| [**Nova Oferta Modal**](#new-offer-modal) | Coleta informações de identidade.  |
| [Página de configuração de oferta](#offer-setup-page) | Permite que você opte por usar os principais recursos e escolher como vender sua oferta através da Microsoft.  |
| [Página de propriedades](#properties-page) | Defina as categorias e indústrias utilizadas para agrupar sua oferta nos marketplaces, os contratos legais que suportam sua oferta e sua versão de aplicativo. |
| [Página de listagem de ofertas](#offer-listing-page) | Defina os detalhes da oferta a serem exibidas no mercado, incluindo descrições de sua oferta e ativos de marketing. |
| [Página de pré-visualização](#preview-page) | Defina uma Audiência de Pré-Visualização limitada para lançar sua oferta antes de publicar sua oferta ao vivo para o público mais amplo do mercado. |
| [Página de configuração técnica de oferta](#technical-configuration-page)  | Só disponível se você optar por vender a oferta através da Microsoft. Defina os detalhes técnicos (caminho de URL, webhook, ID do inquilino e ID do aplicativo) usados para se conectar à sua oferta. |
| [**Novo Plano Modal**](#plan-identity-modal) | Coleta informações de identidade do plano.  |
| [Página de listagem de planos](#plan-listing-page)  | Só disponível se você optar por vender a oferta através da Microsoft. Defina os detalhes utilizados para listar o Plano no mercado.  |
| [Página de disponibilidade de preços & de planejamento](#plan-pricing--availability-page)  | Só disponível se você optar por vender a oferta através da Microsoft.  Reúne as características do negócio (modelo de preços), público e disponibilidade de mercado para cada plano (versão) de sua oferta.  |
| [Página de listagem da unidade de teste](#test-drive-listing-page)  | Só disponível se você selecionar para oferecer um test drive para sua oferta. Defina os detalhes usados para listar o test drive no mercado.  |
| Página de configuração técnica da unidade de teste  | Só disponível se você selecionar para oferecer um test drive para sua oferta. Defina os detalhes técnicos para a demonstração (ou "test drive") que permitirá que os clientes experimentem sua oferta antes de se comprometerem a comprá-la.  |
| [Página de Revisão e Publicação](#review-and-publish-page)  | Selecione as alterações que deseja publicar, veja o status de cada página e forneça notas à equipe de certificação.  |


## <a name="new-offer-modal"></a>Novo modal de oferta 

As primeiras informações que você será solicitada a fornecer são um ID e um alias para sua oferta. 

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| ID da oferta  | Necessário, não pode ser mudado após a criação. Máximo de 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. |
| Oferecer alias  | Obrigatórios. |

## <a name="offer-setup-page"></a>Página de configuração de oferta

A página de configuração da oferta é onde você pode optar por diferentes canais e movimentos de venda, bem como declarar o uso de recursos-chave, como test drive e leads de clientes. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------|  
| Você gostaria de vender através da Microsoft?  | Obrigatórios. Padrão: Sim |
| Como você quer que os potenciais clientes interajam com a listagem de ofertas? (Chamada à ação)  | Obrigatório se não vender através da Microsoft. Padrão: Avaliação gratuita, Opções: "Obtê-lo agora", "Avaliação gratuita", "Entrar em contato comigo". |
| URL da Avaliação  | Obrigatório se "Avaliação gratuita" for selecionada, pois a forma como os clientes devem interagir com a listagem da oferta. |
| Oferecer URL  | Obrigatório se "Get it Now" for selecionado, pois a forma como os clientes devem interagir com a listagem de ofertas |
| Canais  | Opcional. Padrão: Não optei pelo canal CSP (revendedor).  |
| Test drive | Opcional. Padrão: Sem test drive ativado.  |
| Tipo de Test Drive | Necessário se habilitado um test drive. Padrão: Nenhum selecionado. Opções: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logic app, Power BI.  |
| Gerenciamento de chumbo - conecte-se a um sistema de CRM | Obrigatório se vender através da Microsoft, ou se a listagem oferece como "Entre em contato comigo". Padrão: nenhum sistema de CRM conectado. Opções de CRM: tabela Azure, blob Azure, Dynamics CRM online, ponto final dos HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Página Propriedades

A página de propriedades é onde você define as categorias e indústrias usadas para agrupar sua oferta nos marketplaces, os contratos legais que suportam sua oferta e sua versão do aplicativo. Certifique-se de fornecer detalhes completos e precisos sobre sua oferta nesta página, para que ela seja exibida adequadamente e oferecida ao conjunto certo de clientes. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------|  
| Categoria e subcategoria | Necessário 1 e máximo 3. Padrão: Nenhum selecionado. |
| Indústrias e subindústrias | Opcional. max 2 Indústrias L1 e no máximo 2 subindústrias dentro de cada indústria L1, Padrão: Nenhuma selecionada |
| Versão do aplicativo  | Opcional. Padrão: Nenhum. |
| Use o Contrato Padrão  | Opcional. Padrão: não selecionado.  | |
| Termos de uso  | Necessário se o Contrato Padrão não for selecionado.  |

## <a name="offer-listing-page"></a>Página de listagem de ofertas

A página de listagem é onde você fornece o texto e as imagens que os clientes vêem ao visualizar a listagem da sua oferta no mercado. 

| **Nome do campo**    | **Observações**   |
| :---------------- | :-----------| 
| Nome  | Necessário, no máximo 50 chars. |
| Resumo  | Necessário, no máximo 100 chars. | 
| Descrição  | Necessário, no máximo 3000 chars. |
| Instruções de introdução  | Necessário, no máximo 3000 chars. |
| Instruções de introdução  | Necessário, no máximo 3000 chars. |
| Palavras-chave para pesquisa  | Opcionais, recomendados, max 3 palavras-chave. |
| URL da política de privacidade  | Obrigatórios. |
| URL de materiais de marketing do programa CSP  | Opcional. |
| Links úteis Título + URL  | Opcional. |
| Título de documentos de suporte + arquivo  | Obrigatório, min 1 e máximo 3. Deve ser formato de arquivo PDF. |
| Capturas de tela  | Necessário, captura de tela min 1 e máximo 5; quatro ou mais recomendado. Deve ser 1280 X 720 em formato PNG. |
| Logotipos de loja (Pequeno, Médio, Grande, Largo, Herói)  | Necessário (48 X 48) e Grande (216 X 216); outros tamanhos opcionais, mas recomendados: Médio (90 x 90), Largo (255 x 115), Herói (815 x 290). Deve estar no formato PNG. |
| Nome dos vídeos + URL + miniatura  | Opcional, recomendado, max 4 vídeos. Miniatura deve ser 1280 x 720 no formato PNG. O vídeo deve ser hospedado no YouTube ou Vimeo. |
| Contatos (Programa CSP, Engenharia, Suporte)  | É necessário contato de engenharia e suporte (Nome, e-mail e número de telefone); CSP Programa contato opcional, mas recomendado. |
| URL do suporte  | Obrigatórios. |

## <a name="preview-page"></a>Página Visualização

A página de pré-visualização é onde você especifica o público para ter acesso à sua pré-visualização de oferta, para verificar se a oferta atende a todos os seus requisitos antes de entrar em operação. 

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| E-mail AAD/MSA + descrição | Obrigatório, min 1 e máximo 10 se inserido manualmente, ou até 20 se carregar um arquivo CSV. |

## <a name="technical-configuration-page"></a>Página de configuração técnica 

A página de configuração técnica é onde você especifica os detalhes técnicos usados pela Microsoft para se conectar à sua oferta. Esta página não é visível para você se você decidiu não vender através da Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| URL da página de aterrissagem | Exigido se vender através da Microsoft. |
| Webhook de conexão | Exigido se vender através da Microsoft. |
| ID do locatário do Azure AD | Exigido se vender através da Microsoft. |
| ID do aplicativo do Azure AD | Exigido se vender através da Microsoft. |

## <a name="plan-identity-modal"></a>Planejar modal de identidade

As primeiras informações que você deve fornecer são um nome e um ID para o seu Plano. Esta página não é visível para você se você decidiu não vender através da Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| ID do plano  | Exigido se vender através da Microsoft. Não pode ser mudado após a criação. Máximo de 50 caracteres e deve consistir apenas em caracteres minúsculos, alfanuméricos, traços ou sublinhados. |
| Nome do Plano  | Exigido se vender através da Microsoft. Deve ser único em todos os planos da oferta. Max 50 caracteres. |

## <a name="plan-listing-page"></a>Página de listagem de planos

A página de listagem do plano é onde você fornece o texto para os clientes verem ao visualizar o plano no mercado. Esta página não é visível para você se você decidiu não vender através da Microsoft.

| **Nome do campo**    | **Observações**   |  
| :---------------- | :-----------| 
| Descrição do plano   | Exigido se vender através da Microsoft. Max 500 caracteres. | |

## <a name="plan-pricing--availability-page"></a>Página de disponibilidade de preços & de planejamento

A página de preços e disponibilidade do plano é onde você define as características do negócio, o público e a disponibilidade de mercado para cada plano (versão) de sua oferta. Esta página não é visível para você se você decidiu não vender através da Microsoft.

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Disponibilidade de mercado  | Obrigatório, min 1 e máximo 141. |
| Modelo de preços  | Obrigatórios. Padrão: Taxa fixa. Opções: Taxa fixa, por usuário. |
| Assentos mínimos e máximos  | Opcional, disponível apenas se o modelo de preços baseado em assento for selecionado. |
| Termo de Faturamento  | Obrigatórios. Padrão: Mensal. Opções: Mensal, Anual. |
| Price  | USD exigido por mês, se o termo de faturamento mensal for selecionado; ou USD por ano, se o termo de faturamento anual for selecionado. |
| Audiência do Plano  | Opcional. Padrão: Plano público. Opções: Público, Privado por ID de inquilino |
| Audiência do Plano Restrito (ID do inquilino + descrição)  | Exigido se plano privado selecionado. Min 1 e no máximo 10 ids de inquilino se inseridos manualmente. Max 20000 se importação de arquivos CSV. |

## <a name="test-drive-listing-page"></a>Página de listagem do Test Drive

Só disponível se você selecionar para oferecer um test drive para sua oferta. Defina os detalhes usados para listar o test drive no mercado.

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Descrição  | Obrigatórios. |
| Nome manual do usuário + arquivo  | Necessário, máximo 1 doc. Deve ser formato PDF. |
| Nome do vídeo, URL + miniatura  | Opcional, recomendado. Miniatura deve ser 533 x 324 no formato JPGP ou PNG. O vídeo deve ser hospedado no YouTube ou Vimeo. |

## <a name="review-and-publish-page"></a>Revisar e publicar página

| **Nome do campo**    | **Observações**   | 
| :---------------- | :-----------| 
| Notas para certificação  | Opcional. |

## <a name="next-steps"></a>Próximas etapas

- [Criar uma oferta de SaaS](./create-new-saas-offer.md)
