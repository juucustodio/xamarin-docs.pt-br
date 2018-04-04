---
title: API unificada
description: O novo estilo API torna mais fácil para compartilhar código entre Mac e iOS, bem como permitindo que você oferecer suporte a 32 e 64 bits aplicativos com os mesmos binários.
ms.prod: xamarin
ms.assetid: 14311617-1BC2-42CC-AF3F-9F97733EE2D0
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: d0ad5b08aaf5e7b14f75611a28d389e3f7e0477c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="unified-api"></a>API unificada

_O novo estilo API torna mais fácil para compartilhar código entre Mac e iOS, bem como permitindo que você oferecer suporte a 32 e 64 bits aplicativos com os mesmos binários._

Para melhorar o compartilhamento entre Mac e iOS de código e permitir que os desenvolvedores têm uma base de código único que funciona em 32 e 64 bits, no início 2015, apresentamos uma nova API em chamadas da API unificada de produtos Xamarin.Mac e xamarin.

> [!IMPORTANT]
> **Substituição de perfil clássica:** conforme são adicionadas novas plataformas xamarin começamos gradualmente substituir recursos do perfil clássico (monotouch.dll). Por exemplo, a opção de não-NRC (novo-ref count) foi removida. NRC sempre foi habilitada para unificada de todos os aplicativos (ou seja, não-NRC nunca foi uma opção) e não tem nenhum problema conhecido. Versões futuras removerá a opção de usar Boehm como o coletor de lixo. Isso também era uma opção nunca disponível para aplicativos unificados. A remoção completa de suporte clássico é agendada para Avançar estão com a versão do xamarin 10.0.

## <a name="overviewoverviewmd"></a>[Visão geral](overview.md)

Descreve a lógica por trás da API unificada e explica as diferenças na API clássica em detalhes. Consulte esta página para entender as alterações que foram feitas na API unificada.

## <a name="updating-classic-api-based-apps"></a>Atualizando aplicativos baseados em APIs clássicos

Siga as instruções relevantes para sua plataforma:

- [Atualizar aplicativos existentes](updating-apps.md)
- [Atualizar aplicativos iOS existentes](updating-ios-apps.md)
- [Atualizar aplicativos Mac existentes](updating-mac-apps.md)
- [Atualizar aplicativos Xamarin.Forms existentes](updating-xamarin-forms-apps.md)
- [Migrar uma associação para a API unificada](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Dicas para atualizar o código para a API unificada](updating-tips.md)

Independentemente de quais aplicativos você está migrando, check-out [essas dicas](updating-tips.md) para ajudá-lo a atualizar com êxito para a API unificada.

## <a name="library-split"></a>Divisão de biblioteca

Esse ponto em diante, ocorrerá nossas APIs de duas maneiras:

-  **API clássica:** limitados a 32 bits (apenas) e exposto no `monotouch.dll` e `XamMac.dll` assemblies.
-  **Uma API unificada:** oferecer suporte ao desenvolvimento de bit de 32 e 64 com uma única API disponível no `Xamarin.iOS.dll` e `Xamarin.Mac.dll` assemblies.

Isso significa que, para a empresa desenvolvedores (não direcionando a loja de aplicativos), você pode continuar usando as APIs do clássico existentes, como podemos manter mantendo-os para sempre, ou você podem atualizar para as novas APIs.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Alterações de Namespace

Para reduzir a fricção para compartilhar código entre nossos produtos Mac e iOS, estamos alterando os namespaces para as APIs de produtos.

Nós são descartando o prefixo "MonoTouch" do nosso produto de iOS e "MonoMac" de nossos produtos Mac nos tipos de dados.

Isso torna mais simples compartilhar código entre as plataformas Mac e iOS sem recorrer à compilação condicional e pode reduzir o ruído na parte superior dos seus arquivos de código fonte.

-  **API clássica:** usar Namespaces `MonoTouch.` ou `MonoMac.` prefixo.
-  **Uma API unificada:** nenhum prefixo de namespace

