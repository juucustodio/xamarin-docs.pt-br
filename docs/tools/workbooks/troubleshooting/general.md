---
title: Problemas conhecidos e soluções alternativas
description: Este documento descreve problemas conhecidos e soluções alternativas para pastas de trabalho do Xamarin. Ele aborda problemas de CultureInfo, problemas JSON e muito mais.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: d362698d2844ae6d96bba4929d509f5373742578
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350989"
---
# <a name="known-issues--workarounds"></a>Problemas conhecidos e soluções alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistência de CultureInfo nas células

Definindo `System.Threading.CurrentThread.CurrentCulture` ou `System.Globalization.CultureInfo.CurrentCulture` não persistem entre as células da pasta de trabalho em destinos de pastas de trabalho baseados em Mono (Mac, iOS e Android) devido a um [bug no Mono `AppContext.SetSwitch` ] [ appcontext-bug] implementação .

### <a name="workarounds"></a>Soluções alternativas

* Defina o local de aplicativo-domínio `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* Ou então, a atualização para pastas de trabalho 1.2.1 ou mais recente, que irá regravar atribuições às `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` para fornecer o comportamento desejado (trabalhando para resolver o bug Mono).

## <a name="unable-to-use-newtonsoftjson"></a>Não é possível usar o newtonsoft. JSON

### <a name="workaround"></a>Solução alternativa

* Atualizar para pastas de trabalho 1.2.1, que instalarão o newtonsoft. JSON 9.0.1.
  1.3, as pastas de trabalho atualmente no canal alfa, dá suporte às versões 10 e mais recentes.

### <a name="details"></a>Detalhes

Newtonsoft. JSON 10 foi lançado que esbarrar à Microsoft. CSharp que está em conflito com as pastas de trabalho de versão é fornecido para dar suporte à sua dependência `dynamic`. Isso é resolvido na versão de visualização de pastas de trabalho 1.3, mas por enquanto, trabalhamos com base nisso por fixação newtonsoft. JSON especificamente para a versão 9.0.1.

Pacotes do NuGet explicitamente dependendo do newtonsoft. JSON 10 ou mais recente têm suporte apenas em pastas de trabalho 1.3, atualmente no canal alfa.

## <a name="code-tooltips-are-blank"></a>Dicas de ferramenta de código estiver em branco

Há um [bug no editor Monaco] [ monaco-bug] no Safari/WebKit, que é usado no aplicativo pastas de trabalho Mac, que resulta na renderização de dicas de ferramenta de código sem texto.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solução alternativa

* Clicar na dica de ferramenta depois que ele for exibido forçará o texto a ser renderizado.

* Ou atualizar para pastas de trabalho 1.2.1 ou mais recente

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Renderizadores de SkiaSharp estão ausentes na versão 1.3 de pastas de trabalho

A partir de pastas de trabalho 1.3, removemos os renderizadores de SkiaSharp que lançamos em pastas de trabalho 0.99.0, em favor de SkiaSharp fornecendo os renderizadores a mesmo, usando nossa [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solução alternativa

* Atualize SkiaSharp para a versão mais recente no NuGet. No momento da escrita, isso é 1.57.1.

## <a name="related-links"></a>Links relacionados

- [Relatório de Bugs](~/tools/workbooks/install.md#reporting-bugs)
