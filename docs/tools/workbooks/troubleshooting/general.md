---
title: Problemas conhecidos & soluções alternativas
description: Este documento descreve problemas conhecidos e soluções alternativas para Xamarin Workbooks. Ele aborda problemas de CultureInfo, problemas de JSON e muito mais.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: c7b9f93c2d6339ba1fd26b27742ecfc0f438c5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018172"
---
# <a name="known-issues--workarounds"></a>Problemas conhecidos & soluções alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistência de CultureInfo entre células

A configuração `System.Threading.CurrentThread.CurrentCulture` ou `System.Globalization.CultureInfo.CurrentCulture` não persiste nas células da pasta de trabalho em destinos de pastas de trabalho baseadas em mono (Mac, iOS e Android) devido a um [bug na implementação de `AppContext.SetSwitch`do mono][appcontext-bug] .

### <a name="workarounds"></a>Soluções alternativas

- Defina o `DefaultThreadCurrentCulture`do domínio do aplicativo local:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- Ou atualize para as pastas de trabalho 1.2.1 ou mais recentes, que reescreverão atribuições para `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` para fornecer o comportamento desejado (Configurando o bug do mono).

## <a name="unable-to-use-newtonsoftjson"></a>Não é possível usar Newtonsoft. JSON

### <a name="workaround"></a>Solução alternativa

- Atualize para as pastas de trabalho 1.2.1, que instalarão o Newtonsoft. JSON 9.0.1.
  As pastas de trabalho 1,3, atualmente no canal alfa, dão suporte às versões 10 e mais recentes.

### <a name="details"></a>Detalhes

O Newtonsoft. JSON 10 foi lançado, o que redutou sua dependência em Microsoft. CSharp, que está em conflito com as pastas de trabalho de versão fornecida para dar suporte a `dynamic`. Isso é abordado na versão de visualização das pastas de trabalho 1,3, mas, por enquanto, trabalhamos com isso fixando Newtonsoft. JSON especificamente para a versão 9.0.1.

Os pacotes NuGet explicitamente, dependendo do Newtonsoft. JSON 10 ou mais recente, têm suporte apenas nas pastas de trabalho 1,3, atualmente no canal alfa.

## <a name="code-tooltips-are-blank"></a>As dicas de ferramentas de código estão em branco

Há um [bug no editor Mônaco][monaco-bug] no Safari/WebKit, que é usado no aplicativo de pastas de trabalho do Mac, que resulta na renderização de dicas de ferramenta de código sem texto.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solução alternativa

- Clicar na dica de ferramenta depois que ela for exibida forçará o texto a renderizar.

- Ou atualizar para pastas de trabalho 1.2.1 ou mais recentes

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Os renderizadores SkiaSharp estão ausentes nas pastas de trabalho 1,3

A partir das pastas de trabalho 1,3, removemos os renderizadores SkiaSharp que enviamos nas pastas de trabalho 0.99.0, em favor do SkiaSharp fornecendo os próprios renderizadores, usando nosso [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solução alternativa

- Atualize SkiaSharp para a versão mais recente no NuGet. No momento da gravação, isso é 1.57.1.

## <a name="related-links"></a>Links relacionados

- [Relatando bugs](~/tools/workbooks/install.md#reporting-bugs)