## <a name="api-changes"></a>Alterações de API

A API unificada remove métodos preteridos e há alguns casos em que houve erros de digitação em nomes de API quando eles foram associados aos namespaces MonoTouch e MonoMac originais nas APIs do clássico. Essas instâncias foram corrigidas as novas APIs unificado e precisam ser atualizada no seu componente, aplicativos iOS e Mac. Aqui está uma lista dos mais comuns que você pode executar em:

|Nome do método de API clássica|Nome do método de API unificada|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Para obter uma lista completa de alterações ao alternar de clássica para a API unificado, consulte nosso [clássico (monotouch.dll) vs diferenças de API unificada (Xamarin.iOS.dll)](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) documentação.

## <a name="updating-to-unified"></a>Atualizando a unificada

Vários API antiga/dividido/preterida em **clássico** não estão disponíveis no **unificada** API. Pode ser mais fácil de corrigir o `CS0616` avisos antes de iniciar sua (manual ou automatizada) atualização desde que você terá a `[Obsolete]` atributo mensagem (parte do aviso) para orientá-lo para a direita API.

Observe que estamos publicando um [ *comparação* ](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) de versus os clássicos unificado alterações de API que podem ser usadas antes ou após as atualizações do projeto. Corrigindo ainda o obsoletos chamadas em será clássico geralmente economizar tempo (menos pesquisas de documentação).

Siga estas instruções para [atualizar aplicativos existentes do iOS](~/cross-platform/macios/unified/updating-ios-apps.md), ou [aplicativos Mac](~/cross-platform/macios/unified/updating-mac-apps.md) para a API unificada.
Examine o restante desta página, e [essas dicas](~/cross-platform/macios/unified/updating-tips.md) para obter informações adicionais sobre como migrar seu código.

### <a name="nuget"></a>NuGet

Pacotes do NuGet que anteriormente suporte xamarin por meio da API clássica publicado seus assemblies usando o **Monotouch10** moniker de plataforma.

A API unificada apresenta um novo identificador de plataforma para pacotes compatíveis - **Xamarin.iOS10**. Pacotes do NuGet existentes precisa ser atualizado para adicionar suporte para essa plataforma, criando com a API unificada.

> [!IMPORTANT]
> Se você tiver um erro no formato _"Erro 3 não pode incluir 'monotouch.dll' e 'Xamarin.iOS.dll' no mesmo projeto xamarin - 'Xamarin.iOS.dll' é referenciado explicitamente, enquanto a 'monotouch.dll' é referenciado por ' xxx, versão = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ depois de converter seu aplicativo para as APIs unificado, normalmente ele é devido à necessidade de um componente ou um pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa.

### <a name="the-road-to-64-bits"></a>O caminho para 64 Bits

