---
title: Problemas conhecidos e soluções alternativas
description: Este documento descreve problemas conhecidos e soluções alternativas para pastas de trabalho do Xamarin. Ele aborda problemas CultureInfo, JSON e muito mais.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: topgenorth
ms.author: toopge
ms.openlocfilehash: b6dc3b119d3e85369a71638f2519b2ef0c85446c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794027"
---
# <a name="known-issues--workarounds"></a>Problemas conhecidos e soluções alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistência de CultureInfo em células

Configuração `System.Threading.CurrentThread.CurrentCulture` ou `System.Globalization.CultureInfo.CurrentCulture` não persiste entre as células da pasta de trabalho em destinos de pastas de trabalho com base em Mono (Mac, iOS e Android) devido a um [bug em Mono `AppContext.SetSwitch` ] [ appcontext-bug] implementação .

### <a name="workarounds"></a>Soluções alternativas

* Defina o local de aplicativo-domínio `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* Ou então, a atualização para pastas de trabalho 1.2.1 ou mais recente, que irá regravar atribuições para `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` para fornecer para o comportamento desejado (contornar o bug Mono).

## <a name="unable-to-use-newtonsoftjson"></a>Não é possível usar newtonsoft. JSON

### <a name="workaround"></a>Solução alternativa

* Atualizar para pastas de trabalho 1.2.1, que instalam o newtonsoft 9.0.1.
  1.3, as pastas de trabalho atualmente no canal alfa, dá suporte às versões mais recentes e 10.

### <a name="details"></a>Detalhes

Newtonsoft. JSON 10 foi lançado que aumentado sua dependência que está em conflito com as pastas de trabalho de versão é fornecido para oferecer suporte de Microsoft. CSharp `dynamic`. Isso é abordado na versão de visualização de pastas de trabalho 1.3, mas por enquanto trabalhamos esse problema por newtonsoft. JSON fixação especificamente para a versão 9.0.1.

Pacotes do NuGet explicitamente dependendo newtonsoft. JSON 10 ou mais recente só têm suporte em pastas de trabalho 1.3, atualmente no canal alfa.

## <a name="code-tooltips-are-blank"></a>Dicas de ferramenta do código são em branco

Há um [bugs no editor Monaco] [ monaco-bug] no Safari/WebKit, que é usado no aplicativo de pastas de trabalho de Mac, que resulta em processamento de dicas de ferramenta de código sem texto.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solução alternativa

* Clicar na dica de ferramenta depois que ele for exibido forçará o texto para processar.

* Ou atualizar para pastas de trabalho 1.2.1 ou mais recente

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Processadores SkiaSharp estão ausentes no 1.3 de pastas de trabalho

A partir de pastas de trabalho 1.3, removemos os renderizadores de SkiaSharp que enviamos em pastas de trabalho 0.99.0, favor SkiaSharp fornecendo as processadores de si mesmo, usando nosso [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solução alternativa

* Atualize SkiaSharp para a versão mais recente no NuGet. No momento da gravação, isso é 1.57.1.

## <a name="related-links"></a>Links relacionados

- [Relatório de Bugs](~/tools/workbooks/install.md#reporting-bugs)
