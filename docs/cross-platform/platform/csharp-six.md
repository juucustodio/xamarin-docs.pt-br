---
title: C#6 visão geral dos recursos novos
description: A versão mais recente do C# linguagem – versão 6 – continua a evoluir a linguagem para ter menos clichê, melhor clareza e mais consistência. Sintaxe de inicialização mais limpa, a capacidade de usar await em blocos catch/finally e o nulo condicional? operador são especialmente úteis.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: b69fe417bb521781453042269b9b52609d8e00a0
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070950"
---
# <a name="c-6-new-features-overview"></a>C#6 visão geral dos recursos novos

_A versão mais recente do C# linguagem – versão 6 – continua a evoluir a linguagem para ter menos clichê, melhor clareza e mais consistência. Sintaxe de inicialização mais limpa, a capacidade de usar await em blocos catch/finally e o nulo condicional? operador são especialmente úteis._

Este documento apresenta os novos recursos do C# 6. Ele é totalmente suportado pelo compilador mono e os desenvolvedores podem começar a usar os novos recursos em todas as plataformas de destino do Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**O que há de novo no C# 6, por [Xamarin University](https://university.xamarin.com/)**

## <a name="using-c-6"></a>Usando o C# 6

O C# compilador 6 é usado em todas as versões recentes do Visual Studio para Mac.
Aqueles que usam os compiladores de linha de comando devem confirmar que `mcs --version` retorna 4.0 ou superior.
O Visual Studio para usuários do Mac pode verificar se eles tiverem Mono 4 (ou mais recente) instalado, fazendo referência ao **sobre o Visual Studio para Mac > Visual Studio para Mac > Mostrar detalhes**.

## <a name="less-boilerplate"></a>Menos clichê
### <a name="using-static"></a>usando estático
Enumerações e certas classes, como `System.Math`, são principalmente os detentores de valores estáticos e funções. No C# 6, você pode importar todos os membros estáticos de um tipo com uma única `using static` instrução. Comparar uma função trigonométrica típica no C# 5 e C# 6:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` não publique `const` campos, como `Math.PI` e `Math.E`diretamente acessível:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>usando estático com métodos de extensão

O `using static` recurso opera um pouco diferente com métodos de extensão. Embora os métodos de extensão são escritos usando `static`, eles não fazem sentido sem uma instância no qual operar. Portanto, quando `using static` é usado com um tipo que define os métodos de extensão, os métodos de extensão ficam disponíveis em seu tipo de destino (o método `this` tipo). Por exemplo, `using static System.Linq.Enumerable` podem ser usados para estender a API de `IEnumerable<T>` objetos sem interrupções em todos os tipos LINQ:

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

O exemplo anterior demonstra a diferença no comportamento: o método de extensão `Enumerable.Where` está associado com a matriz, enquanto o método estático `String.Join` pode ser chamado sem referência para o `String` tipo.

### <a name="nameof-expressions"></a>Expressões nameof
Às vezes, você quer se referir ao nome que forneceu um campo ou variável. No C# 6, `nameof(someVariableOrFieldOrType)` retornará a cadeia de caracteres `"someVariableOrFieldOrType"`. Por exemplo, ao lançar um `ArgumentException` muito provavelmente deseja nomear qual argumento é inválido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

A principal vantagem de `nameof` expressões é que eles são verificadas por tipo e são compatíveis com a plataforma a ferramenta de refatoração. A verificação de tipo de `nameof` expressões é particularmente bem-vindo em situações em que um `string` é usado para associar dinamicamente os tipos. Por exemplo, no iOS uma `string` é usado para especificar o tipo usado para criar um protótipo `UITableViewCell` objetos em um `UITableView`. `nameof` pode garantir que essa associação não falhará devido a um erro de ortografia ou impreciso de refatoração:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Embora você pode passar um nome qualificado para `nameof`, apenas o elemento final (após o último `.`) será retornado. Por exemplo, você pode adicionar uma associação de dados no xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

As duas chamadas para `SetBinding` estiver passando valores idênticos: `nameof(ReactiveType.StringField)` é `"StringField"`, e não `"ReactiveType.StringField"` como esperado inicialmente.

## <a name="null-conditional-operator"></a>Operador nulo condicional
Anteriormente, atualiza para C# introduziu os conceitos de tipos que permitem valor nulos e o operador de coalescência nula `??` para reduzir a quantidade de código clichê ao lidar com valores anuláveis. C#6 continua esse tema com o "operador nulo condicional" `?.`. Quando usado em um objeto no lado direito de uma expressão, o operador nulo condicional retorna o valor do membro, se o objeto não for `null` e `null` contrário:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Ambos `length0` e `length1` são inferidos para ser do tipo `int?`)

A última linha mostra o exemplo anterior a `?` operador nulo condicional em combinação com o `??` operador de coalescência nula. O novo C# 6 retorna do operador nulo condicional `null` no elemento 2º na matriz, no ponto em que o operador de coalescência nula será acionado e fornece um 0 como o `lengths` (se o que é apropriado ou não, é claro, de matriz específico do problema).

O operador nulo condicional tremendamente deve reduzir a quantidade de texto clichê verificação de nulo necessário em muitos aplicativos.

