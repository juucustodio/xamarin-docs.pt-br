---
title: Visão geral da API unificada
description: API de unificada do Xamarin torna possível compartilhar código entre aplicativos de 32 e 64 bits suporte, iOS e Mac com o mesmo binário.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1d159d280bd3b8855c32e3e437dfdefcbe0463cb
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235019"
---
# <a name="unified-api-overview"></a>Visão geral da API unificada

API de unificada do Xamarin torna possível compartilhar código entre aplicativos de 32 e 64 bits suporte, iOS e Mac com o mesmo binário. A API unificada é usada por padrão em novos projetos xamarin. IOS e xamarin. Mac.

> [!IMPORTANT]
> A API clássica Xamarin, que precedida a API unificada, foi preterida. 
> - A última versão do xamarin. IOS para dar suporte a API clássica (MonoTouch. dll) estava 9.10 de xamarin. IOS.
> - Xamarin. Mac ainda oferece suporte a API clássica, mas ele não é mais atualizado. Uma vez que ela foi preterida, os desenvolvedores devem mover seus aplicativos para a API unificada.

## <a name="updating-classic-api-based-apps"></a>Atualizando aplicativos clássicos baseados em API

Siga as instruções relevantes para sua plataforma:

- [Atualizar aplicativos existentes](updating-apps.md)
- [Atualizar aplicativos iOS existentes](updating-ios-apps.md)
- [Atualizar aplicativos Mac existentes](updating-mac-apps.md)
- [Atualizar aplicativos Xamarin.Forms existentes](updating-xamarin-forms-apps.md)
- [Migrar uma associação para a API unificada](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Dicas para atualizar o código para a API unificada](updating-tips.md)

Independentemente de quais aplicativos você está migrando, fazer check-out [estas dicas](updating-tips.md) para ajudá-lo a atualizar com êxito para a API unificada.

## <a name="library-split"></a>Divisão de biblioteca

Desse ponto em diante, ocorrerão nossas APIs de duas maneiras:

-  **API clássica:** limitados a 32 bits (somente) e expostas em de `monotouch.dll` e `XamMac.dll` assemblies.
-  **API unificada:** dão suporte ao desenvolvimento de bit 32 e 64 com uma única API disponível na `Xamarin.iOS.dll` e `Xamarin.Mac.dll` assemblies.

Isso significa que, para empresas, desenvolvedores (não direcionado para a App Store), você pode continuar usando as APIs clássicas existentes, como podemos manter mantendo-os para sempre, ou você podem atualizar para as novas APIs.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Alterações de Namespace

Para reduzir o atrito para compartilhar código entre os nossos produtos de Mac e iOS, estamos alterando os namespaces para as APIs em produtos.

Podemos está descartando o prefixo "MonoTouch" de nossos produtos de iOS e "MonoMac" do nosso produto Mac nos tipos de dados.

Isso torna mais simples de compartilhar código entre as plataformas Mac e iOS sem recorrer a compilação condicional e reduzirá o ruído na parte superior dos arquivos de código de origem.

-  **API clássica:** usar Namespaces `MonoTouch.` ou `MonoMac.` prefixo.
-  **API unificada:** nenhum prefixo de namespace

## <a name="runtime-defaults"></a>Padrões de tempo de execução

A API unificada, por padrão, usa o **SGen** coletor de lixo e a [nova contagem de referência](~/ios/internals/newrefcount.md) sistema para rastrear a propriedade do objeto. Esse mesmo recurso foi movido para o xamarin. Mac.

