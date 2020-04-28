---
title: Versões do layout da página
titleSuffix: Azure AD B2C
description: Histórico de versão de layout de página para personalização de interface do usuário em políticas personalizadas.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183968"
---
# <a name="page-layout-versions"></a>Versões do layout da página

Os pacotes de layout de página são atualizados periodicamente para incluir correções e aprimoramentos em seus elementos de página. O log de alterações a seguir especifica as alterações introduzidas em cada versão.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Página autodeclarada (`selfasserted`)
  - Adicionado suporte para [controles de exibição](display-controls.md) em políticas personalizadas.

## <a name="120"></a>1.2.0

- Todas as páginas
  -  Correções de acessibilidade
  - Agora você pode adicionar o `data-preload="true"` atributo [em suas marcas HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carregamento para CSS e JavaScript.
    - Carregue arquivos CSS vinculados ao mesmo tempo que o modelo HTML para que ele não fique piscando entre o carregamento dos arquivos.
    - Controle a ordem na qual suas `script` marcas são buscadas e executadas antes do carregamento da página.
  - O campo de email `type=email` agora e teclados móveis fornecerão as sugestões corretas
  - Suporte para tradução do Chrome
- Páginas unificadas e autodeclaradas
  - Os campos nome de usuário/email e senha agora `form` usam o elemento HTML para permitir que o Edge e o Internet Explorer (IE) salvem corretamente essas informações.

## <a name="110"></a>1.1.0

- Página de exceção (globalexception)
  - Correção de acessibilidade
  - Removeu a mensagem padrão quando não há contato da política
  - CSS padrão removido
- Página MFA (multifator)
  - Botão ' confirmar código ' removido
  - O campo de entrada para o código agora só usa a entrada de até seis (6) caracteres
  - A página tentará automaticamente verificar o código inserido quando um código de seis dígitos for inserido, sem qualquer botão que precise ser clicado
  - Se o código estiver errado, o campo de entrada será limpo automaticamente
  - Depois de três (3) tentativas com um código incorreto, o B2C envia um erro de volta para a terceira parte confiável
  -  Correções de acessibilidade
  - CSS padrão removido
- Página autodeclarada (selfasserted)
  - Removido o alerta de cancelamento
  - Classe CSS para elementos error
  - Mostrar/ocultar a lógica de erro aprimorada
  - CSS padrão removido
- SSP unificado (unifiedssp)
  - Controle KMSI (Mantenha-me conectado) adicionado

## <a name="100"></a>1.0.0

- Versão inicial

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre como personalizar a interface do usuário de seus aplicativos em políticas personalizadas, consulte [Personalizar a interface do usuário do seu aplicativo usando uma política personalizada](custom-policy-ui-customization.md).