Há algumas limitações para o operador nulo condicional devido a ambiguidades. Você não pode seguir imediatamente uma `?` com uma lista de argumentos entre parênteses, como você pode esperam fazer com um delegado:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

No entanto, `Invoke` pode ser usado para separar o `?` da lista de argumentos e ainda é marcada por um `null`-bloco de texto clichê de verificação:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres
O `String.Format` função tradicionalmente usou índices como espaços reservados na cadeia de caracteres de formato, por exemplo, `String.Format("Expected: {0} Received: {1}.", expected, received`). É claro, adicionando um novo valor envolveu sempre uma tarefa pouco irritante de contagem de argumentos, renumeração espaços reservados e inserindo o novo argumento na sequência correta na lista de argumentos.

C#6 de novo recurso de interpolação de cadeia de caracteres muito aprimora `String.Format`. Agora, você pode nomear diretamente variáveis em uma cadeia de caracteres prefixadas com um `$`. Por exemplo:

```csharp
$"Expected: {expected} Received: {received}."
```

As variáveis são, obviamente, verificadas e uma variável com ortografia incorreta ou não disponível causará um erro do compilador.

Os espaços reservados não precisa ser variáveis simples, eles podem ser qualquer expressão. Dentro desses espaços reservados, você pode usar aspas *sem* essas cotações de escape. Por exemplo, observe o `"s"` a seguir:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

O alinhamento e a sintaxe de formatação do dá suporte a interpolação de cadeia de caracteres `String.Format`. Assim como você já escrevemos `{index, alignment:format}`, em C# 6, você escreve `{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
resultados em:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Interpolação de cadeia de caracteres é açúcar sintático para `String.Format`: ele não pode ser usado com `@""` literais de cadeia de caracteres e não é compatível com `const`, mesmo se nenhum espaço reservado é usado:

```csharp
const string s = $"Foo"; //Error : const requires value
```

Em que o caso de uso comum de criação de argumentos de função com a interpolação de cadeia de caracteres, você ainda precisa ter cuidado sobre problemas de cultura, codificação e de escape. Consultas SQL e a URL são, obviamente, essenciais para limpar. Assim como acontece com `String.Format`, usa interpolação de cadeia de caracteres a `CultureInfo.CurrentCulture`. Usando `CultureInfo.InvariantCulture` é um pouco mais prolixo:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inicialização

C#6 fornece várias maneiras de concisas para especificar propriedades, campos e membros.

### <a name="auto-property-initialization"></a>Inicialização de propriedade automática

Propriedades automáticas podem agora ser inicializadas da mesma maneira concisa como campos. Autopropriedades imutáveis podem ser escritas com apenas um getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

No construtor, você pode definir o valor de uma propriedade de automático apenas de getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Essa inicialização de propriedades automáticas é um recurso de economia de espaço geral e uma dádiva para os desenvolvedores que desejam enfatizar a imutabilidade em seus objetos.

### <a name="index-initializers"></a>Inicializadores de índice

C#6 introduz inicializadores de índice, que permitem que você defina a chave e o valor em tipos que têm um indexador. Normalmente, isso é para `Dictionary`-estilo de estruturas de dados:

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Membros de função aptos para expressão

As funções lambda tem vários benefícios, um dos quais é simplesmente economizando espaço. Da mesma forma, os membros de classe de expressão permitem que pequenas funções sejam expressas um pouco mais sucintamente do que era possível nas versões anteriores do C# 6.

Membros de função aptos para expressão usam a sintaxe de seta lambda em vez da sintaxe tradicional de bloco:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Observe que a sintaxe de seta lambda não usa uma explícita `return`. Para funções que retornam `void`, a expressão também deve ser uma instrução:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Membros aptos para expressão ainda estão sujeitas a regra que `async` tem suporte para métodos, mas não as propriedades:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Exceções

Não há duas maneiras sobre ele: tratamento de exceções é difícil de solucionar. Novos recursos no C# 6 Verifique o tratamento de exceção mais flexível e consistente.

### <a name="exception-filters"></a>Filtros de exceção

Por definição, as exceções ocorrem em circunstâncias incomuns e pode ser muito difícil de motivo e o código sobre *todos os* as maneiras que pode ocorrer uma exceção de um tipo específico. C#6 introduz a capacidade de proteger um manipulador de execução com um filtro avaliada em tempo de execução. Isso é feito pela adição de um `when (bool)` padrão depois que o normal `catch(ExceptionType)` declaração. A seguir, um filtro distingue relacionadas a um erro de análise de `date` parâmetro em vez de outros erros de análise.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>espera em catch... finally...

O `async` essas capacidades introduzidas no C# 5 ter sido uma virada de jogo para o idioma. No C# 5, `await` não foi permitido no `catch` e `finally` bloqueia um aborrecimento que recebe o valor da `async/await` funcionalidade. C#6 remove essa limitação, permitindo que os resultados assíncronos seja aguardado consistentemente por meio do programa, conforme mostrado no trecho a seguir:

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Resumo

O C# linguagem continua a evoluir para tornar os desenvolvedores mais produtivos enquanto também promovendo as práticas recomendadas e as ferramentas de suporte. Este documento tenha dado a uma visão geral dos novos recursos de linguagem no C# 6 e brevemente demonstrou como eles são usados.

## <a name="related-links"></a>Links relacionados

- [Novo idioma de recursos no C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

