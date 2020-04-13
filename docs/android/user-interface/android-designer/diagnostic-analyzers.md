---
title: Analisadors de diagnóstico de layout do Android
description: Este guia lista todos os analisadores de diagnóstico de layout android suportados atualmente no Xamarin.Android.
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 04/07/2020
ms.openlocfilehash: 6203ce444bb97fa453a912a724f7f5724558b32a
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987602"
---
# <a name="android-designer-diagnostic-analyzers"></a>Analisadores de diagnóstico de designers android

Este guia lista todos os analisadores de diagnóstico de layout android suportados atualmente.

## <a name="accessibility"></a>Acessibilidade

Os seguintes analisadores ajudam a melhorar o suporte à acessibilidade:

| ID | Title | Severity | Descrição |
|----|-------|----------|-------------|
| Descrição do conteúdo | Imagem sem`contentDescription` | Aviso | Atributo ausente `contentDescription` na imagem |

## <a name="correctness"></a>Correção

Os analisadores a seguir ajudam a corrigir problemas de correção em um layout:

| ID | Title | Severity | Descrição | Ajuda |
|----|-------|----------|-------------|------|
| AdaptadorViewChildren | AdaptadorVer com crianças | Aviso | AdapterViews não pode ter filhos em XML | [Link](xref:Android.Widget.AdapterView) |
| DesaparecidoId | Fragmentos devem `id` especificar um ou`tag` | Aviso |Esta `<fragment>` tag deve `id` especificar um ou um `tag` para preservar o estado através das reinicializações de atividades | [Link](xref:Android.App.Fragment) |
| AninhadoScrollingVertical | Elementos de rolagem verticalmente aninhados | Aviso | Widgets de rolagem aninhadas |
| AninhadaHorizontal | Elementos de rolagem horizontalmente aninhados | Aviso | Widgets de rolagem aninhadas |
| ScrollViewSize | ScrollVeja crianças com tamanhos fill_parent/match_parent errados | Aviso | ScrollVeja crianças com tamanhos fill_parent/match_parent errados |
| ScrollViewContag | ScrollViews pode ter apenas um filho | Aviso | Uma visão de pergaminho pode ter apenas um filho |
| DesaparecidoAndroidNamespace | Faltando espaço de nome do Android no atributo | Erro | Faltando espaço de nome Do Android XML; seu atributo será interpretado como um atributo personalizado |
| DuplicatasIDs | IDs duplicados | Erro | Ids duplicados dentro de um único layout |
| IncludeLayoutParamsMissingWidthAndHeight | Falta largura e altura | Erro | Params de layout ignorados em incluir | [Link](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| IncludeLayoutParamsMissingWidth | Largura ausente | Erro | Params de layout ignorados em incluir | [Link](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| IncludeLayoutParamsMissingHeight | Altura perdida | Erro | Params de layout ignorados em incluir | [Link](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| Orientação | Faltando orientação explícita | Erro | Faltando orientação explícita |
| Suspeita0dp | Dimensão 0dp suspeita | Erro | Dimensão 0dp suspeita |
| Largura de tamanho necessária | Atributo de largura ausente | Erro | Atributo ausente: layout_width |
| Tamanho necessário | Atributo de altura ausente | Erro | Atributo ausente: layout_height |
| WebViewLayout | WebViews em pais wrap_content | Erro |
| Caso Errado | Caso errado para tag de exibição | Erro | Caso errado para tag de exibição | [Link](xref:Android.App.Fragment) |

## <a name="design"></a>Design

Os analisadores a seguir ajudam a melhorar a forma como você junta arquivos de layout:

| ID | Title | Severity | Descrição |
|----|-------|----------|-------------|
| Cor codificada | Cor codificada | Informações | Cor codificada muitas vezes leva à inconsistência |
| Tamanho hardcoded  | Tamanho codificado  | Informações | Tamanho codificado muitas vezes leva à inconsistência  |
| HardcodedText  | Texto codificado  | Aviso | Texto codificado |
| Recurso não resolvido | URL de recurso não resolvido | Aviso | Essa URL de recurso não pode ser resolvida |
| XmlErrors | Erro de sintaxe XML | Erro | Erro de sintaxe XML |

## <a name="performance"></a>Desempenho

Os seguintes analisadores ajudam a melhorar o desempenho do seu layout:

| ID | Title | Severity | Descrição |
|----|-------|----------|-------------|
| Pesos Aninhados | Pesos de layout aninhados | Aviso | Pesos aninhados são ruins para o desempenho |
| TooManyViews | Layout tem muitas visualizações | Aviso | Layout tem muitas visualizações |
| TooDeepLayout | A hierarquia de layout é muito profunda | Aviso | A hierarquia de layout é muito profunda |
| Pais Inúteis | Layout dos pais inúteis | Aviso | Layout dos pais inúteis |
| Folha inútil | Layout de folha inútil | Aviso | Esta `%1$s` visão é inútil (sem crianças, não, `background`não, `id`não) `style` |

## <a name="usability"></a>Facilidade de uso

Os seguintes analisadores ajudam a melhorar a usabilidade do layout para seus clientes:

| ID | Title | Severity | Descrição |
|----|-------|----------|-------------|
| Margem Negativa | Margens Negativas | Aviso | Margens Negativas |
| Tipo de entrada ausente | EditText sem entradaType | Aviso | Nenhum tipo de entrada especificado |
| InputTypePhone | EditText parece ser um número de telefone | Aviso | O nome de exibição sugere que este `phone` é um número de telefone, mas não inclui no`inputType` |
| InputTypeNumber | EditText parece ser um número | Aviso | O nome de exibição sugere que este é um `inputType` número, `numberDecimal`mas não inclui um numérico (como ) |
| InputTypePassword | EditText parece ser uma senha | Aviso | O nome de exibição sugere que esta `password` é `inputType` uma `textVisiblePassword`senha, mas não inclui no (como ) |
| InputTypePIN | EditText parece ser um PIN | Aviso | O nome de exibição sugere que esta é `numberPassword` uma senha (PIN), mas não inclui no`inputType` |
| InputTypeEmail | EditText parece ser um e-mail | Aviso | O nome de exibição sugere que este é `email` um `inputType` endereço de `textEmailAddress`e-mail, mas não inclui no (como ) |
| InputTypeURI | EditText parece ser um URI | Aviso | O nome de exibição sugere que este `textUri` é um URI, mas não inclui no`inputType` |
| Data de entrada | EditText parece ser uma data | Aviso | O nome de exibição sugere que esta `date` é `inputType` uma `datetime`data, mas não inclui no (como ) |