Para obter informações sobre suporte de 32 e 64 bits aplicativos e informações sobre estruturas consulte o [32 e 64 bits de considerações sobre a plataforma](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Novos tipos de dados

No núcleo da diferença, Mac e iOS APIs use um tipos de dados específicos de arquitetura sempre 32 bits em plataformas de 32 bits e 64 bits em plataformas de 64 bits.

Por exemplo, Objective-C mapeia o `NSInteger` tipo de dados `int32_t` em sistemas de 32 bits e para `int64_t` em sistemas de 64 bits.

Para corresponder a esse comportamento, em nossa API unificada, estamos substituindo os usos anteriores `int` (que no .NET é definida como sendo sempre `System.Int32`) para um novo tipo de dados: `System.nint`.  Você pode pensar "n" como significado "nativo" para digitar o número inteiro nativo da plataforma.

Estamos introduzindo `nint`, `nuint` e `nfloat` também fornece tipos de dados criado com base em-los quando necessário.

Para saber mais sobre essas alterações de tipo de dados, consulte o [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Como detectar a arquitetura de aplicativos do iOS

Pode haver situações em que seu aplicativo precisa saber se ele está em execução em um sistema de iOS de 64 bits ou um de 32 bits. O código a seguir pode ser usado para verificar se a arquitetura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrizes e Generic

Como o c# indexadores esperam um tipo de `int`, você precisa converter explicitamente `nint` valores `int` para acessar os elementos de uma coleção ou matriz. Por exemplo:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Esse comportamento é esperado, porque a conversão de `int` para `nint` é perdas em 64 bits, uma conversão implícita não é feita.

### <a name="converting-datetime-to-nsdate"></a>Convertendo DateTime em NSDate

Ao usar as APIs unificado, a conversão implícita de `DateTime` para `NSDate` valores não é mais executada. Esses valores precisará ser explicitamente convertidos de um tipo para outro. Os seguintes métodos de extensão podem ser usados para automatizar esse processo:

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

### <a name="deprecated-apis-and-typos"></a>Erros tipográficos e APIs obsoletas

Xamarin dentro de API com clássico (monotouch.dll) o `[Obsolete]` atributo foi usado de duas maneiras diferentes:

-  **Preterido API para iOS:** isso é quando Apple dicas para que você pare de usar uma API porque ele está sendo substituído pelo mais recente. A API clássica é o problema e geralmente necessária (se você oferece suporte a versão mais antiga do iOS).
 Essa API (e o `[Obsolete]` atributo) são incluídos em novos assemblies xamarin.
-  **API incorreto** API alguns tiveram erros de digitação em seus nomes.

Para os assemblies originais (monotouch.dll e XamMac.dll), mantivemos o código antigo disponível para compatibilidade, mas eles foram removidos dos assemblies de API unificada (Xamarin.iOS.dll e Xamarin.Mac)

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject subclasses .ctor(IntPtr)

Cada `NSObject` subclasse tem um construtor que aceita um `IntPtr`. Isso é como podemos pode criar uma nova instância gerenciada de um identificador de ObjC nativo.

Em clássico essa era uma `public` construtor. No entanto, é fácil de usar indevidamente a esse recurso no código do usuário, por exemplo, criar várias instâncias de uma única instância ObjC gerenciadas *ou* criando uma instância gerenciada que seria não têm o estado esperado gerenciado (para que as subclasses).

Para evitar esses tipos de problema a `IntPtr` construtores são agora `protected` na **unificada** API a ser usado apenas para subclassificação. Isso garantirá que a corrigir/safe API é usada para criar a instância gerenciada de identificadores, ou seja

    var label = Runtime.GetNSObject<UILabel> (handle);

Essa API retornará uma instância gerenciada existente (se existir) ou criará um novo (quando necessário). Já está disponível na API clássico e unificado.

Observe que o `.ctor(NSObjectFlag)` agora também está `protected` , mas este raramente foi usado fora subclassificação.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction substituído por ação

Com as APIs unificado, `NSAction` foi removido em favor do .NET padrão `Action`. Este é um grande aprimoramento porque `Action` é um tipo comum do .NET, enquanto `NSAction` específica para xamarin. Ambos fazem exatamente a mesma coisa, mas eles foram os tipos distintos e incompatíveis e resultaram no código mais precisar ser gravado para alcançar o mesmo resultado.

Por exemplo, se seu aplicativo Xamarin existente incluído o código a seguir:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
Agora, ele pode ser substituído por um lambda simple:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Anteriormente que seria um erro do compilador porque um `Action` não pode ser atribuído a `NSAction`, mas como `UITapGestureRecognizer` agora leva um `Action` em vez de um `NSAction` é válida nas APIs do unificado.

### <a name="custom-delegates-replaced-with-actiont"></a>Substituído por ação de delegados personalizados<T>

Em **unificada** algumas simples (por exemplo, um parâmetro) delegados do .net foram substituídos por `Action<T>`. Por exemplo,

    public delegate void NSNotificationHandler (NSNotification notification);

agora pode ser usado como um `Action<NSNotification>`. Esse código de promoção reutilizar e reduzir a duplicação de código dentro de xamarin e seus próprios aplicativos.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Tarefa<bool> substituído por tarefa < booleano, NSError >>

Em **clássico** Houve algumas APIs assíncronas retornando `Task<bool>`. No entanto, alguns deles onde devem usar quando um `NSError` fazia parte da assinatura, ou seja, o `bool` já foi `true` e era que capturar uma exceção ao obter o `NSError`.

Como alguns erros são muito comuns e o valor de retorno não eram úteis esse padrão foi alterado em **unificada** para retornar um `Task<Tuple<Boolean,NSError>>`. Isso permite que você verificar o sucesso e qualquer erro que pode ter ocorrido durante a chamada assíncrona.

### <a name="nsstring-vs-string"></a>Cadeia de caracteres NSString vs

Em alguns casos algumas constantes foram alterados de `string` para `NSString`, por exemplo, `UITableViewCell`

**Clássico**

    public virtual string ReuseIdentifier { get; }

**Unificado**

    public virtual NSString ReuseIdentifier { get; }

Em geral, prefira o .NET `System.String` tipo. No entanto, apesar de diretrizes da Apple, alguns API nativa está comparando ponteiros constantes (não a cadeia de caracteres em si) e isso funciona somente quando expomos as constantes como `NSString`.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos de Objective-C

API de MonoTouch original não tinha suporte completo para protocolos ObjC e alguns não ideal, foram adicionadas para oferecer suporte a situação mais comum. Essa limitação não existe mais, mas, para compatibilidade com versões anteriores, várias APIs são mantidas dentro de `monotouch.dll` e `XamMac.dll`.

Essas limitações foram removidas e limpas sobre as APIs unificado. A maioria das alterações será assim:

**Clássico**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unificado**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

O `I` prefixo significa **unificada** expor uma interface, em vez de um tipo específico, para o protocolo ObjC. Isso facilitará a casos em que você não deseja subclasse do tipo específico que xamarin fornecido.

Ele também permissão alguns API para ser mais precisas e fácil de usar, por exemplo:

**Clássico**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unificado**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

Essa API agora são mais fáceis para nós, sem se referindo a documentação e a conclusão de código do IDE fornecerá mais útil sugestões com base na interface/protocolo.

#### <a name="nscoding-protocol"></a>Protocolo NSCoding

Nossa associação original incluído um .ctor(NSCoder) para cada tipo - mesmo que não oferecia suporte a `NSCoding` protocolo.  Um único `Encode(NSCoder)` método estava presente no `NSObject` para codificar o objeto.
Mas esse método funcionaria somente se a instância de acordo com o protocolo NSCoding.

A API do Unified Corrigimos isso.  Os novos assemblies só terá o `.ctor(NSCoder)` se o tipo está em conformidade com `NSCoding`. Também tipos agora tem um `Encode(NSCoder)` método que está de acordo com o `INSCoding` interface.

Baixo impacto: Na maioria dos casos essa alteração não afetará aplicativos como os construtores antigos, removidos, não podem ser usados.

## <a name="further-tips"></a>Outras dicas

Alterações adicionais a serem consideradas são listadas no [dicas para atualizar aplicativos para a API unificada](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Código de exemplo

A partir de 31 de julho, publicadas portas dos exemplos de iOS para essa nova API no `magic-types` branch no [monotouch exemplos](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Para Mac, estamos verificando exemplos em ambos o [mac exemplos](https://github.com/xamarin/mac-samples) repositório (mostrando as novas APIs no Mavericks/Yosemite), bem como exemplos de 32/64 bits no branch de tipos magic [mac exemplos](https://github.com/xamarin/monotouch-samples/commits/magic-types).
-->

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos do iOS](updating-ios-apps.md)
- [Atualizando aplicativos do Mac](updating-mac-apps.md)
- [Atualizar aplicativos xamarin. Forms](updating-xamarin-forms-apps.md)
- [Atualizando associações](update-binding.md)
- [Atualizar dicas](updating-tips.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
