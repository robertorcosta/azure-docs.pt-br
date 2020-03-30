---
title: Verifique o status de publicação da sua oferta de Marketplace Comercial
description: Verifique o status das etapas de validação, certificação e visualização necessárias para publicar uma oferta através do Mercado Comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275672"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Verifique o status de publicação da sua oferta de Marketplace Comercial

Você pode visualizar seu status atual **de publicação** na guia Visão geral da **Oferta** do portal Do [Mercado Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

Um dos indicadores de status a seguir deve ser exibido para cada oferta.

| **Status**    | **Descrição**  |
| :---------- | :-------------------|
| **Escalação** | A oferta foi criada, mas não está sendo publicada. |
| **Publicação em andamento** | A Oferta/Plano está trabalhando nas etapas do processo de publicação. |
| **Ação necessária** | Um problema crítico foi descoberto durante a certificação pela Microsoft ou qualquer uma das etapas de publicação. |
| **Visualizar** | A oferta foi certificada pela Microsoft, e agora aguarda uma verificação final pelo editor. Selecione ir ao vivo para fazer a oferta ao vivo. |
| **Ativa** | A oferta é ao vivo no mercado e pode ser vista e adquirida pelos clientes. |
| **Espera-se parar de vender** | A editora selecionou "stop sell" em oferta ou plano, mas a ação ainda não foi concluída. |
| **Não disponível no mercado** | Uma oferta/plano publicado anteriormente no mercado foi removido. |

## <a name="automated-validation"></a>Validação automatizada

O primeiro passo no processo de publicação é um conjunto de validações automatizadas. Cada etapa de validação corresponde a um recurso que você escolheu para habilitar na criação de sua oferta. Se esse recurso não foi habilitado, a validação será seguida para a próxima etapa de publicação. Cada verificação de validação deve ser concluída antes da aprovação do status de publicação.

- **Configuração de fluxo de compra de oferta (<10 min)**

Nesta etapa, garantimos que sua oferta possa ser cumprida quando comprada pelos clientes através do portal Azure. Esta etapa só é aplicável para ofertas que estão sendo vendidas através da Microsoft.

- **Validação de dados da unidade de teste (~5 min)**

Nesta etapa, validamos os dados fornecidos na seção de configuração técnica do test drive da oferta. A funcionalidade do test drive é testada e aprovada. Esta etapa só é aplicável para ofertas com um test drive ativado.

- **Provisionamento de test drive (~30 min)**

Nesta etapa, depois de validar os dados e a funcionalidade do seu test drive na etapa anterior, implantamos e replicamos instâncias do seu test drive para que eles estejam prontos para uso do cliente.  Esta etapa só é aplicável para ofertas com um test drive ativado.

- **Validação e registro de gestão de chumbo (<15 min)**

Nesta etapa, confirmamos que seu sistema de gerenciamento de leads pode receber leads de clientes com base nos detalhes fornecidos na configuração oferta. Esta etapa só é aplicável para ofertas com gerenciamento de lead ativado.

## <a name="certification"></a>Certificação

Antes de serem publicadas, as ofertas submetidas ao Mercado Comercial no Partner Center devem ser certificadas. As ofertas submetidas passam por rigorosos testes, alguns automatizados e outros manuais, incluindo uma verificação com as [políticas de certificação do Azure Marketplace.](https://docs.microsoft.com/legal/marketplace/general-policies) As submissões de ofertadevem ser marcadas elegíveis para a certificação antes de prosseguirem para o próximo passo no fluxo de publicação.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Há três níveis de validação incluídos no processo de certificação para cada oferta submetida.

- Elegibilidade empresarial de editores
- Validação de conteúdo
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade empresarial de editores

Cada tipo de oferta verifica um conjunto de critérios básicos de elegibilidade que o editor deve atender. Os critérios de elegibilidade podem incluir o status mpn do editor, competências mantidas, níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdo

Durante a validação do conteúdo, as informações inseridas quando você criou sua oferta são verificadas quanto à qualidade e relevância. Essas verificações revisarão suas entradas para os detalhes de listagem do mercado, preços, disponibilidade, planos associados, etc. Para atender aos critérios de listagem do Azure Marketplace e/ou AppSource, validaremos que sua oferta inclui:

- um título que descreve com precisão a oferta;
- descrições bem escritas que fornecem uma visão geral completa e proposta de valor;
- capturas de tela de qualidade e vídeos de acompanhamento; E
- uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdo lendo as [políticas gerais de listagem](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (pacote ou binário) passa pelas seguintes verificações.
- Escaneado para malware
- Chamadas de rede monitoradas
- Pacote analisado
- Digitalização completa da funcionalidade real da oferta

A oferta é testada em várias plataformas e versões para garantir que seja robusta.

Revise os detalhes de configuração específicos necessários para sua oferta na seção de configuração técnica deste documento.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Após a conclusão da revisão, se sua oferta foi aprovada, ela passa para o próximo passo no processo de publicação. Se sua oferta falhou em qualquer uma das verificações de listagem, técnica ou política, ou se você não for elegível para enviar uma oferta desse tipo, um relatório de falha de certificação é gerado e enviado por e-mail para você.

Este relatório contém descrições de quaisquer políticas que falharam, juntamente com notas de revisão. Revise este relatório de e-mail, resolva quaisquer problemas, faça atualizações da sua oferta quando necessário e reenvie a oferta usando o [portal do Mercado Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. (Você pode reenviar a oferta quantas vezes forem necessárias até passar pela certificação).

## <a name="preview-creation"></a>Criação de visualização

Durante a etapa **de criação do Preview,** criamos uma versão de sua oferta acessível apenas ao público que você especificou na seção Visualização de sua oferta.

>[!Note]
> Não use esse passo para dar às pessoas fora da sua organização visibilidade em uma oferta. Use a opção **Oferta Privada** em vez disso. Até o momento, sua oferta não foi totalmente testada e validada, e não está pronta para distribuição externa.

## <a name="publisher-approval"></a>Aprovação do editor

Nesta etapa, você será enviado por e-mail com um pedido para que você revise e aprove sua pré-visualização da oferta antes da etapa final de publicação.

Se você tiver selecionado para vender sua oferta através da Microsoft, você poderá testar a aquisição e a implantação de sua oferta para garantir que ela atenda aos seus requisitos durante esta fase de aprovação de visualização. Sua oferta ainda não estará disponível no mercado público. Depois de testar e aprovar essa visualização, você precisará selecionar **Go-Live** no painel Visão geral da [**oferta.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

Se você quiser fazer alterações na oferta durante esta fase de pré-visualização, você pode editar e reenviar para publicar uma nova pré-visualização. Veja o artigo [Atualizar as ofertas de marketplace existentes](#update-existing-marketplace-offers) para obter detalhes sobre mais alterações.

Se sua oferta já estiver ao vivo e disponível para o público no mercado, quaisquer atualizações que você fizer não entrarão em operação até que você selecione **Go-live** no painel Visão Geral da [**Oferta.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

### <a name="publish-offer-to-the-public"></a>Publicar oferta ao público

Faça login no Partner Center e acesse a oferta. Você será redirecionado para a página **visão geral da Oferta.** No topo desta página, você verá uma opção para **Ir ao vivo**. Selecione **Ir ao vivo,** e após a confirmação, a oferta começará a ser publicada ao público. Você receberá uma notificação por e-mail quando a oferta estiver ao vivo.

## <a name="publish"></a>Publicar

Agora que você selecionou para **ir ao vivo** com sua oferta, disponibilizando-a no mercado, há uma série de verificações finais de validação que serão realizadas para garantir que a oferta ao vivo seja configurada assim como a versão de pré-visualização da oferta.

- **Configuração de fluxo de compra de oferta (>10 min)**

Nesta etapa, garantimos que sua oferta possa ser cumprida quando comprada pelos clientes através do portal Azure. Esta etapa só é aplicável para ofertas que estão sendo vendidas através da Microsoft.

- **Validação de dados da unidade de teste (~5 min)**

Nesta etapa, validamos os dados fornecidos na seção de configuração técnica do test drive da oferta. A funcionalidade do test drive é testada e aprovada. Esta etapa só é aplicável para ofertas com um test drive ativado.

- **Provisionamento de test drive (~30 min)**

Nesta etapa, implantamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente.  Esta etapa só é aplicável para ofertas com um test drive ativado.

- **Validação e registro de gestão de chumbo (>15 min)**

Nesta etapa, confirmamos que seu sistema de gerenciamento de leads pode receber leads de clientes com base nos detalhes fornecidos na configuração oferta. Esta etapa só é aplicável para ofertas com gerenciamento de lead ativado.

- **Publicação final (>30 minutos)**

Nesta etapa, garantimos que sua oferta se torne pública no mercado.

## <a name="update-existing-marketplace-offers"></a>Atualizar as ofertas de marketplace existentes

Se você quiser fazer alterações em uma oferta que você já publicou, você precisará primeiro atualizar a oferta existente e depois publicá-la novamente.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
