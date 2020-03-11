---
title: Visão geral de API Unificada
description: O API Unificada do Xamarin torna possível compartilhar código entre Mac e iOS e dar suporte a aplicativos de 32 e 64 bits com o mesmo binário.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 8402a48602dd94578e688faeb038aec69684e7d4
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917551"
---
# <a name="unified-api-overview"></a>Visão geral de API Unificada

O API Unificada do Xamarin torna possível compartilhar código entre Mac e iOS e dar suporte a aplicativos de 32 e 64 bits com o mesmo binário. O API Unificada é usado por padrão nos projetos novos Xamarin. iOS e Xamarin. Mac.

> [!IMPORTANT]
> O Xamarin API Clássica, que precede o API Unificada, foi preterido.
>
> - A última versão do Xamarin. iOS para dar suporte ao API Clássica (MonoTouch. dll) era Xamarin. iOS 9,10.
> - O Xamarin. Mac ainda dá suporte ao API Clássica, mas ele não é mais atualizado. Como ele foi preterido, os desenvolvedores devem mover seus aplicativos para a API Unificada.

## <a name="updating-classic-api-based-apps"></a>Atualizando aplicativos baseados em API Clássica

Siga as instruções relevantes para sua plataforma:

- [Atualizar aplicativos existentes](updating-apps.md)
- [Atualizar aplicativos iOS existentes](updating-ios-apps.md)
- [Atualizar aplicativos Mac existentes](updating-mac-apps.md)
- [Atualizar aplicativos Xamarin.Forms existentes](updating-xamarin-forms-apps.md)
- [Migrar uma associação para a API unificada](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-api"></a>[Dicas para atualizar o código para a API unificada](updating-tips.md)

Independentemente de quais aplicativos você está migrando, confira [estas dicas](updating-tips.md) para ajudá-lo a atualizar com êxito para o API unificada.

## <a name="library-split"></a>Divisão de biblioteca

A partir deste ponto, nossas APIs serão apresentadas de duas maneiras:

- **API clássica:** Limitado a 32 bits (somente) e expostos nos assemblies `monotouch.dll` e `XamMac.dll`.
- **API unificada:** Dê suporte ao desenvolvimento de 32 e 64 bits com uma única API disponível nos assemblies `Xamarin.iOS.dll` e `Xamarin.Mac.dll`.

Isso significa que para os desenvolvedores corporativos (não direcionando para a loja de aplicativos), você pode continuar usando as APIs clássicas existentes, pois continuaremos mantendo-as sempre, ou você pode atualizar para as novas APIs.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Alterações no namespace

Para reduzir o conflito de compartilhamento de código entre nossos produtos Mac e iOS, estamos alterando os namespaces das APIs nos produtos.

Estamos removendo o prefixo "MonoTouch" de nosso produto iOS e "MonoMac" de nosso produto Mac nos tipos de dados.

Isso simplifica o compartilhamento de código entre as plataformas Mac e iOS sem recorrer à compilação condicional e reduzirá o ruído na parte superior dos seus arquivos de código-fonte.

- **API clássica:** Os namespaces usam `MonoTouch.` ou prefixo de `MonoMac.`.
- **API unificada:** Nenhum prefixo de namespace

## <a name="runtime-defaults"></a>Padrões de tempo de execução

O API Unificada, por padrão, usa o coletor de lixo do **SGen** e o novo sistema de [contagem de referência](~/ios/internals/newrefcount.md) para controlar a propriedade do objeto. Esse mesmo recurso foi portado para Xamarin. Mac.

Isso resolve vários problemas que os desenvolvedores enfrentavam com o sistema antigo e também facilitam o [Gerenciamento de memória](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Observe que é possível habilitar um novo Refcount mesmo para a API Clássica, mas o padrão é conservador e não exige que os usuários façam alterações. Com o API Unificada, tiramos a oportunidade de alterar o padrão e fornecer aos desenvolvedores todos os aprimoramentos ao mesmo tempo que refatoram e testam novamente seu código.

## <a name="api-changes"></a>Alterações de API

O API Unificada remove os métodos preteridos e há algumas instâncias em que houve erros de digitação nos nomes de API quando eles eram associados aos namespaces MonoMac e MonoTouch originais nas APIs clássicas. Essas instâncias foram corrigidas nas novas APIs unificadas e precisarão ser atualizadas em seus aplicativos do componente, iOS e Mac. Aqui está uma lista dos mais comuns que você pode encontrar:

|Nome do método de API Clássica|Nome do método de API Unificada|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Para obter uma lista completa de alterações ao alternar do clássico para o API Unificada, consulte a documentação de [diferenças de API (Xamarin. Ios. dll) clássicas (MonoTouch. dll) vs Unified](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) .

## <a name="updating-to-unified"></a>Atualizando para unificado

Várias APIs antigas/desfeitas/preteridas no **clássico** não estão disponíveis na API **unificada** . Pode ser mais fácil corrigir os avisos de `CS0616` antes de iniciar a atualização (manual ou automatizada), já que você terá a mensagem de atributo `[Obsolete]` (parte do aviso) para orientá-lo na API correta.

Observe que estamos publicando uma [*comparação*](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) das alterações de API clássicas vs unificadas que podem ser usadas antes ou depois das atualizações do projeto. Ainda assim, a correção das chamadas obsoletas no clássico será uma economia de tempo (menos pesquisas de documentação).

Siga estas instruções para [atualizar aplicativos Ios existentes](~/cross-platform/macios/unified/updating-ios-apps.md)ou [aplicativos Mac](~/cross-platform/macios/unified/updating-mac-apps.md) para o API unificada.
Examine o restante desta página e [essas dicas](~/cross-platform/macios/unified/updating-tips.md) para obter informações adicionais sobre como migrar seu código.

### <a name="nuget"></a>NuGet

Pacotes NuGet com suporte do Xamarin. iOS anteriormente por meio do API Clássica publicou seus assemblies usando o moniker da plataforma **Monotouch10** .

O API Unificada introduz um novo identificador de plataforma para pacotes compatíveis – **Xamarin. iOS10**. Os pacotes NuGet existentes precisarão ser atualizados para adicionar suporte para essa plataforma, criando-se no API Unificada.

> [!IMPORTANT]
> Se você tiver um erro no formato _"erro 3, não será possível incluir ' MonoTouch. dll ' e ' Xamarin. Ios. dll ' no mesmo Xamarin. o projeto Ios-' Xamarin. Ios. dll ' é referenciado explicitamente, enquanto ' MonoTouch. dll ' é referenciado por ' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null '"_ depois de converter seu aplicativo para as APIs unificadas, normalmente, isso é devido à existência de um componente ou pacote NuGet no projeto que não foi atualizado para o API unificada. Você precisará remover o componente/NuGet existente, atualizar para uma versão que ofereça suporte a APIs unificadas e fazer uma compilação limpa.

### <a name="the-road-to-64-bits"></a>A estrada para 64 bits

Para conhecer o suporte a aplicativos 32 e 64 bits e informações sobre estruturas, consulte as [considerações da plataforma 32 e 64 bits](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Novos tipos de dados

No núcleo da diferença, as APIs do Mac e do iOS usam tipos de dados específicos da arquitetura que são sempre 32 bits em plataformas de 32 bits e 64 bit em plataformas de 64 bits.

Por exemplo, Objective-C mapeia o tipo de dados `NSInteger` para `int32_t` em sistemas de 32 bits e para `int64_t` em sistemas de 64 bits.

Para corresponder a esse comportamento, em nosso API Unificada, estamos substituindo os usos anteriores de `int` (que, no .NET, é definido como sempre sendo `System.Int32`) para um novo tipo de dados: `System.nint`.  Você pode considerar o "n" como significa "nativo", portanto, o tipo inteiro nativo da plataforma.

Estamos introduzindo `nint`, `nuint` e `nfloat`, bem como fornecer tipos de dados criados sobre eles, quando necessário.

Para saber mais sobre essas alterações de tipo de dados, consulte o documento [tipos nativos](~/cross-platform/macios/nativetypes.md) .

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Como detectar a arquitetura de aplicativos iOS

Pode haver situações em que seu aplicativo precisa saber se está em execução em um sistema iOS de 32 ou 64 bits. O código a seguir pode ser usado para verificar a arquitetura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrizes e System. coleções. Generic

Como C# os indexadores esperam um tipo de `int`, você terá que converter explicitamente `nint` valores em `int` para acessar os elementos em uma coleção ou matriz. Por exemplo:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Esse é o comportamento esperado, porque a conversão de `int` para `nint` é uma perda de 64 bits, uma conversão implícita não é feita.

### <a name="converting-datetime-to-nsdate"></a>Convertendo DateTime em NSDate

Ao usar as APIs unificadas, a conversão implícita de `DateTime` para `NSDate` valores não é mais executada. Esses valores precisarão ser explicitamente convertidos de um tipo para outro. Os métodos de extensão a seguir podem ser usados para automatizar esse processo:

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>APIs preteridas e erros de digitação

Dentro da API clássica do Xamarin. iOS (MonoTouch. dll), o atributo `[Obsolete]` foi usado de duas maneiras diferentes:

- **API do IOS substituída:** Isso é quando a Apple Dicas para interromper o uso de uma API, pois ela está sendo substituída por uma mais nova. A API Clássica ainda é bem e geralmente necessária (se você oferecer suporte à versão mais antiga do iOS).
 Essa API (e o atributo `[Obsolete]`) são incluídas nos novos assemblies do Xamarin. iOS.
- **API incorreta** Algumas APIs tinham erros de digitação em seus nomes.

Para os assemblies originais (MonoTouch. dll e XamMac. dll), guardamos o código antigo disponível para compatibilidade, mas eles foram removidos dos assemblies de API Unificada (Xamarin. iOS. dll e Xamarin. Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject subclasses. ctor (IntPtr)

Cada subclasse de `NSObject` tem um construtor que aceita um `IntPtr`. É assim que podemos instanciar uma nova instância gerenciada de um identificador ObjC nativo.

No clássico, esse era um construtor de `public`. No entanto, foi fácil usar o uso indevido desse recurso no código do usuário, por exemplo, criando várias instâncias gerenciadas para uma única instância de ObjC *ou* criando uma instância gerenciada que não teria o estado gerenciado esperado (para subclasses).

Para evitar esses tipos de problemas, os construtores de `IntPtr` agora são `protected` na API **unificada** , para serem usados somente para subclasses. Isso garantirá que a API correta/segura seja usada para criar instância gerenciada de identificadores, ou seja,

```csharp
var label = Runtime.GetNSObject<UILabel> (handle);
```

Essa API retornará uma instância gerenciada existente (se ela já existir) ou criará uma nova (quando necessário). Ele já está disponível na API clássica e unificada.

Observe que o `.ctor(NSObjectFlag)` agora também é `protected`, mas ele raramente era usado fora da subclasse.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction substituído por ação

Com as APIs unificadas, a `NSAction` foi removida em favor do .NET `Action`padrão. Essa é uma grande melhoria porque `Action` é um tipo comum de .NET, enquanto `NSAction` foi específica para Xamarin. iOS. Ambos fazem exatamente a mesma coisa, mas eles eram tipos distintos e incompatíveis e resultaram em mais código a ser escrito para obter o mesmo resultado.

Por exemplo, se o aplicativo Xamarin existente incluiu o seguinte código:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```

Agora ele pode ser substituído por um lambda simples:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Anteriormente, isso seria um erro do compilador, pois um `Action` não pode ser atribuído a `NSAction`, mas como agora `UITapGestureRecognizer` pega um `Action` em vez de um `NSAction` ele é válido nas APIs unificadas.

### <a name="custom-delegates-replaced-with-actiont"></a>Delegados personalizados substituídos por ação\<T >

Em **um** simples (por exemplo, um parâmetro), os delegados do .net foram substituídos por `Action<T>`. Por ex.:

```csharp
public delegate void NSNotificationHandler (NSNotification notification);
```

Agora pode ser usado como um `Action<NSNotification>`. Isso promove a reutilização de código e reduz a duplicação de código dentro do Xamarin. iOS e de seus próprios aplicativos.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>A tarefa\<bool > substituída por Task < Boolean, NSError > >

No **clássico** , havia algumas APIs assíncronas retornando `Task<bool>`. No entanto, alguns deles são usados quando um `NSError` fazia parte da assinatura, ou seja, o `bool` já estava `true` e você tinha que capturar uma exceção para obter a `NSError`.

Como alguns erros são muito comuns e o valor de retorno não era útil, esse padrão foi alterado em **unificado** para retornar um `Task<Tuple<Boolean,NSError>>`. Isso permite que você verifique o êxito e qualquer erro que possa ter ocorrido durante a chamada assíncrona.

### <a name="nsstring-vs-string"></a>Cadeia de caracteres NSString vs

Em alguns casos, algumas constantes precisavam ser alteradas de `string` para `NSString`, por exemplo, `UITableViewCell`

**Clássico**

```csharp
public virtual string ReuseIdentifier { get; }
```

**Unificada**

```csharp
public virtual NSString ReuseIdentifier { get; }
```

Em geral, preferimos o tipo de `System.String` .NET. No entanto, apesar das diretrizes da Apple, algumas APIs nativas estão comparando ponteiros constantes (não a própria cadeia de caracteres) e isso só pode funcionar quando expõemos as constantes como `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos Objective-C

O MonoTouch original não tinha suporte completo para protocolos ObjC e algumas APIs não ideais foram adicionadas para dar suporte ao cenário mais comum. Essa limitação não existe mais, mas, para compatibilidade com versões anteriores, várias APIs são mantidas dentro do `monotouch.dll` e `XamMac.dll`.

Essas limitações foram removidas e limpas nas APIs unificadas. A maioria das alterações terá a seguinte aparência:

**Clássico**

```csharp
public virtual AVAssetResourceLoaderDelegate Delegate { get; }
```

**Unificada**

```csharp
public virtual IAVAssetResourceLoaderDelegate Delegate { get; }
```

O prefixo `I` significa que o expõe **unificado** uma interface, em vez de um tipo específico, para o protocolo ObjC. Isso facilitará os casos em que você não deseja criar uma subclasse do tipo específico que o Xamarin. iOS forneceu.

Ele também permitia que alguma API fosse mais precisa e fácil de usar, por exemplo:

**Clássico**

```csharp
public virtual void SelectionDidChange (NSObject uiTextInput);
```

**Unificada**

```csharp
public virtual void SelectionDidChange (IUITextInput uiTextInput);
```

Essa API agora é mais fácil para nós, sem fazer referência à documentação, e a conclusão de seu código IDE fornecerá sugestões mais úteis com base no protocolo/interface.

#### <a name="nscoding-protocol"></a>Protocolo NSCoding

Nossa associação original incluía um. ctor (NSCoder) para todos os tipos, mesmo que ele não tivesse suporte para o protocolo de `NSCoding`.  Um único método de `Encode(NSCoder)` estava presente no `NSObject` para codificar o objeto.
Mas esse método só funcionaria se a instância estiver em conformidade com o protocolo NSCoding.

Na API Unificada corrigimos isso.  Os novos assemblies terão apenas o `.ctor(NSCoder)` se o tipo estiver em conformidade com `NSCoding`. Esses tipos agora têm um método `Encode(NSCoder)` que está de acordo com a interface `INSCoding`.

Baixo impacto: na maioria dos casos essa alteração não afetará os aplicativos como os construtores antigos, removidos, que não puderam ser usados.

## <a name="further-tips"></a>Mais dicas

Alterações adicionais a serem observadas estão listadas nas [dicas para atualizar aplicativos para o API unificada](~/cross-platform/macios/unified/updating-tips.md).


## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos iOS](updating-ios-apps.md)
- [Atualizando aplicativos do Mac](updating-mac-apps.md)
- [Atualizando aplicativos do Xamarin. Forms](updating-xamarin-forms-apps.md)
- [Atualizando associações](update-binding.md)
- [Atualizando dicas](updating-tips.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
