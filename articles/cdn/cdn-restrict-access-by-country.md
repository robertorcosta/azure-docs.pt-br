---
title: Restringir o conteúdo do CDN do Azure por país/região | Microsoft Docs
description: Saiba como restringir o acesso por país/região ao seu conteúdo CDN do Azure usando o recurso de filtragem geográfica.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 75c422d456f2509ce478e2609a6509f78a6eb31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593423"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Restringir o conteúdo do CDN do Azure por país/região

## <a name="overview"></a>Visão geral
Quando um usuário solicita o conteúdo, por padrão, o conteúdo é exibido, independentemente da localização do usuário que fez a solicitação. No entanto, em alguns casos, você pode querer restringir o acesso ao seu conteúdo por país/região. Com o recurso *de geofiltragem,* você pode criar regras sobre caminhos específicos no ponto final do CDN para permitir ou bloquear conteúdo em países/regiões selecionados.

> [!IMPORTANT]
> **A CDN Standard do Azure dos perfis da Microsoft** não oferecem suporte baseado no caminho da filtragem geográfica.
> 

## <a name="standard-profiles"></a>Perfis Standard
Os procedimentos nesta seção são apenas para perfis **CDN Standard do Azure da Akamai** e **CDN Standard do Azure da Verizon**. 

Para perfis**CDN Premium do Azure da Verizon**, você deve usar o portal **Gerenciar** para ativar a filtragem geográfica. Para obter mais informações, consulte os [perfis CDN Premium do Azure da Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definir o caminho do diretório
Para acessar o recurso de filtragem geográfica, selecione o ponto de extremidade da CDN no portal e, em seguida, selecione **Filtragem Geográfica** em CONFIGURAÇÕES no menu esquerdo. 

![Filtragem geográfica Standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Na caixa **PATH**, especifique o caminho relativo para o local ao qual os usuários terão permissão ou acesso negado. 

É possível aplicar filtragem geográfica a todos os arquivos com uma barra (/) ou selecionar pastas específicas, especificando caminhos de diretórios (por exemplo, */imagens/*). Adicionalmente, é possível aplicar filtragem geográfica a um único arquivo (por exemplo */imagens/city.png*). Várias regras são permitidas e, após inserir uma regra, aparecerá uma linha em branco para que você insira a próxima regra.

Por exemplo, todos os filtros de caminho de diretório a seguir são válidos:   
*/*                                 
*/Fotos/*     
*/Fotos/Estrasburgo/*     
*/Fotos/Estrasburgo/city.png*

### <a name="define-the-type-of-action"></a>Definir o tipo de ação

Na lista **AÇÃO**, selecione **Permitir** ou **Bloquear**: 

- **Permitir**: Somente usuários dos países/regiões especificados podem acessar os ativos solicitados no caminho recursivo.

- **Bloco**: Os usuários dos países/regiões especificados têm acesso negado aos ativos solicitados no caminho recursivo. Se nenhuma outra opção de filtragem de país/região tiver sido configurada para esse local, todos os outros usuários terão acesso permitido.

Por exemplo, uma regra de filtragem geográfica para bloquear o caminho */Fotos/Estrasburgo/* filtra os arquivos a seguir:     
*\//http:\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definir os países/regiões
Na lista CÓDIGOS DE **PAÍS,** selecione os países/regiões que deseja bloquear ou permita o caminho. 

Depois de terminar de selecionar os países/regiões, selecione **Salvar** para ativar a nova regra de filtragem geográfica. 

![Regras de filtragem geográfica](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Para excluir uma regra, selecione-a na lista da página **Filtragem Geográfica** e, em seguida escolha **Excluir**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Perfis CDN Premium do Azure da Verizon
Para perfis**CDN Premium do Azure da Verizon**, a interface do usuário para criar uma regra de filtragem geográfica é diferente:

1. No menu superior do perfil da CDN do Azure, selecione **Gerenciar**.

2. No portal da Verizon, selecione **HTTP Grande** e, em seguida, selecione **Filtragem por País/Região**.

    ![Filtragem geográfica Standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Selecione **Adicionar Filtro por País/Região**.

    A página **Etapa Um:** aparece.

4. Insira o caminho do diretório, selecione **Bloquear** ou **Adicionar** e, em seguida, selecione **Avançar**.

    A página **Etapa Dois:** aparece. 

5. Selecione um ou mais países/regiões da lista e selecione **Concluir** para ativar a regra. 
    
    A nova regra aparece na tabela na página **Filtragem por País/Região**.

    ![Regras de filtragem geográfica](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Limpar recursos
Na tabela de regras de filtragem de país/região, selecione o ícone excluir ao lado de uma regra para excluí-lo ou do ícone de edição para modificá-lo.

## <a name="considerations"></a>Considerações
* As alterações na configuração de filtragem geográfica não são efetivadas imediatamente:
   * Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
   * Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
   * Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos. 
 
* Esse recurso não dá suporte a caracteres curinga (por exemplo, *).

* A configuração de filtragem geográfica associada ao caminho relativo é aplicada recursivamente a esse caminho.

* Apenas uma regra pode ser aplicada para o mesmo caminho relativo. Ou seja, não é possível criar vários filtros de país/região que apontam para o mesmo caminho relativo. No entanto, como os filtros de país/região são recursivos, uma pasta pode ter vários filtros de país/região. Em outras palavras, uma subpasta de uma pasta previamente configurada pode ser atribuída a um filtro de país/região diferente.

* O recurso de geofiltragem usa códigos de países para definir os países/regiões dos quais uma solicitação é permitida ou bloqueada para um diretório seguro. Embora os perfis da Akamai e da Verizon deem suporte para a maioria dos códigos do país, existem algumas diferenças. Para obter mais informações, consulte [os códigos de país do Azure CDN](/previous-versions/azure/mt761717(v=azure.100)). 

