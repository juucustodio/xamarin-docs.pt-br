---
title: Vinculação de dados e codificação de chave-valor no Xamarin. Mac
description: Este artigo aborda o uso da codificação de valor-chave e da observação de valor-chave para permitir a vinculação de dados a elementos da interface do usuário no Interface Builder do Xcode.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 2f9b4dfac4909a5c68b6c53fc913925ee95313a3
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997508"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Vinculação de dados e codificação de chave-valor no Xamarin. Mac

_Este artigo aborda o uso da codificação de valor-chave e da observação de valor-chave para permitir a vinculação de dados a elementos da interface do usuário no Interface Builder do Xcode._

## <a name="overview"></a>Visão geral

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso à mesma codificação de chave-valor e técnicas de vinculação de dados que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para associar dados a elementos da interface do usuário em vez de escrever código.

Usando a codificação de valor chave e técnicas de vinculação de dados em seu aplicativo Xamarin. Mac, você pode diminuir muito a quantidade de código que você precisa escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem o benefício de desacoplar ainda mais os dados de backup (_modelo de dados_) da sua interface do usuário de front-end (_Model-View-Controller_), levando a um design de aplicativo mais fácil de manter e mais flexível.

[![Um exemplo do aplicativo em execução](databinding-images/intro01.png "Um exemplo do aplicativo em execução")](databinding-images/intro01-large.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com codificação de chave-valor e vinculação de dados em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` atributos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

<a name="What_is_Key-Value_Coding"></a>

## <a name="what-is-key-value-coding"></a>O que é codificação de chave-valor

A KVC (codificação de valor chave) é um mecanismo para acessar as propriedades de um objeto indiretamente, usando chaves (cadeias de caracteres especialmente formatadas) para identificar Propriedades em vez de acessá-las por meio de variáveis de instância ou métodos de acessador ( `get/set` ). Ao implementar acessadores compatíveis com codificação de chave-valor em seu aplicativo Xamarin. Mac, você obterá acesso a outros recursos macOS (anteriormente conhecidos como OS X), como KVO (observação de valor-chave), vinculação de dados, dados principais, associações Cocoa e scripts.

Usando a codificação de valor chave e técnicas de vinculação de dados em seu aplicativo Xamarin. Mac, você pode diminuir muito a quantidade de código que você precisa escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem o benefício de desacoplar ainda mais os dados de backup (_modelo de dados_) da sua interface do usuário de front-end (_Model-View-Controller_), levando a um design de aplicativo mais fácil de manter e mais flexível.

Por exemplo, vamos examinar a seguinte definição de classe de um objeto compatível com KVC:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

Primeiro, o `[Register("PersonModel")]` atributo registra a classe e a expõe para Objective-C. Em seguida, a classe precisa herdar de `NSObject` (ou uma subclasse herdada de `NSObject` ), que adiciona vários métodos de base que permitem que a classe seja compatível com KVC. Em seguida, o `[Export("Name")]` atributo expõe a `Name` propriedade e define o valor da chave que posteriormente será usado para acessar a propriedade por meio de técnicas KVC e KVO.

Por fim, para ser capaz de ser o valor-chave observado nas alterações do valor da propriedade, o acessador deve encapsular as alterações em seu valor no `WillChangeValue` e nas `DidChangeValue` chamadas de método (especificando a mesma chave que o `Export` atributo).  Por exemplo:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Esta etapa é _muito_ importante para a vinculação de dados no interface Builder do Xcode (como veremos mais adiante neste artigo).

Para obter mais informações, consulte o [Guia de programação de codificação de valor-chave](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)da Apple.

### <a name="keys-and-key-paths"></a>Chaves e caminhos de chave

Uma _chave_ é uma cadeia de caracteres que identifica uma propriedade específica de um objeto. Normalmente, uma chave corresponde ao nome de um método acessador em um objeto de conformidade com codificação de chave-valor. As chaves devem usar a codificação ASCII, geralmente começam com uma letra minúscula e não podem conter espaço em branco. Portanto, dado o exemplo acima, `Name` seria um valor-chave da `Name` propriedade da `PersonModel` classe. A chave e o nome da propriedade que eles expões não precisam ser os mesmos, mas na maioria dos casos eles são.

Um _caminho de chave_ é uma cadeia de caracteres de chaves separadas por pontos usada para especificar uma hierarquia de propriedades de objeto a ser atravessada. A propriedade da primeira chave na sequência é relativa ao receptor, e cada chave subsequente é avaliada em relação ao valor da propriedade anterior. Da mesma maneira, você usa a notação de ponto para atravessar um objeto e suas propriedades em uma classe C#.

Por exemplo, se você expandiu a `PersonModel` classe e adicionou a `Child` Propriedade:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

O caminho da chave para o nome do filho seria `self.Child.Name` ou simplesmente `Child.Name` (com base em como o valor da chave estava sendo usado).

### <a name="getting-values-using-key-value-coding"></a>Obtendo valores usando codificação de chave-valor

O `ValueForKey` método retorna o valor para a chave especificada (como um `NSString` ), em relação à instância da classe KVC que recebe a solicitação. Por exemplo, se `Person` for uma instância da `PersonModel` classe definida acima:

```csharp
// Read value
var name = Person.ValueForKey (new NSString("Name"));
```

Isso retornará o valor da `Name` propriedade para essa instância do `PersonModel` .

### <a name="setting-values-using-key-value-coding"></a>Definindo valores usando codificação de valor-chave

Da mesma forma, `SetValueForKey` defina o valor da chave especificada (como a `NSString` ), em relação à instância da classe KVC que recebe a solicitação. Novamente, usando uma instância da `PersonModel` classe, conforme mostrado abaixo:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Alteraria o valor da `Name` propriedade para `Jane Doe` .

<a name="Observing_Value_Changes"></a>

### <a name="observing-value-changes"></a>Observando alterações de valor

Usando a observação de valor-chave (KVO), você pode anexar um observador a uma chave específica de uma classe compatível com KVC e ser notificado sempre que o valor dessa chave for modificado (usando técnicas KVC ou acessando diretamente a propriedade fornecida no código C#). Por exemplo:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

Agora, sempre que a `Name` propriedade da `Person` instância da `PersonModel` classe for modificada, o novo valor será gravado no console.

Para obter mais informações, consulte [introdução à Apple no guia de programação de observação de valor-chave](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>Vinculação de dados

As seções a seguir mostrarão como você pode usar um código de chave-valor e um valor de chave que observa a classe compatível para associar dados aos elementos da interface do usuário no Interface Builder do Xcode, em vez de ler e gravar valores usando código C#. Dessa forma, você separa o _modelo de dados_ dos modos de exibição usados para exibi-los, tornando o aplicativo Xamarin. Mac mais flexível e mais fácil de manter. Você também diminui muito a quantidade de código a ser escrito.

<a name="Defining_your_Data_Model"></a>

### <a name="defining-your-data-model"></a>Definindo seu modelo de dados

Antes de poder associar dados a um elemento de interface do usuário no Interface Builder, você deve ter uma classe compatível com KVC/KVO definida em seu aplicativo Xamarin. Mac para atuar como o _modelo de dados_ para a associação. O modelo de dados fornece todos os dados que serão exibidos na interface do usuário e recebe quaisquer modificações para os dados que o usuário faz na interface do usuário ao executar o aplicativo.

Por exemplo, se você estivesse escrevendo um aplicativo que gerenciava um grupo de funcionários, poderia usar a seguinte classe para definir o modelo de dados:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

A maioria dos recursos dessa classe foi abordada na seção [o que é codificação de chave-valor](#What_is_Key-Value_Coding) acima. No entanto, vamos examinar alguns elementos específicos e algumas adições que foram feitas para permitir que essa classe atue como um modelo de dados para **controladores de matriz** e de **árvore** (que usaremos mais tarde para **exibições de árvore**de ligação de dados, **exibições de estrutura de tópicos** e **exibições de coleção**).

Primeiro, como um funcionário pode ser um gerente, usamos um `NSArray` (especificamente um para que `NSMutableArray` os valores possam ser modificados) para permitir que os funcionários que eles gerenciaram sejam anexados a eles:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Duas coisas a serem observadas aqui:

1. Usamos um `NSMutableArray` em vez de uma matriz ou coleção C# padrão, pois isso é um requisito para associar dados a controles AppKit, como **exibições de tabela**, **exibições de estrutura de tópicos** e **coleções**.
2. Expunhamos a matriz de funcionários, convertendo-o em um `NSArray` para fins de vinculação de dados e alteramos seu nome formatado em C#, `People` , para um que a vinculação de dados espera, `personModelArray` na matriz do formato **{class_name}** (Observe que o primeiro caractere foi feito em letras minúsculas).

Em seguida, precisamos adicionar alguns métodos públicos de nome especialmente para dar suporte a **controladores de matriz** e de **árvore**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Eles permitem que os controladores solicitem e modifiquem os dados que eles exibem. Como as expostas `NSArray` acima, elas têm uma Convenção de nomenclatura muito específica (que difere das convenções típicas de nomenclatura C#):

- `addObject:`-Adiciona um objeto à matriz.
- `insertObject:in{class_name}ArrayAtIndex:`-Em que `{class_name}` é o nome da sua classe. Esse método insere um objeto na matriz em um determinado índice.
- `removeObjectFrom{class_name}ArrayAtIndex:`-Em que `{class_name}` é o nome da sua classe. Esse método remove o objeto na matriz em um determinado índice.
- `set{class_name}Array:`-Em que `{class_name}` é o nome da sua classe. Esse método permite que você substitua o transporte existente por um novo.

Dentro desses métodos, Encapsulamos as alterações na matriz `WillChangeValue` e `DidChangeValue` as mensagens para conformidade com o KVO.

Por fim, como a `Icon` propriedade depende do valor da `isManager` propriedade, as alterações na `isManager` propriedade podem não ser refletidas no `Icon` para elementos de interface do usuário associados a dados (durante KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
```

Para corrigir isso, usamos o seguinte código:

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Observe que, além de sua própria chave, o `isManager` acessador também está enviando as `WillChangeValue` `DidChangeValue` mensagens e para a `Icon` chave, para que ela também veja a alteração.

Vamos usar o modelo de `PersonModel` dados no restante deste artigo.

<a name="Simple_Data_Binding"></a>

### <a name="simple-data-binding"></a>Vinculação de dados simples

Com nosso modelo de dados definido, vamos dar uma olhada em um exemplo simples de vinculação de dados no Interface Builder do Xcode. Por exemplo, vamos adicionar um formulário ao nosso aplicativo Xamarin. Mac que pode ser usado para editar o `PersonModel` que definimos acima. Vamos adicionar alguns campos de texto e uma caixa de seleção para exibir e editar as propriedades de nosso modelo.

Primeiro, vamos adicionar um novo **controlador de exibição** ao nosso arquivo **Main. Storyboard** em interface Builder e nomear sua classe `SimpleViewController` :

[![Adicionando um novo controlador de exibição](databinding-images/simple01.png "Adicionando um novo controlador de exibição")](databinding-images/simple01-large.png#lightbox)

Em seguida, retorne para Visual Studio para Mac, edite o arquivo **SimpleViewController.cs** (que foi adicionado automaticamente ao nosso projeto) e expor uma instância do `PersonModel` que iremos associar dados ao nosso formulário. Adicione o seguinte código:

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

Em seguida, quando o modo de exibição for carregado, vamos criar uma instância do nosso `PersonModel` e preenchê-la com este código:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Agora, precisamos criar nosso formulário, clicar duas vezes no arquivo **Main. Storyboard** para abri-lo para edição no interface Builder. Layout o formulário deve ser semelhante ao seguinte:

[![Editando o storyboard no Xcode](databinding-images/simple02.png "Editando o storyboard no Xcode")](databinding-images/simple02-large.png#lightbox)

Para associar os dados ao formulário `PersonModel` que expunhamos por meio da `Person` chave, faça o seguinte:

1. Selecione o campo de texto **nome do funcionário** e alterne para o **Inspetor de associações**.
2. Marque a caixa **associar a** e selecione **controlador de exibição simples** na lista suspensa. Em seguida, insira `self.Person.Name` para o **caminho da chave**:

    [![Inserindo o caminho da chave](databinding-images/simple03.png "Inserindo o caminho da chave")](databinding-images/simple03-large.png#lightbox)
3. Selecione o campo de texto **profissão** e marque a caixa **associar a** e selecione **controlador de exibição simples** no menu suspenso. Em seguida, insira `self.Person.Occupation` para o **caminho da chave**:

    [![Inserindo o caminho da chave](databinding-images/simple04.png "Inserindo o caminho da chave")](databinding-images/simple04-large.png#lightbox)
4. Selecione o **funcionário é uma** caixa de seleção de gerente e marque a caixa **associar a** e selecione **controlador de exibição simples** no menu suspenso. Em seguida, insira `self.Person.isManager` para o **caminho da chave**:

    [![Inserindo o caminho da chave](databinding-images/simple05.png "Inserindo o caminho da chave")](databinding-images/simple05-large.png#lightbox)
5. Selecione o campo **de texto número de funcionários gerenciados** e marque a caixa **associar a** e selecione **controlador de exibição simples** no menu suspenso. Em seguida, insira `self.Person.NumberOfEmployees` para o **caminho da chave**:

    [![Inserindo o caminho da chave](databinding-images/simple06.png "Inserindo o caminho da chave")](databinding-images/simple06-large.png#lightbox)
6. Se o funcionário não for um gerente, queremos ocultar o número de funcionários gerenciados rótulo e campo de texto.
7. Selecione o **número de funcionários gerenciados** rótulo, expanda o Turndown **oculto** e marque a caixa **associar a** e selecione **controlador de exibição simples** no menu suspenso. Em seguida, insira `self.Person.isManager` para o **caminho da chave**:

    [![Inserindo o caminho da chave](databinding-images/simple07.png "Inserindo o caminho da chave")](databinding-images/simple07-large.png#lightbox)
8. Selecione `NSNegateBoolean` na lista suspensa **valor do transformador** :

    ![Selecionando a transformação chave NSNegateBoolean](databinding-images/simple08.png "Selecionando a transformação chave NSNegateBoolean")
9. Isso informa à ligação de dados que o rótulo ficará oculto se o valor da `isManager` propriedade for `false` .
10. Repita as etapas 7 e 8 para o campo de texto de **número de funcionários gerenciados** .
11. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Se você executar o aplicativo, os valores da `Person` Propriedade preencherão automaticamente nosso formulário:

[![Mostrando um formulário preenchido automaticamente](databinding-images/simple09.png "Mostrando um formulário preenchido automaticamente")](databinding-images/simple09-large.png#lightbox)

Todas as alterações que os usuários fizerem no formulário serão gravadas novamente na `Person` propriedade no controlador de exibição. Por exemplo, cancelar a seleção de **funcionário é** que o gerente atualiza a `Person` instância do `PersonModel` e o **número de funcionários gerenciados** rótulo e campo de texto são ocultos automaticamente (por meio da vinculação de dados):

[![Ocultando o número de funcionários para não-gerentes](databinding-images/simple10.png "Ocultando o número de funcionários para não-gerentes")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding"></a>

### <a name="table-view-data-binding"></a>Vinculação de dados de exibição de tabela

Agora que temos os conceitos básicos da ligação de dados do caminho, vamos examinar uma tarefa de ligação de dados mais complexa usando um controlador de _matriz_ e uma ligação de dados para uma exibição de tabela. Para obter mais informações sobre como trabalhar com exibições de tabela, consulte a documentação de [exibições de tabela](~/mac/user-interface/table-view.md) .

Primeiro, vamos adicionar um novo **controlador de exibição** ao nosso arquivo **Main. Storyboard** em interface Builder e nomear sua classe `TableViewController` :

[![Adicionando um novo controlador de exibição](databinding-images/table01.png "Adicionando um novo controlador de exibição")](databinding-images/table01-large.png#lightbox)

Em seguida, vamos editar o arquivo **TableViewController.cs** (que foi adicionado automaticamente ao nosso projeto) e expor uma matriz ( `NSArray` ) de `PersonModel` classes para as quais os dados serão vinculados ao nosso formulário. Adicione o seguinte código:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Assim como fizemos na `PersonModel` classe acima na seção [definindo seu modelo de dados](#Defining_your_Data_Model) , apresentamos quatro métodos públicos especialmente nomeados para que o controlador da matriz e ler e gravar dados de nossa coleção de `PersonModels` .

Em seguida, quando a exibição é carregada, precisamos preencher nossa matriz com este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Agora, precisamos criar nossa exibição de tabela, clicar duas vezes no arquivo **Main. Storyboard** para abri-lo para edição no interface Builder. Layout a tabela deve ser semelhante ao seguinte:

[![Layout de uma nova exibição de tabela](databinding-images/table02.png "Layout de uma nova exibição de tabela")](databinding-images/table02-large.png#lightbox)

Precisamos adicionar um controlador de **matriz** para fornecer dados associados à nossa tabela, faça o seguinte:

1. Arraste um **controlador de matriz** do **Inspetor de biblioteca** para o **Editor de interface**:

    ![Selecionando um controlador de matriz da biblioteca](databinding-images/table03.png "Selecionando um controlador de matriz da biblioteca")
2. Selecione **controlador de matriz** na **hierarquia de interface** e alterne para o **Inspetor de atributo**:

    [![Selecionando o Inspetor de atributos](databinding-images/table04.png "Selecionando o Inspetor de atributos")](databinding-images/table04-large.png#lightbox)
3. Insira `PersonModel` para o **nome da classe**, clique no botão de **adição** e adicione três chaves. Nomeie- `Name` os `Occupation` e `isManager` :

    ![Adicionando os caminhos de chave necessários](databinding-images/table05.png "Adicionando os caminhos de chave necessários")
4. Isso informa ao controlador da matriz o que está gerenciando uma matriz e quais propriedades ele deve expor (via chaves).
5. Alterne para o **Inspetor de associações** e, **em matriz de conteúdo** , selecione **associar a** e **controlador de exibição de tabela**. Insira um **caminho de chave de modelo** de `self.personModelArray` :

    ![Inserindo um caminho de chave](databinding-images/table06.png "Inserindo um caminho de chave")
6. Isso vincula o controlador de matriz à matriz de `PersonModels` que expostos em nosso controlador de exibição.

Agora precisamos associar o modo de exibição de tabela ao controlador da matriz, faça o seguinte:

1. Selecione o modo de exibição de tabela e o **Inspetor de associação**:

    [![Selecionando o Inspetor de associação](databinding-images/table07.png "Selecionando o Inspetor de associação")](databinding-images/table07-large.png#lightbox)
2. Na Turndown **conteúdo da tabela** , selecione **associar a** e **controlador de matriz**. Insira `arrangedObjects` para o campo **chave do controlador** :

    ![Definindo a chave do controlador](databinding-images/table08.png "Definindo a chave do controlador")
3. Selecione a **célula exibição de tabela** na coluna **funcionário** . No **Inspetor de associações** sob o **valor** Turndown, selecione **a exibição associar à** **célula da tabela**. Insira `objectValue.Name` para o **caminho de chave do modelo**:

    [![Definindo o caminho de chave do modelo](databinding-images/table09.png "Definindo o caminho de chave do modelo")](databinding-images/table09-large.png#lightbox)
4. `objectValue`é o atual `PersonModel` na matriz que está sendo gerenciada pelo controlador da matriz.
5. Selecione a **célula exibição de tabela** na coluna **ocupação** . No **Inspetor de associações** sob o **valor** Turndown, selecione **a exibição associar à** **célula da tabela**. Insira `objectValue.Occupation` para o **caminho de chave do modelo**:

    [![Definindo o caminho de chave do modelo](databinding-images/table10.png "Definindo o caminho de chave do modelo")](databinding-images/table10-large.png#lightbox)
6. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Se executarmos o aplicativo, a tabela será populada com nossa matriz de `PersonModels` :

[![Executando o aplicativo](databinding-images/table11.png "Executando o aplicativo")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding"></a>

### <a name="outline-view-data-binding"></a>modo de exibição da Estrutura do Código Associação de dados

a vinculação de dados em um modo de exibição de estrutura de tópicos é muito semelhante à de uma exibição de tabela. A principal diferença é que usaremos um **controlador de árvore** em vez de um **controlador de matriz** para fornecer os dados associados ao modo de exibição de estrutura de tópicos. Para obter mais informações sobre como trabalhar com exibições de estrutura de tópicos, consulte a documentação de [exibições de estrutura de tópicos](~/mac/user-interface/outline-view.md) .

Primeiro, vamos adicionar um novo **controlador de exibição** ao nosso arquivo **Main. Storyboard** em interface Builder e nomear sua classe `OutlineViewController` :

[![Adicionando um novo controlador de exibição](databinding-images/outline01.png "Adicionando um novo controlador de exibição")](databinding-images/outline01-large.png#lightbox)

Em seguida, vamos editar o arquivo **OutlineViewController.cs** (que foi adicionado automaticamente ao nosso projeto) e expor uma matriz ( `NSArray` ) de `PersonModel` classes para as quais os dados serão vinculados ao nosso formulário. Adicione o seguinte código:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Assim como fizemos na `PersonModel` classe acima na seção [definindo seu modelo de dados](#Defining_your_Data_Model) , apresentamos quatro métodos públicos especialmente nomeados para que o controlador de árvore e leia e grave dados de nossa coleção de `PersonModels` .

Em seguida, quando a exibição é carregada, precisamos preencher nossa matriz com este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Agora, precisamos criar nossa exibição de estrutura de tópicos, clicar duas vezes no arquivo **Main. Storyboard** para abri-lo para edição no interface Builder. Layout a tabela deve ser semelhante ao seguinte:

[![Criando o modo de exibição de estrutura de tópicos](databinding-images/outline02.png "Criando o modo de exibição de estrutura de tópicos")](databinding-images/outline02-large.png#lightbox)

Precisamos adicionar um controlador de **árvore** para fornecer dados associados à nossa estrutura de tópicos, faça o seguinte:

1. Arraste um **controlador de árvore** do **Inspetor de biblioteca** para o **Editor de interface**:

    ![Selecionando um controlador de árvore na biblioteca](databinding-images/outline03.png "Selecionando um controlador de árvore na biblioteca")
2. Selecione **controlador de árvore** na **hierarquia de interface** e alterne para o **Inspetor de atributo**:

    [![Selecionando o Inspetor de atributo](databinding-images/outline04.png "Selecionando o Inspetor de atributo")](databinding-images/outline04-large.png#lightbox)
3. Insira `PersonModel` para o **nome da classe**, clique no botão de **adição** e adicione três chaves. Nomeie- `Name` os `Occupation` e `isManager` :

    ![Adicionando os caminhos de chave necessários](databinding-images/outline05.png "Adicionando os caminhos de chave necessários")
4. Isso informa ao controlador de árvore o que está gerenciando uma matriz e quais propriedades ele deve expor (via chaves).
5. Na seção **controlador de árvore** , insira `personModelArray` para **filhos**, insira `NumberOfEmployees` sob a **contagem** e insira `isEmployee` em **folha**:

    ![Definindo os caminhos de chave do controlador de árvore](databinding-images/outline05.png "Definindo os caminhos de chave do controlador de árvore")
6. Isso informa ao controlador de árvore onde encontrar todos os nós filho, quantos nós filhos existem e se o nó atual tem nós filho.
7. Alterne para o **Inspetor de associações** e, **em matriz de conteúdo** , selecione **associar ao** **proprietário do arquivo**. Insira um **caminho de chave de modelo** de `self.personModelArray` :

    ![Editando o caminho da chave](databinding-images/outline06.png "Editando o caminho da chave")
8. Isso vincula o controlador de árvore à matriz de `PersonModels` que expostos em nosso controlador de exibição.

Agora precisamos associar nossa exibição de estrutura de tópicos ao controlador de árvore, faça o seguinte:

1. Selecione o modo de exibição de estrutura de tópicos e, no **Inspetor de associação** , selecione:

    [![Selecionando o Inspetor de associação](databinding-images/outline07.png "Selecionando o Inspetor de associação")](databinding-images/outline07-large.png#lightbox)
2. Sob o **tópico exibir conteúdo** Turndown, selecione **associar a** e **controlador de árvore**. Insira `arrangedObjects` para o campo **chave do controlador** :

    ![Definindo a chave do controlador](databinding-images/outline08.png "Definindo a chave do controlador")
3. Selecione a **célula exibição de tabela** na coluna **funcionário** . No **Inspetor de associações** sob o **valor** Turndown, selecione **a exibição associar à** **célula da tabela**. Insira `objectValue.Name` para o **caminho de chave do modelo**:

    [![Inserindo o caminho de chave do modelo](databinding-images/outline09.png "Inserindo o caminho de chave do modelo")](databinding-images/outline09-large.png#lightbox)
4. `objectValue`é o atual `PersonModel` na matriz que está sendo gerenciada pelo controlador de árvore.
5. Selecione a **célula exibição de tabela** na coluna **ocupação** . No **Inspetor de associações** sob o **valor** Turndown, selecione **a exibição associar à** **célula da tabela**. Insira `objectValue.Occupation` para o **caminho de chave do modelo**:

    [![Inserindo o caminho de chave do modelo](databinding-images/outline10.png "Inserindo o caminho de chave do modelo")](databinding-images/outline10-large.png#lightbox)
6. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Se executarmos o aplicativo, o contorno será preenchido com nossa matriz de `PersonModels` :

[![Executando o aplicativo](databinding-images/outline11.png "Executando o aplicativo")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Associação de dados de exibição de coleção

A associação de dados com uma exibição de coleção é muito semelhante à associação com uma exibição de tabela, uma vez que um controlador de matriz é usado para fornecer dados para a coleção. Como a exibição de coleção não tem um formato de exibição predefinido, mais trabalho é necessário para fornecer comentários de interação do usuário e para rastrear a seleção do usuário.

> [!IMPORTANT]
> Devido a um problema no Xcode 7 e no macOS 10,11 (e superior), as exibições de coleção não podem ser usadas dentro de um arquivo de storyboard (. Storyboard). Como resultado, você precisará continuar a usar arquivos. xib para definir suas exibições de coleção para seus aplicativos Xamarin. Mac. Consulte a documentação de [exibições de coleção](~/mac/user-interface/collection-view.md) para obter mais informações.

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`:

![Add a View Controller](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![Create the CollectionView](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1. **Collection View Item** -  That manages a single instance of an item in the collection.
2. **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![Add controls](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![NSBox](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![Array Controller](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![Attribute inspector](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![Set the class and key names](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![Bindings inspector](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![Binding inspector](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![Contents](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![Selection indexes](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![Binding inspector values](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![Label value](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![Label value](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![NSBox value](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![Populated table](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Depurando falhas nativas

Cometer um erro em suas associações de dados pode resultar em uma _falha nativa_ no código não gerenciado e fazer com que seu aplicativo Xamarin. Mac falhe completamente com um `SIGABRT` erro:

[![Exemplo de uma caixa de diálogo de falha nativa](databinding-images/debug01.png "Exemplo de uma caixa de diálogo de falha nativa")](databinding-images/debug01-large.png#lightbox)

Normalmente, há quatro causas principais para falhas nativas durante a vinculação de dados:

1. Seu modelo de dados não herda de `NSObject` ou de uma subclasse de `NSObject` .
2. Você não expôs sua propriedade para Objective-C usando o `[Export("key-name")]` atributo.
3. Você não encapsula as alterações no valor do acessador `WillChangeValue` e nas `DidChangeValue` chamadas de método (especificando a mesma chave que o `Export` atributo).
4. Você tem uma chave incorreta ou digitada incorretamente no **Inspetor de associação** no interface Builder.

### <a name="decoding-a-crash"></a>Decodificando uma falha

Vamos causar uma falha nativa em nossa vinculação de dados para que possamos mostrar como localizá-la e corrigi-la. Em Interface Builder, vamos alterar nossa associação do primeiro rótulo no exemplo de exibição de coleção de `Name` para `Title` :

[![Editando a chave de associação](databinding-images/debug02.png "Editando a chave de associação")](databinding-images/debug02-large.png#lightbox)

Vamos salvar a alteração, voltar para Visual Studio para Mac para sincronizar com o Xcode e executar nosso aplicativo. Quando o modo de exibição de coleção for exibido, o aplicativo falhará momentaneamente com um `SIGABRT` erro (conforme mostrado na **saída do aplicativo** no Visual Studio para Mac), já que o `PersonModel` não expõe uma propriedade com a chave `Title` :

[![Exemplo de um erro de associação](databinding-images/debug03.png "Exemplo de um erro de associação")](databinding-images/debug03-large.png#lightbox)

Se rolarmos para a parte superior do erro na saída do **aplicativo** , podemos ver a chave para resolver o problema:

[![Encontrando o problema no log de erros](databinding-images/debug04.png "Encontrando o problema no log de erros")](databinding-images/debug04-large.png#lightbox)

Essa linha está informando que a chave `Title` não existe no objeto ao qual estamos ligando. Se alterarmos a ligação de volta para `Name` em interface Builder, salvar, sincronizar, recompilar e executar, o aplicativo será executado conforme o esperado sem problemas.

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com vinculação de dados e codificação de valor de chave em um aplicativo Xamarin. Mac. Primeiro, ele analisou a exposição de uma classe C# para Objective-C usando KVC (codificação de valor chave) e KVO (key-value Observation). Em seguida, ele mostrou como usar uma classe compatível com KVO e vincular dados a elementos da interface do usuário no Interface Builder do Xcode. Por fim, ele mostrou uma ligação de dados complexa usando **controladores de matriz** e de **árvore**.

## <a name="related-links"></a>Links Relacionados

- [Storyboard MacDatabinding (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding XIBs (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-xibs)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controles padrão](~/mac/user-interface/standard-controls.md)
- [Exibições de tabela](~/mac/user-interface/table-view.md)
- [Exibições de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Exibições de coleção](~/mac/user-interface/collection-view.md)
- [Guia de programação de codificação de valor-chave](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introdução ao guia de programação de observação de valor-chave](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introdução aos tópicos de programação de associações do Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introdução à referência de associações do Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
