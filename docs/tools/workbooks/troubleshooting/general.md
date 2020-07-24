---
title: Problemas conhecidos & soluções alternativas
description: Este documento descreve problemas conhecidos e soluções alternativas para Xamarin Workbooks. Ele aborda problemas de CultureInfo, problemas de JSON e muito mais.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: aa6bbf9336acbff8558744220b9f4b8634f46421
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997222"
---
# <a name="known-issues--workarounds"></a>Problemas conhecidos & soluções alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistência de CultureInfo entre células

`System.Threading.CurrentThread.CurrentCulture`A configuração ou não `System.Globalization.CultureInfo.CurrentCulture` persiste nas células da pasta de trabalho em destinos de pastas de trabalho baseadas em mono (Mac, Ios e Android) devido a um [bug na implementação do `AppContext.SetSwitch` mono][appcontext-bug] .

### <a name="workarounds"></a>Soluções Alternativas

- Definir o aplicativo-domínio-local `DefaultThreadCurrentCulture` :

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- Ou atualize para as pastas de trabalho 1.2.1 ou mais recentes, que reescreverão atribuições para `System.Threading.CurrentThread.CurrentCulture` e `System.Globalization.CultureInfo.CurrentCulture` para fornecer o comportamento desejado (Configurando o erro de mono).

## <a name="unable-to-use-newtonsoftjson"></a>Não é possível usar Newtonsoft.Jsem

### <a name="workaround"></a>Solução alternativa

- Atualize para as pastas de trabalho 1.2.1, que instalarão o Newtonsoft.Jsno 9.0.1.
  As pastas de trabalho 1,3, atualmente no canal alfa, dão suporte às versões 10 e mais recentes.

### <a name="details"></a>Detalhes

O Newtonsoft.Jsem 10 foi lançado, o que redutou sua dependência no Microsoft. CSharp, que está em conflito com as pastas de trabalho da versão fornecida para dar suporte ao `dynamic` . Isso é abordado na versão de visualização das pastas de trabalho 1,3, mas, por enquanto, configuramos isso fixando Newtonsoft.Jsem relação especificamente à versão 9.0.1.

Os pacotes NuGet explicitamente, dependendo do Newtonsoft.Jsem 10 ou mais recente, só têm suporte nas pastas de trabalho 1,3, atualmente no canal alfa.

## <a name="code-tooltips-are-blank"></a>As dicas de ferramentas de código estão em branco

Há um [bug no editor Mônaco][monaco-bug] no Safari/WebKit, que é usado no aplicativo de pastas de trabalho do Mac, que resulta na renderização de dicas de ferramenta de código sem texto.

![Processamento de dicas de ferramenta Mônaco sem texto](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solução alternativa

- Clicar na dica de ferramenta depois que ela for exibida forçará o texto a renderizar.

- Ou atualizar para pastas de trabalho 1.2.1 ou mais recentes

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Os renderizadores SkiaSharp estão ausentes nas pastas de trabalho 1,3

A partir das pastas de trabalho 1,3, removemos os renderizadores SkiaSharp que enviamos nas pastas de trabalho 0.99.0, em favor do SkiaSharp fornecendo os próprios renderizadores, usando nosso [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solução alternativa

- Atualize SkiaSharp para a versão mais recente no NuGet. No momento da gravação, isso é 1.57.1.

## <a name="related-links"></a>Links Relacionados

- [Relatando bugs](~/tools/workbooks/install.md#reporting-bugs)