Isso resolve alguns problemas que os desenvolvedores enfrentam com o sistema antigo e também facilitam [gerenciamento de memória](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Observe que é possível habilitar o novo Refcount mesmo para a API clássica, mas o padrão é conservadora e não requer que os usuários façam alterações. Com a API unificada, aproveitaram a oportunidade de alterar o padrão e dar aos desenvolvedores todas as melhorias ao mesmo tempo que eles Refatorar e testar novamente o seu código.

## <a name="api-changes"></a>Alterações na API

A API unificada remove métodos preteridos e há alguns casos em que houve erros de digitação em nomes de API, quando eles eram vinculados aos namespaces MonoTouch e MonoMac originais nas APIs do clássico. Essas instâncias foram corrigidas nas novas APIs unificado e precisarão ser atualizados no seu componente, aplicativos iOS e Mac. Aqui está uma lista das mais comuns que você pode executar:

|Nome do método da API clássica|Nome do método de API unificada|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Para obter uma lista completa das alterações ao mudar do clássico para a API unificada, consulte nosso [clássico (MonoTouch. dll) versus as diferenças de API unificado (xamarin)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) documentação.

## <a name="updating-to-unified"></a>Atualizando a unificada

Vários antigo/dividida/API foi preterida no **clássico** não estão disponíveis na **unificado** API. Ele pode ser mais fácil de corrigir a `CS0616` avisos antes de iniciar sua (manual ou automática) de atualização, pois você terá o `[Obsolete]` atributo mensagem (parte do aviso) para orientá-lo para a API à direita.

Observe que estamos publicando uma [ *diff* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) do vs clássicos unified alterações na API que podem ser usadas antes ou após as atualizações do seu projeto. Corrigindo ainda o obsoletos geralmente chamadas serão clássico economizar tempo (menos pesquisas de documentação).

Siga estas instruções para [atualizar aplicativos existentes do iOS](~/cross-platform/macios/unified/updating-ios-apps.md), ou [aplicativos Mac](~/cross-platform/macios/unified/updating-mac-apps.md) para a API unificada.
Revise o restante desta página, e [estas dicas](~/cross-platform/macios/unified/updating-tips.md) para obter mais informações sobre como migrar seu código.

### <a name="nuget"></a>NuGet

Pacotes do NuGet que tinham suporte anteriormente xamarin. IOS por meio da API clássica publicado seus assemblies usando o **Monotouch10** moniker de plataforma.

A API unificada introduz um novo identificador de plataforma para pacotes compatíveis – **Xamarin.iOS10**. Pacotes do NuGet precisará ser atualizado para adicionar suporte para essa plataforma, pela construção contra a API unificada.

> [!IMPORTANT]
> Se você tiver um erro no formulário _"Erro 3 não pode incluir 'MonoTouch. dll' e 'Xamarin' no mesmo projeto xamarin. IOS – 'Xamarin' é referenciada explicitamente, enquanto 'MonoTouch. dll' é referenciado por ' xxx, versão = 0.0.000, Cultura = neutral, PublicKeyToken = null'"_ após converter seu aplicativo para as APIs unificadas, ela normalmente é devido a ter um componente ou o pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa.

### <a name="the-road-to-64-bits"></a>O caminho para a 64 Bits

Para obter informações sobre suporte a 32 e 64 bits de aplicativos e informações sobre estruturas, consulte o [considerações sobre a plataforma de 32 e 64 bits](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Novos tipos de dados

A essência da diferença, Mac e iOS APIs usam um tipos de dados específicos de arquitetura que são sempre de 32 bits em plataformas de 32 bits e 64 bits em plataformas de 64 bits.

Por exemplo, o Objective-C mapeia o `NSInteger` tipo de dados `int32_t` em sistemas de 32 bits e, ao `int64_t` em sistemas de 64 bits.

Para corresponder a esse comportamento, em nossa API unificada, estamos substituindo os usos anteriores `int` (que no .NET é definida como sempre sendo `System.Int32`) para um novo tipo de dados: `System.nint`.  Você pode pensar "n" como o que significa "nativo", para que o inteiro nativo digitar da plataforma.

Estamos introduzindo `nint`, `nuint` e `nfloat` fornecer também os tipos de dados criados sobrelos quando necessário.

Para saber mais sobre essas alterações de tipo de dados, consulte o [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Como detectar a arquitetura de aplicativos do iOS

Pode haver situações em que seu aplicativo precisa saber se ele está em execução em um de 32 bits ou um sistema iOS de 64 bits. O código a seguir pode ser usado para verificar a arquitetura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>System. Collections e matrizes

Porque C# indexadores esperam um tipo de `int`, você terá que converter explicitamente `nint` valores de `int` para acessar os elementos em uma coleção ou matriz. Por exemplo:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Esse comportamento é esperado, porque a conversão de `int` para `nint` é com perdas em 64 bits, uma conversão implícita não é feita.

### <a name="converting-datetime-to-nsdate"></a>Convertendo DateTime em NSDate

Ao usar as APIs de Unificação, conversão implícita de `DateTime` para `NSDate` valores não é mais executada. Esses valores precisará ser explicitamente convertidos de um tipo para outro. Os seguintes métodos de extensão podem ser usados para automatizar esse processo:

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

### <a name="deprecated-apis-and-typos"></a>Erros de digitação e APIs preteridas

API clássica do xamarin. IOS de dentro (MonoTouch. dll) a `[Obsolete]` atributo foi usado de duas maneiras diferentes:

-  **Preterido API do iOS:** isso é quando Apple dicas para você parar de usar uma API, porque ela está sendo substituída por uma mais recente. A API clássica é ainda bem e geralmente necessário (se houver suporte a versão mais antiga do iOS).
 API desse tipo (e o `[Obsolete]` atributo) estão incluídos para os novos assemblies do xamarin. IOS.
-  **API incorreto** API alguns tiver erros de digitação em seus nomes.

Para os assemblies originais (MonoTouch. dll e XamMac.dll), mantivemos o código antigo disponível para compatibilidade, mas eles foram removidos dos assemblies de API unificada (xamarin e xamarin. Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>.Ctor(IntPtr) de subclasses de NSObject

Cada `NSObject` subclasse tem um construtor que aceita um `IntPtr`. Isso é como podemos pode criar uma nova instância gerenciada de um identificador de ObjC nativo.

No modo clássico, isso era um `public` construtor. No entanto, foi fácil usar indevidamente esse recurso no código do usuário, por exemplo, criar várias instâncias de uma única instância de ObjC gerenciadas *ou* criando uma instância gerenciada que seria não têm o estado esperado gerenciado (para que as subclasses).

Para evitar esses tipos de problemas de `IntPtr` construtores agora estão `protected` na **unificada** API a ser usado apenas para a criação de subclasses. Isso garantirá que o corrigir/seguros API é usado para criar a instância gerenciada de identificadores, ou seja

    var label = Runtime.GetNSObject<UILabel> (handle);

Essa API retorna uma instância gerenciada existente (se já houver) ou criará um novo (quando necessário). Já está disponível na API clássica e unificada.

Observe que o `.ctor(NSObjectFlag)` agora também é `protected` , mas este foi usado raramente fora a criação de subclasses.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction substituído por ação

Com as APIs de Unificação `NSAction` foi removido em favor do .NET standard `Action`. Isso é uma grande melhoria porque `Action` é um tipo comum do .NET, enquanto `NSAction` era específico ao xamarin. IOS. Ambos fazem exatamente a mesma coisa, mas elas eram tipos distintos e incompatíveis e resultaram em um código mais ter que ser escrito para alcançar o mesmo resultado.

Por exemplo, se o seu aplicativo Xamarin incluiu o código a seguir:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
Agora, ele pode ser substituído por um lambda simple:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Anteriormente que seria um erro do compilador porque uma `Action` não pode ser atribuído a `NSAction`, mas como `UITapGestureRecognizer` agora leva um `Action` em vez de um `NSAction` é válida nas APIs unificada.

### <a name="custom-delegates-replaced-with-actiont"></a>Delegados personalizados substituídos por ação<T>

Na **unificada** algumas simples (por exemplo, um parâmetro) delegados do .net foram substituídos por `Action<T>`. Por exemplo,

    public delegate void NSNotificationHandler (NSNotification notification);

agora pode ser usado como um `Action<NSNotification>`. Esse código de promoção reutilizar e reduzir a duplicação de código dentro do xamarin. IOS e seus próprios aplicativos.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Tarefa<bool> substituído com a tarefa < booleano, NSError >>

Na **clássico** havia algumas APIs assíncronas retornando `Task<bool>`. Porém, alguns deles onde estão para serem usados quando um `NSError` fazia parte da assinatura, ou seja, o `bool` já estava `true` e você tinha que capturar uma exceção para obter o `NSError`.

Uma vez que alguns erros são muito comuns e o valor de retorno não era útil esse padrão foi alterado no **unificada** para retornar um `Task<Tuple<Boolean,NSError>>`. Isso permite que você verificar o sucesso e qualquer erro que pode ter ocorrido durante a chamada assíncrona.

### <a name="nsstring-vs-string"></a>Cadeia de caracteres NSString vs

Em alguns casos algumas constantes tinham que ser alterado de `string` para `NSString`, por exemplo, `UITableViewCell`

**clássico**

    public virtual string ReuseIdentifier { get; }

**Unificado**

    public virtual NSString ReuseIdentifier { get; }

Em geral, Preferimos .NET `System.String` tipo. No entanto, apesar das diretrizes da Apple, alguma API nativa estiver comparando ponteiros constante (não a própria cadeia de caracteres) e isso só funcionam quando podemos expor as constantes como `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos de Objective-C

O MonoTouch original não tinha suporte completo para protocolos de ObjC e alguns não ideal, API foram adicionados para suportar o cenário mais comum. Essa limitação não existe mais, mas, para compatibilidade com versões anteriores, várias APIs que são mantidas em torno de dentro `monotouch.dll` e `XamMac.dll`.

Essas limitações foram removidas e limpas sobre as APIs de Unificação. A maioria das alterações terá esta aparência:

**clássico**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unificado**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

O `I` prefixo significa **unificada** expor uma interface, em vez de um tipo específico, para o protocolo de ObjC. Isso facilitará a casos em que você não deseja subclasse o tipo específico que o xamarin. IOS é fornecida.

Isso também permitiu alguma API ser mais preciso e fácil de usar, por exemplo:

**clássico**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unificado**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

API desse tipo agora são mais fáceis para nós, sem fazer referência a documentação e a conclusão de código do IDE fornecerá mais úteis sugestões com base na interface/protocolo.

#### <a name="nscoding-protocol"></a>Protocolo NSCoding

Nossa associação original incluído um .ctor(NSCoder) para cada tipo - mesmo que não oferecia suporte a `NSCoding` protocolo.  Uma única `Encode(NSCoder)` método estava presente no `NSObject` para codificar o objeto.
Mas esse método só funcionará se a instância de acordo com o protocolo NSCoding.

A API unificada Corrigimos isso.  Os novos assemblies terá apenas o `.ctor(NSCoder)` se o tipo está em conformidade com `NSCoding`. Também esses tipos agora tem um `Encode(NSCoder)` método que está de acordo com o `INSCoding` interface.

Baixo impacto: Na maioria dos casos essa alteração não afetará aplicativos como os construtores antigos, removidos, não pode ser usados.

## <a name="further-tips"></a>Dicas adicionais

Alterações adicionais a serem consideradas são listadas na [dicas para atualizar aplicativos para a API unificada](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Código de exemplo

A partir de 31 de julho, publicamos as portas dos exemplos de iOS para essa nova API na `magic-types` ramificar no [monotouch-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Para Mac, estamos verificando se há exemplos em ambas as [amostras de mac](https://github.com/xamarin/mac-samples) repositório (mostrando as novas APIs em Mavericks/Yosemite), bem como exemplos de 32/64 bits no branch de tipos de mágica [amostras de mac](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos do iOS](updating-ios-apps.md)
- [Atualizando aplicativos do Mac](updating-mac-apps.md)
- [Atualizando aplicativos xamarin. Forms](updating-xamarin-forms-apps.md)
- [Atualizando associações](update-binding.md)
- [Dicas de atualização](updating-tips.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
