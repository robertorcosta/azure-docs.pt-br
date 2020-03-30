---
title: Versões do layout da página
titleSuffix: Azure AD B2C
description: Histórico da versão do layout da página para personalização de IA em políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183968"
---
# <a name="page-layout-versions"></a>Versões do layout da página

Os pacotes de layout de página são atualizados periodicamente para incluir correções e melhorias em seus elementos de página. O registro de alterações a seguir especifica as alterações introduzidas em cada versão.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Página auto-afirmada`selfasserted`( )
  - Adicionado suporte para controles de exibição em políticas [personalizadas.](display-controls.md)

## <a name="120"></a>1.2.0

- Todas as páginas
  -  Correções de acessibilidade
  - Agora você pode `data-preload="true"` adicionar o atributo [em suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carregamento para CSS e JavaScript.
    - Carregar arquivos CSS vinculados ao mesmo tempo que seu modelo HTML para que ele não 'cintile' entre o carregamento dos arquivos.
    - Controle a ordem `script` em que suas tags são buscadas e executadas antes da carga da página.
  - O campo `type=email` de e-mail é agora e os teclados móveis fornecerão as sugestões corretas
  - Suporte para traduzir chrome
- Páginas unificadas e auto-afirmadas
  - Os campos de nome de usuário/e-mail e senha agora usam o elemento HTML para permitir que o `form` Edge e o Internet Explorer (IE) salvem adequadamente essas informações.

## <a name="110"></a>1.1.0

- Página de exceção (exceção global)
  - Correção de acessibilidade
  - Removeu a mensagem padrão quando não há contato da diretiva
  - CSS padrão removido
- Página MFA (multifator)
  - Botão 'Confirmar código' removido
  - O campo de entrada para o código agora só leva entrada até seis (6) caracteres
  - A página tentará verificar automaticamente o código inserido quando um código de 6 dígitos é inserido, sem que nenhum botão tenha que ser clicado
  - Se o código estiver errado, o campo de entrada é automaticamente limpo
  - Após três (3) tentativas com um código incorreto, B2C envia um erro de volta para a parte que confia
  -  Correções de acessibilidade
  - CSS padrão removido
- Página auto-afirmada (auto-afirmada)
  - Alerta de cancelamento removido
  - Classe CSS para elementos de erro
  - A lógica de erro show/hide melhorou
  - CSS padrão removido
- SSP Unificada (Unificada)
  - Adicionado me mantenha assinado no controle (KMSI)

## <a name="100"></a>1.0.0

- Versão inicial

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre como personalizar a interface do usuário de seus aplicativos em políticas personalizadas, consulte [Personalizar a interface de usuário do seu aplicativo usando uma política personalizada](custom-policy-ui-customization.md).
